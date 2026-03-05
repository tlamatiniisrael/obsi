
This article is all about tips and tricks for **writing high-performance Swift** **code**. Some of the tips will help improve the quality of Swift code, making it less error-prone and more readable.

> **Note:**
> 
> I will keep this article as simple as possible so that any non-English readers can understand it better.
> 
> Please read this article slowly, as some topics might look a little complicated to understand, but if you read thoroughly, I am pretty sure you will understand.

[

## 10 SwiftUI Modifiers of iOS 26+ You Probably Missed

### Not a Medium Member? “Read For Free”

medium.com



](https://medium.com/ios-lab/10-swiftui-modifiers-of-ios-26-you-probably-missed-3222f1193a1f?source=post_page-----b210659717f0---------------------------------------)

> Let’s get started

## 1. Reducing Dynamic Dispatch

As we all know, Swift is a **dynamic language** by default, like **Objective-C**. However, Swift allows programmers to **reduce dynamic behavior** to improve code performance.

> Yes, **dynamic language** takes little extra time because it mostly works at runtime.

### **What does dynamic language mean?**

> **Dynamic language** means that the important aspects of language are decided at runtime rather than at compile time.
> 
> In simple terms, any **variable**, **methods** and **behavior** of a program that change at runtime is called a **dynamic language.**

For example -

var x:Any = 10  
x = "Jayant"

==Here, variable x does not have a fixed type; at compile time, it is an== ==**integer**====, and at runtime, it is a== ==**string**====.==

### What is Dynamic Dispatch

Dynamic dispatch means that **method** and **property** execution occur at runtime.

Swift uses dynamic dispatch for

- `classes`
- `inheritance`
- `method overriding`
- `protocol with class constraints`
- `@objc methods`

Dynamic dispatch occurs when you use **classes**, **inheritance**, **method** **overriding**, etc.

Let’s understand this with an example:

class Animal {  
    func speak() { print("Animal sound") }  
}  
  
class Dog: Animal {  
    override func speak() { print("Dog barks") }  
}  
  
let pet: Animal = Dog() // object creation...  
pet.speak()

At object cre==a==tion, the variable is initialized to **Animal**, but the object is a **Dog (because Dog inherits from Animal)**.

> **Note** — The **Swift Compiler** must wait until the program has finished executing to determine whether to call the **speak method** with a **Dog** or an **Animal** object.

This is decided at runtime, which is why it is a **dynamic dispatch**.

### Another Example:

Suppose we have this class:

class Dog {  
    func bark() { print("Bark") }  
}  
  
let d = Dog()  
d.bark()

> When you run the code, Swift actually checks if the bark function is overridden somewhere or not; this will increase the extra lookup time.

### Does Dynamic Dispatch Affect Performance?

Yes, slightly — not usually something to worry about, but if you have a larger codebase where performance really matters, then you should be worried about it.

Swift uses **vtable** (virtual method table) for **dynamic dispatch.**

### Reducing Dynamic Dispatch

Yes, reducing dynamic dispatch can improve Swift code performance.

> **Static dispatch** is faster because it calls **methods** and **properties** at **compile time.**
> 
> **(Static Dispatch is a compile-time thing)**

Swift uses **static dispatch** by default for:

- `struct` methods
- `final` class methods
- `static` methods
- `private` functions (often)
- `internal`/`public`

It means that if the class declaration and its methods are not overridden, it’s best to make them **final**, since this prevents overriding and ensures **static dispatch.**

final class Animal {  
    func speak() { print("Animal sound") }  
}

You can also use **private** and **fileprivate** when the declaration doesn't need to be accessed outside the file.

These slight changes definitely improve the performance, especially in larger codebases.

## 2. Using Container Types Efficiently

The container types refer to **Array** and **Dictionary** — we will discuss how to use these types efficiently.

### Use value types in Array

Yes, Value types mean that each variable gets its own copy of data.

Examples of value types are:

- `struct`
- `enum`
- `Int`, `String`, `Bool`, `Double` (all structs)
- `Array`, `Set`, `Dictionary` (these are value types too)

var a = 5  
var b = a  
b = 10  
  
print(a) // 5  
print(b) // 10

> Here, we copied the **`a`** value into **`b`**, but changing `**b`** doesn’t change **`a`**.

### What does “use value types _in an Array_” mean?

Always prefer value types like **structs** in **arrays** over **classes**, because updating one item does not affect the others.

Let’s understand with an example:

struct User {  
    var name: String  
    var age: Int  
}  
  
var users: [User] = [  
    User(name: "A", age: 20),  
    User(name: "B", age: 25)  
]

The above is a **value type array** because we used a struct.

var userA = users[0]  
userA.age = 30 

- We copied **index 0** of the **user’s array** to the **userA** variable and updated its value. The good thing is that it does not modify the original array (users).
- Another thing is that when you copy an array, it doesn’t copy immediately; it copies only when you modify it (**copy-on-write)**.
- Value types are **copied** rather than **shared**, which is beneficial for concurrency.

> **Note** — Don’t use class in an array until it’s required.

### Use in-place mutation instead of object reassignment

This means, instead of creating a new object, modify the existing one.

Let’s understand with an example:

struct User {  
    var name: String  
    var age: Int  
}

**Reassignment**

var user = User(name: "A", age: 20)  
  
user = User(name: user.name, age: 21)  // new instance

Here, we are creating a new instance.

**In-place Mutation**

var user = User(name: "A", age: 20)  
  
user.age = 21 // modify the same instance

Here, we are simply modifying the existing one.

> **Note** — It’s better to mutate the object rather than create unnecessary objects.

## 3. Wrapping operations

The wrapping operation is a **swift arithmetic operation** that saves you from **overflow errors.**

**_Wrapping Arithmetic Operators Symbol:_**

- **&+** (Addition)
- **&-** (Subtraction)
- **&*** (Multiplication)

_Let’s understand with an example:_

Suppose we have a **UInt8** that can store the values from **0 to 255.**

let x: UInt8 = 255 // min - 0 & max - 255  
let y = x + 1  // overflow error

In the code above, if we try to perform a normal addition on a **UInt8**, it will overflow since its value is **255** (maximum value) and we are adding 1.

Let’s do the same things with **wrapping addition arithmetic:**

let x: UInt8 = 255  
let y = x &+ 1  
print(y) // 0

==Here, we will not encounter any overflow error; basically, when we try to add 1 to its maximum value, it wraps around to its minimum value, which is 0.==

**Wrapping Subtraction**

let x: UInt8 = 0  
let y = x &- 1  
print(y) // 255

Here, its value is 0 (the minimum), and when we subtract 1, it wraps around to its maximum of 255.

> **In simple terms, In wrapping arithmetic:**
> 
> When a number **exceeds its maximum limit**, it wraps around to its **minimum value.**
> 
> When a number **goes below its minimum**, it wraps around to the **maximum value.**

### Does wrapping integer arithmetic improve performance?

Yes, but it only:

- When you are performing heavy arithmetic operations (millions per second).
- In algorithms such as hashing and encryption.
- Calculation in the tight loops.

> **Note:**
> 
> It’s not beneficial to use, when you are building a small Swift application.
> 
> Also, use **Wrapping Arithmetic** only when you are sure that **overflow** cannot occur; otherwise, things could go wrong.

## 4. The Cost of let / var When Captured by Escaping Closures in Swift

_Let's first understand_ **_Escaping Closure._**

When a function runs after some delay, it is called an **escaping closure.**

func runAfter1Second(_ task: @escaping () -> Void) {  
    DispatchQueue.main.asyncAfter(deadline: .now() + 1) {  
        task()   // runs after 1 second  
    }  
}

In the code above, the **task()** closure executes after a second delay, so it is an **escaping closure.**

When we use any variable inside the **closure**, that is called **capturing a variable**, like this**:**

runAfter1Second {  
    print(count)   // captured  
    index += 1     // captured + mutated  
}

### The cost of var when captured by escaping closure

When a variable is declared with **var** and **captured by an escaping closure,** Swift allows mutation within the closure (we can change the variable's value because it is declared with var).

But since closure is stored in **heap memory** when it escapes, the captured variable is also stored in a reference cell on the **heap**. This process is called **variable boxing.**

var index = 0  
  
closure {  
    index += 1      // needs a box  
}

In the code above, **index** is declared with **var**, so when it is used inside the closure, it is stored in a small heap object (a box). The closure stores a reference to that box. When a mutation occurs inside the closure, it mutates the box reference rather than the original stack variable.

> This process is obviously more expensive, so it’s a significant cost when var is used inside the escaping closure.

### The cost of let when captured by escaping closure

When declared with let, its value will not change, and Swift often copies the value directly into the closure, which means that:

- No heap memory will be created to store the variable
- Creating variables with 'let' is very cheap.

> It’s always good to create a variable with 'let' when you have to use it inside the escaping closure.

### Pass var as an inout if closure not actually escaping

It means that if you are not using an escaping closure, pass the variable as **inout** to avoid performance overhead.

Let’s understand with an example:

func addOne(_ number: Int) {  
    number += 1 // compiler time error - Left side of mutating operator isn't mutable: 'number' is a 'let' constant  
}

Here, we are not using the escaping closure, and try to update the number, but it will give a compile-time error because the number is constant; we cannot update it directly.

To resolve this error, we use the **inout** keyword.

func addOne(_ number: inout Int) {  
    number += 1  
}  
  
var value = 10  
addOne(&value)  
print(value)

Now that the problem is solved, we will not encounter any errors, and the value will be updated.

> Here, we get no closure, no capture, no heap memory, no boxing, and no performance cost.

## 5. Mark protocols that are only satisfied by classes as class-protocols

This means that if a **protocol** is only needed by a class, not by a **struct** or **enum**, it should be marked with a **class protocol.**

protocol Pingable: AnyObject { func ping() -> Int }

**AnyObject** means that this protocol can only be used with **classes**, not with **structs** and **enums**.

### Why is it important?

Sometimes your protocol requires capabilities that can only be satisfied by classes.

- Inheritance
- reference identity (`===`)
- deinitializers
- shared mutable state
- weak references

Structs and enums cannot do this.

Also, if your **protocol** is only adapted to **classes**, it may improve performance.

## 6. The cost of large Swift values

Please read the following article to better understand it.

[

## Stop Using Swift Struct Like This — It’s Slowing You Down

### As we all know, structs are value types that are lightweight, fast, and safe for creating models and DTOs. But using it…

medium.com



](https://medium.com/ios-lab/stop-using-swift-struct-like-this-its-slowing-you-down-accfb3042209?source=post_page-----b210659717f0---------------------------------------)

## Conclusion

These are the concepts you can keep in mind when writing high-performance Swift code.

> I hope you enjoyed this article a lot. Let me know in the comment section if you have any further questions or suggestions, or if you'd like to correct me anywhere.

_Thank you for reading until the end. Before you go:_

- _Please consider_ **_clapping_** _and_ **_following_** _👏_
- _Follow us on_ [**_Twitter(X)_**](https://x.com/nameisjayant)_,_ [**_LinkedIn_**](https://www.linkedin.com/in/jayant-kumar-%F0%9F%87%AE%F0%9F%87%B3-262597171/)_,_ [**_Substack_**](https://nameisjayant.substack.com/), _and_ [**_YouTube_**](https://www.youtube.com/@ProgrammingSimplified0)**_._**