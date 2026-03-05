
Swift is one of the most **advanced**, **expressive**, **fast**, and **beloved** programming languages in the world.

[

## 6 Hidden SwiftUI Modifiers You Probably Missed

### Not a Medium Member? “Read For Free”

nameisjayant2.medium.com



](https://nameisjayant2.medium.com/6-hidden-swiftui-modifiers-you-probably-missed-04b822bc9738?source=post_page-----eb9038e78413---------------------------------------)

There are **various hidden secrets** that you might not know or have even not heard about. In this article, we will discuss those.

## 1. @inline(__always)

You write a function like this:

func add(_ a: Int, _ b: Int) -> Int {  
    a + b  
}

When you call this **add** function somewhere, the **Swift compiler** actually **copies the whole function each time.**

That’s totally fine, but when this function is called **millions of times** in a loop, it might **impact the app's performance.**

Then, where **@inline(__always)** comes in.

@inline(__always)  
func add(_ a: Int, _ b: Int) -> Int {  
    a + b  
}

When we add **@inline(__always)** to a function, it instructs the **Swift compiler** to **copy the code directly** instead of calling the **entire function**. It’s called **function inlining.**

It means it only copies this code:

 a + b

Not the whole function code:

func add(_ a: Int, _ b: Int) -> Int {  
    a + b  
}

### Why You Shouldn’t Use It Everywhere

Every time the **compiler copies the function code**, your binary **size grows.**

If you **inline a big function** or a **function called in many file**s, your app will:

- Become bigger in size
- Take a longer time to compile
- Can even slow down

**Use only:**

- If the function is small
- Called very often in loops and for math calculations.

## 2. @_transparent

Actually, it’s a more advanced version of **@inline(__always).** When you add **@inline(__always)** to any function, it depends on the **compiler** whether it **will optimize the code or not.**

It might be skipping the inline if:

- The code is excessively large
- The code optimization does not improve performance.

But when we use **@_transparent**, it instructs the **compiler to force-inline** this code and optimize the rest of the code as well.

Look at this code:

@_transparent  
func addOne(_ x: Int) -> Int {  
    x + 1  
}  
  
func doubleAndAddOne(_ y: Int) -> Int {  
    2 * addOne(y)  
}

We added **@_transparent** to the **addOne** function.

Now it internally, copy this code:

 2 * (y + 1)

And simplifies to:

 (2 * y) + 2

This is the deeper optimization of the code that **@inline(__always)** cannot guarantee.

## 3. Namespace with Nested Types

In Swift, a **namespace** is a way to **organize related items together**. However, in Swift, there is no way to create a namespace.

But there is a trick to use **namespace** in Swift by using **Nested Structs.**

We usually write **'constant'** like this:

let baseURL = "https://api.example.com"  
let loginURL = "https://api.example.com/login"  
let profileURL = "https://api.example.com/profile"

This works for small projects, but as the project grows, it becomes poor practice.

Then, **nested structs** serve as a solution.

struct Network {  
    struct Endpoint {  
        static let base = "https://api.example.com"  
        static let login = base + "/login"  
        static let profile = base + "/profile"  
    }  
}

Now everything is well-organized, and using this anywhere is also simple.

let url = Network.Endpoint.login

## 4. Enums Can Store Functions

You have seen an **enum** like this:

enum Direction {  
    case north  
    case south  
    case east  
    case west  
}

That’s fine, but do you know we can also define **functions in the enum** like this:

enum UIAction{  
    case ShowToast(()->Void)  
    case FetchData((String)->Void)  
}

Let’s define the body for each individual.

let showToast = UIAction.ShowToast{  
    print("Hello, world")  
}  
  
let fetchData = UIAction.FetchData{data in  
    print(data)  
}

Here, we define what both functions will do; later, we will pass them in at the calling site.

Next, use the **switch** case to handle the enum.

func action(_ uiAction : UIAction){  
    switch uiAction{  
    case  let .FetchData(task) : task("Hello Swift")  
    case let .ShowToast(handler) : handler()  
    }  
}

Now, call this action function somewhere and pass the required **UIAction**.

func calling(){  
    action(showToast)  
    action(fetchData)  
}

Here, we called the **showToast** and **fetchData** functions respectively.

## 5. final Keyword

When you create a **class**, anyone can **inherit** from it and make modifications.

But when you add the **final keyword**, it means no one can make any modifications to it.

There are two main reasons the **final** **keyword** is special.

### **To protect class design**

Sometimes, you create a **class** for a specific purpose, but when someone inherits it and changes something in it, it can affect many places where it is called.

So making them **final** means working as expected, as you want.

### To make your app faster

When you create a class and subclass like this:

class Animal {  
    func makeSound() {  
        print("Some sound")  
    }  
}  
  
class Dog: Animal {  
    override func makeSound() {  
        print("Woof!")  
    }  
}  
  
let myPet: Animal = Dog()  
myPet.makeSound()

Here, the class **Animal** is not final; it means anyone can extend this class. But when you call the **makeSound** function, the **Swift compiler** checks which version of this function is called — whether it's from the **Animal** or Dog class.

The **Swift compiler** looks up the right version of the **makeSound** function during runtime, which is called **dynamic dispatch.**

This might take a little extra time and could affect the app's performance.

But when you add the _class_ with the **final** keyword, it knows it cannot be overridden.

So the Swift compiler doesn’t need to check which function to run; it can call it directly.

This process is called **devirtualization** — **“skip the dynamic lookup.”**, which improves the performance.

## 6. The “didSet” of Lazy Properties

When **didSet** is used with **lazy** properties, it is called when the **lazy** variable is **assigned to new data.**

Let’s understand through an example:

 lazy var title: String = "Swift" {  
        didSet {  
            print("Changed to \(title)")  
        }  
    }  
}

When the **title variable is first accessed**, **didSet** will not be called; it is called when the **title is reassigned with another value.**

## 7. The “didSet” and “willSet” Order Matters

In Swift, **willSet** executes before the **value** updates, while **didSet** runs immediately after.

var counter: Int = 0 {  
    willSet {  
        print("About to change from \(counter) to \(newValue)")  
    }  
    didSet {  
        print("Changed from \(oldValue) to \(counter)")  
    }  
}  
  
counter = 10

**Output:**

About to change from 0 to 10  
Changed from 0 to 10

First, **willSet** is called before the value is updated, and once you update the **counter** variable, **didSet** **is called immediately.**

_Thank you for reading until the end. Before you go:_

- _Please consider_ **_clapping_** _and_ **_following_** _👏_
- _Follow us on_ [**_Twitter(X)_**](https://x.com/nameisjayant)_,_ [**_LinkedIn_**](https://www.linkedin.com/in/jayant-kumar-%F0%9F%87%AE%F0%9F%87%B3-262597171/)_,_ [**_Substack_**](https://nameisjayant.substack.com/), _and_ [**_YouTube_**](https://www.youtube.com/@ProgrammingSimplified0)**_._**