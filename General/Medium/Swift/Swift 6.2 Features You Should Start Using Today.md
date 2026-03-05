
![](https://miro.medium.com/v2/resize:fit:1400/0*s2iqdM_RfyUgvWrr)

Image Credit — [fatbobman.com](https://fatbobman.com/)

Member-only story

Featured

Today, we’re going to discuss **Swift 6.2**, which was introduced at **WWDC25** and that you should begin using today.

[

## Writing High-Performance Swift Code

### Tips and Tricks for writing high-performance Swift code.

nameisjayant2.medium.com



](https://nameisjayant2.medium.com/writing-high-performance-swift-code-b210659717f0?source=post_page-----4f3bc7b3f91c---------------------------------------)

## 1. Swift in VSCode

Yes, it’s true. With the release of **Swift 6.2**, we can now write [**Swift code in VSCode.**](https://www.swift.org/documentation/articles/getting-started-with-vscode-swift.html)

> Now we don’t need to open the heavy **Xcode** to practice Swift coding.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*8E2-Bqpj7SIwtc1OkapzSw.png)

Screenshot from the VSCode

I have already written an article on running **Swift in VSCode**; please take a look.

[

## How to Install and Run Swift in VSCode

### Learn how to install and run Swift in VSCode

medium.com



](https://medium.com/ios-lab/how-to-install-and-run-swift-in-vscode-100d428cd73f?source=post_page-----4f3bc7b3f91c---------------------------------------)

## 2. Swift Toolchains

With **Swift 6.2**, [**Swiftly**](https://www.swift.org/blog/introducing-swiftly_10/) introduces [**Swift Toolchains**](https://www.swift.org/swiftly/documentation/swiftly/use-toolchains/) that include **compilers**, **linkers**, **debuggers**, **documentation generators**, and other useful tools for working with Swift.

**In simple terms:**

- The **Swift toolchain** is used to manage multiple Swift versions in a system, which helps to compile and run Swift code.
- Suppose you want to test a feature of **Swift 5**, with the help of the **toolchain**, you can switch to Swift 5 and start testing that feature.

Using the command below, you can install the latest **Swift toolchain.**

swiftly install latest

If you want to download a specific version, use the command below.

swiftly install 5.10.1

To check the **Swift version,** use the command below.

swift --version

When you have multiple toolchains installed and want to use a specific version, use the command below.

swiftly use 5.10.1  
or   
swiftly use 5

Use the command below to check which toolchain is currently in use.

swiftly use

The command below prints the current toolchain location.

swiftly use --print-location

## 3. Default Value in String Interpolations

Before **Swift 6.2**, we had to write like this:

let name: String? = nil  
print("Hello, \(name ?? "Anonymous")!")

Here, we need to use the **nil coalescing operator** to provide a fallback value when the name is null.

With **Swift 6.2+**, we have gotten something different:

let name: String? = nil  
print("Hello, \(name, default : "Anonymous")!")

Instead of using the **nil coalescing operator**, we can use a **default parameter** in the **string interpolation** to provide an optional value.

## 4. Raw identifiers

With **Swift 6.2+**, we can now use **backticks** with **functions**, **variables**, and **enums** like this:

func `this is a raw function`() {  
    print("Hello, Raw Function!")  
}  
  
// function calling  
 `this is a raw function`()  
  
// output  
Hello, Raw Function!

This is a cool feature; now we can write functions in a descriptive way.

This feature is more useful in **Swift testing** because, previously, we used to write the Swift test like this:

import Testing  
  
@Test("Check email is value or not")  
func isEmailValid() {  
    // test code  
}

Here, we write the function **name** and its **description**. Now, with the introduction of raw identifiers in Swift 6.2, we can write like this:

@Test  
func `Check email is valid or not`() {  
    // test code  
}

Now, the function name says it all; no need to write the description.

## 5. Method and Initializer Key Paths

We all know, Swift key path supports **properties** and **subscripts** like this:

let strings = ["Hello", "world"]  
let capitalized = strings.map(\.capitalized)

This will capitalize the list items, which is fine since we already know.

But with **Swift 6.2**, we can also **access the method in the key path** like this.

let strings = ["Hello", "world"]  
let uppercased = strings.map(\.uppercased())

The `\.uppercased()` method will transform the list items into uppercase letters.

## 6. Add Collection conformances for enumerated()

Now, with **Swift 6.2**, the type returned by **enumerated** conforms to **Collection**.

We can easily use **enumerated()** with **SwiftUI List** and **ForEach**.

import SwiftUI  
  
struct ContentView: View {  
    var names = ["Apple", "Orange", "Mango"]  
  
    var body: some View {  
        List(names.enumerated(), id: \.offset) { values in  
            Text("Fruit \(values.offset + 1): \(values.element)")  
        }  
    }  
}

## 7. weak let

In Swift, **'weak'** is used to **prevent retain cycles** by not increasing an object's reference count.

Before **Swift 6.2**, **'weak' was only used with 'var'** and didn’t support 'let'. But with 6.2, we can use **'weak' with 'let'** as well.

> When declaring a variable with a **weak let**, it means we cannot change the property created with let, but it will be destroyed once the original one is no longer available.

Let’s see an example:

final class User: Sendable {  
    let id = UUID()  
}  
  
final class Admin: Sendable {  
    weak let user: User?  
  
    init(user: User?) {  
        self.user = user  
    }  
}  
  
var user: User? = User()  
let admin = Admin(user: user)  
print(session.user?.id ?? "No User ID")

Here, the user property has a **weak reference to the Admin class,** so if we try to destroy the original User’s object, the property will be destroyed too.

user = nil  
print(session.user?.id ?? "No User ID")

As you can see in the code above, we used **Sendable** with both classes (User and Admin); that’s only possible with **'weak let',** not with **'weak var'.**

## Conclusion

These are the features I liked most about **Swift 6.2.** Let me know which feature you liked the most, or if you have any suggestions or questions.

_Thank you for reading until the end. Before you go:_

- _Please consider_ **_clapping_** _and_ **_following_** _👏_
- _Follow us on_ [**_Twitter(X)_**](https://x.com/nameisjayant)_,_ [**_LinkedIn_**](https://www.linkedin.com/in/jayant-kumar-%F0%9F%87%AE%F0%9F%87%B3-262597171/)_,_ [**_Substack_**](https://nameisjayant.substack.com/), _and_ [**_YouTube_**](https://www.youtube.com/@ProgrammingSimplified0)**_._**