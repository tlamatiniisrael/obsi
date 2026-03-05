
![](https://miro.medium.com/v2/resize:fit:1400/1*hp-QzwlVNwgnkwKz1V9oEQ.png)

Building a Modern Network Layer in Swift with Combine

Networking is the backbone of any mobile application. But if not designed carefully, network code quickly turns into a mess of duplicated logic, scattered error handling, and boilerplate request setup.

In this 2-part series, we’ll build a **scalable, reusable, and production-ready network layer** in Swift.

- **Part 1 (this article):** Build a Combine-based network layer with defaults, retries, cancellation, and caching.
- [**Part 2**](https://medium.com/@alokupadhyay1192/part-2-building-a-modern-network-layer-in-swift-with-async-await-fdb6d65fa21f)**:** Rebuild the same layer using Swift’s modern async/await.

By the end, you’ll have a **plug-and-play module** you can drop into any project.

👉 _“You can read the_ [**_full article here_**](https://medium.com/@alokupadhyay1192/building-a-modern-network-layer-in-swift-with-combine-part-1-44ea76c9d78e?sk=0e818baf738d75d5f4a36aea312c3d1b) _(no membership needed):_

### ❌ The Problems with Naive Networking

Most projects start with something like this:

```swift
let url = URL(string: "https://api.myapp.com/users")!  
var request = URLRequest(url: url)  
request.httpMethod = "GET"  
request.addValue("application/json", forHTTPHeaderField: "Content-Type")  
  
URLSession.shared.dataTask(with: request) { data, response, error in  
    // decode manually, handle error manually...  
}.resume()
```

Looks fine, right? But scale it to a real app:

- You repeat **base URL, headers** for every API.
- Error handling is **scattered and inconsistent**.
- Retrying failed requests? Manually handled each time.
- Cancelling requests? Painful.
- Caching responses? Rarely implemented.

👉 We need a **generic, extensible network layer** that handles all this centrally.

### ✅ Step 1: Global API Config

Let’s centralize values that don’t change often — like base URL and default headers.

```swift
struct APIConfig {  
    static var baseURL: URL = URL(string: "https://api.myapp.com")!  
    static var defaultHeaders: [String: String] = [  
        "Accept": "application/json",  
        "Content-Type": "application/json"  
    ]  
}
```

Now, if you need to switch environments (dev/staging/prod), just update APIConfig.

### ✅ Step 2: Endpoint Protocol

Every API endpoint will conform to a common Endpoint protocol.

```swift
public protocol Endpoint {  
    var baseURL: URL { get }  
    var path: String { get }  
    var method: HTTPMethod { get }  
    var headers: [String: String]? { get }  
    var queryItems: [URLQueryItem]? { get }  
      
    /// Instead of raw Data, keep it clean:  
    var bodyParameters: [String: Any]? { get }  
    var encodableBody: Encodable? { get }  
}
```

### Defaults

Most APIs don’t need to override everything:

```swift
extension Endpoint {  
    var baseURL: URL { APIConfig.baseURL }  
    var headers: [String: String]? { APIConfig.defaultHeaders }  
    var queryItems: [URLQueryItem]? { nil }  
    var bodyParameters: [String: Any]? { nil }  
    var encodableBody: Encodable? { nil }  
}
```

### ✅ Step 3: HTTP Method Enum

A simple enum keeps methods type-safe:

```swift
public enum HTTPMethod: String {  
    case GET, POST, PUT, PATCH, DELETE  
}
```

### ✅ Step 4: Request Builder

The builder converts an Endpoint into a URLRequest.

It also handles encoding body automatically (dictionary or Encodable).

```swift
struct RequestBuilder {  
    static func build(from endpoint: Endpoint) throws -> URLRequest {  
        var url = endpoint.baseURL.appendingPathComponent(endpoint.path)  
          
        // Add query items  
        if let queryItems = endpoint.queryItems,  
           var components = URLComponents(url: url, resolvingAgainstBaseURL: false) {  
            components.queryItems = queryItems  
            url = components.url ?? url  
        }  
          
        var request = URLRequest(url: url)  
        request.httpMethod = endpoint.method.rawValue  
          
        // Headers  
        endpoint.headers?.forEach { request.addValue($1, forHTTPHeaderField: $0) }  
          
        // Body (Encodable or Dictionary)  
        if let encodable = endpoint.encodableBody {  
            request.httpBody = try? JSONEncoder().encode(AnyEncodable(encodable))  
        } else if let params = endpoint.bodyParameters {  
            request.httpBody = try? JSONSerialization.data(withJSONObject: params, options: [])  
        }  
          
        return request  
    }  
}
```

Helper wrapper for Encodable:

```swift
struct AnyEncodable: Encodable {  
    private let encodeFunc: (Encoder) throws -> Void  
      
    init<T: Encodable>(_ value: T) {  
        self.encodeFunc = value.encode  
    }  
      
    func encode(to encoder: Encoder) throws {  
        try encodeFunc(encoder)  
    }  
}
```

### ✅ Step 5: Network Error Handling

Centralize errors:

```swift
enum NetworkError: Error {  
    case invalidRequest  
    case invalidResponse  
    case decodingFailed  
    case serverError(Int)  
    case unknown(Error)  
}
```

### ✅ Step 6: Network Client with Combine

Here’s where the magic happens:

```swift
import Combine  
  
protocol NetworkClient {  
    func request<T: Decodable>(_ endpoint: Endpoint, type: T.Type) -> AnyPublisher<T, NetworkError>  
    func cancelAll()  
}  
  
final class CombineNetworkClient: NetworkClient {  
    private var cancellables = Set<AnyCancellable>()  
    private let cache = NSCache<NSURL, NSData>()   // 👈 Simple caching  
      
    func request<T: Decodable>(_ endpoint: Endpoint, type: T.Type) -> AnyPublisher<T, NetworkError> {  
        do {  
            let request = try RequestBuilder.build(from: endpoint)  
              
            // Caching layer  
            if let cachedData = cache.object(forKey: request.url! as NSURL) {  
                return Just(cachedData as Data)  
                    .decode(type: T.self, decoder: JSONDecoder())  
                    .mapError { _ in NetworkError.decodingFailed }  
                    .eraseToAnyPublisher()  
            }  
              
            return URLSession.shared.dataTaskPublisher(for: request)  
                .tryMap { result -> Data in  
                    guard let httpResponse = result.response as? HTTPURLResponse else {  
                        throw NetworkError.invalidResponse  
                    }  
                    guard 200..<300 ~= httpResponse.statusCode else {  
                        throw NetworkError.serverError(httpResponse.statusCode)  
                    }  
                    // Cache response  
                    self.cache.setObject(result.data as NSData, forKey: request.url! as NSURL)  
                    return result.data  
                }  
                .decode(type: T.self, decoder: JSONDecoder())  
                .mapError { error -> NetworkError in  
                    if let netError = error as? NetworkError { return netError }  
                    return .unknown(error)  
                }  
                .retry(2) // 👈 automatic retries  
                .eraseToAnyPublisher()  
              
        } catch {  
            return Fail(error: NetworkError.invalidRequest).eraseToAnyPublisher()  
        }  
    }  
      
    func cancelAll() {  
        cancellables.removeAll()  
    }  
}
```

### ✅ Step 7: Example Usage

Define an endpoint:

```swift
struct GetUserEndpoint: Endpoint {  
    var path: String { "/users/me" }  
    var method: HTTPMethod { .GET }  
}
```

Use the client:

```swift
let client = CombineNetworkClient()  
let cancellable = client.request(GetUserEndpoint(), type: User.self)  
    .sink(receiveCompletion: { completion in  
        if case .failure(let error) = completion {  
            print("Error: \(error)")  
        }  
    }, receiveValue: { user in  
        print("User: \(user)")  
    })
```

If you want to cancel:

```swift
cancellable.cancel()
```

### 🎯 Recap

In this part, we built a **Combine-based network layer** with:

- Centralized **APIConfig**
- Flexible Endpoint with defaults
- Automatic **body encoding** (dictionary or Encodable)
- **Caching** with NSCache
- Centralized **error handling**
- **Retries**
- **Cancellation**

👉 In [**Part 2**](https://medium.com/@alokupadhyay1192/part-2-building-a-modern-network-layer-in-swift-with-async-await-fdb6d65fa21f), we’ll rebuild this same layer using async/await, making it even more concise and SwiftUI-friendly.

## A message from our Founder

**Hey,** [**Sunil**](https://linkedin.com/in/sunilsandhu) **here.** I wanted to take a moment to thank you for reading until the end and for being a part of this community.

Did you know that our team run these publications as a volunteer effort to over 3.5m monthly readers? **We don’t receive any funding, we do this to support the community. ❤️**

If you want to show some love, please take a moment to **follow me on** [**LinkedIn**](https://linkedin.com/in/sunilsandhu)**,** [**TikTok**](https://tiktok.com/@messyfounder), [**Instagram**](https://instagram.com/sunilsandhu). You can also subscribe to our [**weekly newsletter**](https://newsletter.plainenglish.io/).

And before you go, don’t forget to **clap** and **follow** the writer️!