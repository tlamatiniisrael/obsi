
# Part 2: Building a Modern Network Layer in Swift with Async/Await

![](https://miro.medium.com/v2/resize:fit:1400/1*rLNB27Taz8mbtISTynsKqQ.png)

Building a Modern Network Layer in Swift with Async/Await

In [**Part 1**](https://medium.com/@alokupadhyay1192/building-a-modern-network-layer-in-swift-with-combine-part-1-44ea76c9d78e), we built a reusable network layer using **Combine**.

Now, let’s see how we can design the **same flexible and production-ready network layer** using **Swift Concurrency (async/await)**.

Async/await is simpler to read, easier to debug, and has native support in modern Swift projects (iOS 15+).

👉 _“You can read the_ [**_full article here_**](https://medium.com/@alokupadhyay1192/part-2-building-a-modern-network-layer-in-swift-with-async-await-fdb6d65fa21f?sk=e251d869eb18e9cd6e45f386909aab01) _(no membership needed):_

### 🔥 Common Problems in Networking

Just like before, a good network layer must solve:

- **Avoid Repetition**: Don’t write baseURL, headers, or JSONEncoder() everywhere.
- **Error Handling**: Unified way to handle client, server, and decoding errors.
- **Request Cancellation**: Users may leave the screen before an API finishes.
- **Caching**: Avoid refetching unchanged data.
- **Flexibility**: Should support GET/POST/PUT/DELETE, query params, body payloads.

### ⚡ Step 1: API Config

```swift
struct APIConfig {  
    static var baseURL: URL = URL(string: "https://api.myapp.com")!  
    static var defaultHeaders: [String: String] = [  
        "Accept": "application/json",  
        "Content-Type": "application/json"  
    ]  
}
```

### ⚡ Step 2: HTTP Method

```swift
enum HTTPMethod: String {  
    case GET, POST, PUT, DELETE  
}
```

### ⚡ Step 3: Endpoint Protocol

Instead of forcing Data for body, let’s allow dictionary or Encodable.

```swift
public protocol Endpoint {  
    var baseURL: URL { get }  
    var path: String { get }  
    var method: HTTPMethod { get }  
    var headers: [String: String]? { get }  
    var queryItems: [URLQueryItem]? { get }  
    var bodyParameters: [String: Any]? { get }  
    var encodableBody: Encodable? { get }  
}  
  
  
// MARK: - Defaults  
extension Endpoint {  
    var baseURL: URL { APIConfig.baseURL }  
    var headers: [String: String]? { APIConfig.defaultHeaders }  
    var queryItems: [URLQueryItem]? { nil }  
    var bodyParameters: [String: Any]? { nil }  
    var encodableBody: Encodable? { nil }  
}
```
### ⚡ Step 4: Request Builder

We centralize encoding and request creation here.

```swift
struct RequestBuilder {  
    static func buildRequest(from endpoint: Endpoint) throws -> URLRequest {  
        var url = endpoint.baseURL.appendingPathComponent(endpoint.path)  
          
        if let queryItems = endpoint.queryItems {  
            var components = URLComponents(url: url, resolvingAgainstBaseURL: false)  
            components?.queryItems = queryItems  
            if let newURL = components?.url {  
                url = newURL  
            }  
        }  
          
        var request = URLRequest(url: url)  
        request.httpMethod = endpoint.method.rawValue  
        request.allHTTPHeaderFields = endpoint.headers  
          
        if let encodable = endpoint.encodableBody {  
            request.httpBody = try? JSONEncoder().encode(AnyEncodable(encodable))  
        } else if let params = endpoint.bodyParameters {  
            request.httpBody = try? JSONSerialization.data(withJSONObject: params, options: [])  
        }  
          
        return request  
    }  
}  
  
// Wrapper for any Encodable  
struct AnyEncodable: Encodable {  
    private let encodeFunc: (Encoder) throws -> Void  
    init<T: Encodable>(_ value: T) {  
        self.encodeFunc = value.encode  
    }  
    func encode(to encoder: Encoder) throws { try encodeFunc(encoder) }  
}
```

### ⚡ Step 5: Network Error

```swift
enum NetworkError: Error {  
    case invalidRequest  
    case invalidResponse  
    case serverError(Int)  
    case decodingError(Error)  
    case other(Error)  
}
```

### ⚡ Step 6: Network Manager with Async/Await

Supports **caching** + **cancellation**.

```swift
actor NetworkManager {  
    static let shared = NetworkManager()  
      
    private let urlSession: URLSession  
    private var cache: [String: Data] = [:]   // Simple in-memory cache  
      
    private init() {  
        let config = URLSessionConfiguration.default  
        config.timeoutIntervalForRequest = 30  
        self.urlSession = URLSession(configuration: config)  
    }  
      
    func request<T: Decodable>(_ endpoint: Endpoint,  
                               useCache: Bool = false,  
                               type: T.Type) async throws -> T {  
          
        let request = try RequestBuilder.buildRequest(from: endpoint)  
        let cacheKey = request.cacheKey  
          
        // 🔹 Return from cache  
        if useCache, let cachedData = cache[cacheKey] {  
            return try JSONDecoder().decode(T.self, from: cachedData)  
        }  
          
        do {  
            let (data, response) = try await urlSession.data(for: request)  
              
            guard let httpResponse = response as? HTTPURLResponse else {  
                throw NetworkError.invalidResponse  
            }  
              
            guard 200..<300 ~= httpResponse.statusCode else {  
                throw NetworkError.serverError(httpResponse.statusCode)  
            }  
              
            // 🔹 Save in cache  
            cache[cacheKey] = data  
              
            return try JSONDecoder().decode(T.self, from: data)  
        } catch let decodingError as DecodingError {  
            throw NetworkError.decodingError(decodingError)  
        } catch {  
            throw NetworkError.other(error)  
        }  
    }  
}  
  
// 🔹 Helper: Create cache key from request  
private extension URLRequest {  
    var cacheKey: String {  
        "\(httpMethod ?? "GET"):\(url?.absoluteString ?? ""):\(httpBody?.hashValue ?? 0)"  
    }  
}
```

### ⚡ Step 7: Cancel a Request

With async/await, **cancellation** is baked in.

Just store your Task and cancel when needed.

```swift
var currentTask: Task<Void, Never>?  
  
func fetchUser() {  
    currentTask = Task {  
        do {  
            let user: User = try await NetworkManager.shared.request(GetUserEndpoint(), type: User.self)  
            print("User: \(user)")  
        } catch {  
            print("Error: \(error)")  
        }  
    }  
}  
  
// Cancel when user leaves screen  
func cancelFetch() {  
    currentTask?.cancel()  
}
```

### ⚡ Step 8: Example Usage

1. **Define Endpoint**

```swift
struct GetUserEndpoint: Endpoint {  
    var path: String { "/users/me" }  
    var method: HTTPMethod { .GET }  
}  
  
struct CreatePostEndpoint: Endpoint {  
    var path: String { "/posts" }  
    var method: HTTPMethod { .POST }  
    var bodyParameters: [String : Any]? {  
        ["title": "Hello", "content": "World"]  
    }  
}
```

**2. Call API**

```swift
Task {  
    do {  
        let user: User = try await NetworkManager.shared.request(GetUserEndpoint(),  
                                                                 useCache: true,  
                                                                 type: User.self)  
        print("Fetched User:", user)  
    } catch {  
        print("Error:", error)  
    }  
}
```

### ⚡ Step 9: Retry Mechanism with Exponential Backoff

We’ll add a helper inside NetworkManager that retries failed requests up to maxRetries times, with a growing delay between attempts (e.g., 1s → 2s → 4s).

```swift
extension NetworkManager {  
    func requestWithRetry<T: Decodable>(  
        _ endpoint: Endpoint,  
        type: T.Type,  
        maxRetries: Int = 3,  
        useCache: Bool = false  
    ) async throws -> T {  
          
        var attempt = 0  
        var lastError: Error?  
          
        while attempt <= maxRetries {  
            do {  
                return try await request(endpoint, useCache: useCache, type: type)  
            } catch {  
                lastError = error  
                attempt += 1  
                  
                // Only retry for network/server errors  
                if case NetworkError.serverError = error {  
                    let delay = pow(2.0, Double(attempt)) // exponential backoff: 2^n  
                    try? await Task.sleep(nanoseconds: UInt64(delay * 1_000_000_000))  
                } else {  
                    throw error  
                }  
            }  
        }  
          
        throw lastError ?? NetworkError.other(NSError(domain: "Unknown", code: -1))  
    }  
}
```

### ⚡ Example Usage with Retry

```swif
Task {  
    do {  
        let user: User = try await NetworkManager.shared.requestWithRetry(  
            GetUserEndpoint(),  
            type: User.self,  
            maxRetries: 3,  
            useCache: true  
        )  
        print("Fetched user:", user)  
    } catch {  
        print("Final error after retries:", error)  
    }  
}
```

Here:

- If API fails with a **server error** (e.g., 500), it retries up to 3 times.
- Delay grows: 1s → 2s → 4s.
- Other errors (decoding, invalid request) are **not retried**.

### ⚡ Part 2 Conclusion: Combine vs Async/Await

Both approaches have their strengths. Let’s compare them across **readability, performance, and usability**.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:2000/1*I6NajHc_jmACxOrazX6kRw.png)

Combine vs Async/Await

### ✅ Key Takeaways

1. **Combine** is great if you’re already using it heavily for **reactive streams**, chaining multiple publishers, or integrating with legacy code.
2. **Async/Await** is simpler, more readable, and ideal for most modern Swift projects — especially when dealing with sequential network calls or structured concurrency.
3. Both approaches can implement **caching, retries, and cancellation**, but async/await often results in **cleaner, easier-to-maintain code**.

By building the network layer in **both styles**, you can choose the approach that best fits your project while keeping:

- ✅ Centralized error handling
- ✅ Default configuration
- ✅ Flexible request bodies
- ✅ Caching
- ✅ Retry & exponential backoff
- ✅ Cancellation

This gives you a **production-ready, reusable, and scalable network layer** suitable for any Swift/iOS project.

## A message from our Founder

**Hey,** [**Sunil**](https://linkedin.com/in/sunilsandhu) **here.** I wanted to take a moment to thank you for reading until the end and for being a part of this community.

Did you know that our team run these publications as a volunteer effort to over 3.5m monthly readers? **We don’t receive any funding, we do this to support the community. ❤️**

If you want to show some love, please take a moment to **follow me on** [**LinkedIn**](https://linkedin.com/in/sunilsandhu)**,** [**TikTok**](https://tiktok.com/@messyfounder), [**Instagram**](https://instagram.com/sunilsandhu). You can also subscribe to our [**weekly newsletter**](https://newsletter.plainenglish.io/).

And before you go, don’t forget to **clap** and **follow** the writer️!