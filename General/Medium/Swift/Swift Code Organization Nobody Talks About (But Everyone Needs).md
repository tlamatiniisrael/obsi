
![](https://miro.medium.com/v2/resize:fit:1400/0*gSAFE-nDO9G61LNY)

Photo by [Abubakar Isa](https://unsplash.com/@ayimages?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

In this article, we will talk about the Swift code organization that will help you write and organize your code better.

[

## You Won’t Believe These Swift Syntax Tricks Actually Exist

### These Swift Syntax tricks will blow your mind.

medium.com



](https://medium.com/ios-lab/you-wont-believe-these-swift-syntax-tricks-actually-exist-4229b140b08d?source=post_page-----1fb25ce588bc---------------------------------------)

Let’s get started!

## 1. Access to shared resources using type subscripts

In Swift, accessing shared resources or data seamlessly across application components is a common requirement.

Most developers rely on the **singleton pattern**, **static methods**, and **global variables** to manage shared access.

But in Swift, we can consider using `subscripts`, which allows us to access the shared resources directly through the **type’s interface.**

It means we don’t need to **create an object to access shared resources.**

Suppose we have an application that manages a set of configurations, such as theme and language preference.

Rather than relying on the singleton, we will use the `subscripts` to access the shared resource.

```swift
struct AppConfiguration {  
    private static var settings: [String: String] = [  
        "Theme": "Light", "Language": "Hindi"  
    ]  
      
    ==static== ==subscript====(====key====:== ==String====) ->== ==String====? {==        ==return== ==settings[key]  
    }==  
}  
  
==if== ==let== ==theme== ===== ==AppConfiguration====[===="Theme"====] {==    ==print====(===="The current theme is== ==\(theme)====."====)  
}==
```

As you can see above, settings is a static context that holds the settings data.

The **subscript** function provides access to the dictionary, enabling easy retrieval of settings by key.

## 2. Organize related functionalities using enums as namespaces

If you have any functionality that is similar and related to each other, such as **constants** or **utility functions**, then using **enums as a namespace** is the perfect approach.

Let’s understand with an example;

```swift 
enum MathConstants {  
    static let pi = 3.14159  
    static let e = 2.71828  
}  
  
enum UtilityFunctions {  
    static func computeArea(radius: Double) -> Double {  
        return MathConstants.pi * radius * radius  
    }  
}  
  
let area = UtilityFunctions.computeArea(radius: 5)
```

As you can see, `MathConstants` and `UtilityFunctions` act as a namespace. **MathConstants** groups all mathematical constants, and **UtilityFunctions** provides a logical grouping for utility functions.

> This way, we can use common logics across the app.

## 3. Put generic declarations in the same module to enable specialization

As we know, Swift generics provide a flexible way to write reusable code, but this flexibility comes with some runtime costs when generics are not **specialized**.

Without **specialization**, generic functions operate on a **box representation** and rely on the **witness table** to handle type-specific behavior, which may affect performance and code optimization.

Now, if we want to enable **specialization**, the generic code must be in the same module as its use.

```swift
class Store<T> {  
    var items: [T] = []  
  
    func add(_ item: T) {  
        items.append(item)  
    }  
}  
  
func sum<T: BinaryInteger>(_ values: [T]) -> T {  
    values.reduce(0, +)  
}  
  
// The compiler can specialize Store<Int>  
let intStore = Store<Int>()  
intStore.add(10)  
intStore.add(20)  
  
// The compiler can specialize sum() for [Int]  
let result = sum([10, 20, 30])
```

In the above code, both `Store<Int>` and `sum(_:`) with `[Int]` can be specialized by the compiler, because their definition are in the same file.

## 4. Draw attention to code that needs review or modification

Swift offers various directives to enhance code quality and readability. One such directive is `#warning`, a powerful tool for developers aiming to keep their codebase clean.

The `#warning` directive serves as a reminder or a flag within our code. It's a way to generate warnings during the compilation process, intentionally drawing attention to pieces of code that need **review or modification.** Unlike errors that prevent code from compiling, warnings don't stop the build process but signify something noteworthy or potentially problematic.

Implementing `#warning` is straightforward. We can simply include it in our code followed by a custom message.

Consider that we are working on a Swift project involving an API. We’ve set up a function to handle API requests, but haven’t implemented caching yet. We could use `#warning` to remind ourselves or inform our team about this pending enhancement.

```swift
func fetchUserData(from url: URL) {  
    #warning("TODO: Implement caching to optimize network calls")  
    URLSession.shared.dataTask(with: url) { data, response, error in  
        // Handle response and error  
    }.resume()  
}
```

In this scenario, the `#warning` directive clearly indicates that while the function for fetching user data is operational, it lacks a caching mechanism. This reminder ensures the enhancement is not forgotten during development.

_Thank you for reading until the end. Before you go:_

- _Please consider_ **_clapping_** _and_ **_following_** _👏_
- _Follow us on_ [**_Twitter(X)_**](https://x.com/nameisjayant)_,_ [**_LinkedIn_**](https://www.linkedin.com/in/jayant-kumar-%F0%9F%87%AE%F0%9F%87%B3-262597171/)_,_ [**_Substack_**](https://nameisjayant.substack.com/), _and_ [**_YouTube_**](https://www.youtube.com/@ProgrammingSimplified0)**_._**