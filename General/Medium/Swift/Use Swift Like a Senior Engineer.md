
In this article, we will see the **Swift Tips and Tricks** that will make you think like a **Senior Engineer.**

> **Read Part 2** 👇

[

## Use Swift Like a Senior Engineer — Part (2)

### These Swift tricks will think you like a Senior Engineer

medium.com



](https://medium.com/ios-lab/use-swift-like-a-senior-engineer-part-2-feb014be5731?source=post_page-----2d4a6b8a0f0e---------------------------------------)

Let’s get started!

## 1. Overload the pattern-matching operator for custom matching behavior

First, let’s understand the **“pattern-matching”** operator.

> In **Swift**, **“~=”** is the **pattern-matching operator.**

Look at this code:

switch number {  
case 1...5:  
    print("Small number")  
default:  
    break  
}

When Swift compiles and runs the above code, it internally does it like this:

(1...5) ~= number

It checks whether the number is within the specified range.

> In Swift, we can easily **overload** the **pattern-matching operator (~=).**

### Why do we overload the **pattern-matching operator (~=)?**

Generally, when we use a **pattern-matching operator**, Swift uses the built-in logic. If we overload it, we can provide our own logic, and Swift will use it.

Suppose we have a struct called **Circle**,

struct Circle{  
    var radius:Double  
}

Let’s use this in a switch statement,

var circle = Circle(radius: 10)  
      
    switch circle.radius {  
    case 10:  
        print("Hello")  
    default:  
        print("Not 10")  
    }

Here, it is simple: we pass the radius to the switch statement and check whether it falls within the given number.

That’s fine!

But what if we want this, so that we do not pass the **“circle.radius”** only passes the circle, and the **switch statement compares each case with the radius of the circle**, like this:

var circle = Circle(radius: 10)  
  
switch circle {  
case 10:  
    print("Hello")  
default:  
    print("Not 10")  
}

As of now, it will give a compile-time error: **“Expression pattern of type ‘Int’ cannot match values of type ‘Circle’”.**

To resolve this error, we will override the **pattern-matching operator** to implement our own logic and make the code above work.

func ~= (pattern:Double,value:Circle) -> Bool {  
    return value.radius == pattern  
}

Here, we override the **pattern-matching operator,** which takes two mandatory parameters.

**pattern** — the cases of the switch statement.

**value** — with whom we need to compare

Inside the function, we compare the pattern and the circle's radius.

Now, when we write a switch statement like this, it will work;

let circle = Circle(radius: 10)  
      
    switch circle {  
    case 10:  
        print("Hello")  
    default:  
        print("Not 10")  
    }

When we pass the circle, inside the switch, it will compare the circle’s radius.

We can also define as many overrides of the **pattern-matching operator** as we want.

Suppose we want to check whether the **circle’s radius** falls in a specific range.

func ~= (pattern: ClosedRange<Double>, value: Circle) -> Bool {  
    return pattern.contains(value.radius)  
}

Now, in the switch statement, we can check the specific range as well.

let circle = Circle(radius: 10)  
      
    switch circle {  
    case 1...10:  
        print("Hello")  
    default:  
        print("Not 10")  
    }

Here, we can now check whether the radius is in the **1…10** range or not.

Let’s take another example,

let input = "admin"  
  
switch input {  
case "ADMIN":  
    print("Matched")  
default:  
    print("No match")  
}

Here, when we compare the input, it falls through to the default case because Swift compares **strings case-sensitively.**

> **admin** !== **ADMIN**

Let’s overload the **~=** for **case-sensitively matching.**

func ~= (pattern: String, value: String) -> Bool {  
    pattern.lowercased() == value.lowercased()  
}

Inside the function, we changed both the value and pattern to lowercase.

Now it doesn’t matter whether the string is in uppercase or lowercase; Swift will not compare strings **case-sensitively inside the switch.**

## 2. Switch on multiple optional values simultaneously

This means that we can **utilize the tuple pattern** in a switch statement to handle multiple values simultaneously.

Let’s understand with an example,

func test(){  
    var optionalInt1: Int? = 1  
    var optionalInt2: Int? = nil  
      
    switch (optionalInt1, optionalInt2) {  
    case let (value1?, value2?):  
        print("Both have values: \(value1) and \(value2)")  
    case let (value1?, nil):  
        print("First has a value: \(value1), second is nil")  
    case let (nil, value2?):  
        print("First is nil, second has a value: \(value2)")  
    case (nil, nil):  
        print("Both are nil")  
    }  
}

Here, we have two optional values (**optionalInt1**, **optionalInt2**). Depending on their value, we will execute different actions.

In the first case, we check if both values are non-nil, then print the statement.

In the second and third cases, we check where one of the optional values is nil.

In the final case, we check if both optional values are nil.

> This way, we can structure the switch statement and handle each combination explicitly.

## 3. Iterate over items and indices in collections

When we need to iterate over a sequence in Swift, the **enumerated()** function is the one that comes to mind.

var ingredients = ["Apple", "Banana", "Orange"]  
   
for (i, ingredient) in ingredients.enumerated() {  
        print("ingredient number \(i + 1) is \(ingredient)")  
}  
  
// output  
  
0 Apple  
1 Banana  
2 Orange

> It displays the **index** with the **ingredient name.**

But it’s crucial to understand the limitations of **enumerated().**

The index it produces **starts at zero and increments by 1 for each item,** which is perfect for use as a counter but not necessarily as an index, especially if the collection isn’t zero-based.

We know that **enumerated()** starts at **index 0**, but not all collections begin at index 0.

Let’s see the example,

var ingredients = ["Apple", "Banana", "Orange"]  
var slice = ingredients.dropFirst() // Apple will be removed from the list.

Here, the slice variable is **Array<Int>.SubSequence** and its index are,

1, 2 

So when we loop it with **enumerated(),**

for (i, value) in slice.enumerated() {  
    print("i =", i, "value =", value)  
}

The output will look like this,

i = 0 value = Banana  
i = 1 value = Orange

This looks fine until you use **i** as an index,

slice[i] // crash

The application will crash because,

i is 0,1

But the valid indices are 1 and 2.

To resolve this problem, we will use the **zip()** method. This method pairs each element with its index, even if the collection has been modified.

var ingredients = ["Apple", "Banana", "Orange"]  
  
var slice = ingredients.dropFirst()  
  
for (index, value) in zip(slice.indices, slice) {  
    print(index, value)  
}  
  
// output  
1 Banana  
2 Orange

The index will now start at 1 and remain valid for each item.

Another way to use with the **indices,**

for index in slice.indices {  
    print(index, slice[index])  
}  
  
// output  
1 Banana  
2 Orange

## 4. Label loop statements to control execution of nested loops

Suppose you have nested loops like this,

for i in 1...3 {  
    for j in 1...3 {  
        if j == 2 {  
            break  
        }  
        print("i:", i, "j:", j)  
    }  
}

Here, we place a break statement inside the inner loop, but the outer loop continues running.

Sometimes you want to,

- break the outer loop
- continue the outer loop
- Stop the execution of the program

Currently, it isn't easy to do the above scenarios.

To do this, a **loop label** comes in.

outerLoop: for i in 1...3 {  
    for j in 1...3 {  
        if j == 2 {  
            break outerLoop  
        }  
        print("i:", i, "j:", j)  
    }  
}  
  
// output  
  
i: 1 j: 1

As you can see, we provide a label to the **outer loop**; now it is easy to break the outer loop with minimal effort, or you can do anything with it.

_Thank you for reading until the end. Before you go:_

- _Please consider_ **_clapping_** _and_ **_following_** _👏_
- _Follow us on_ [**_Twitter(X)_**](https://x.com/nameisjayant)_,_ [**_LinkedIn_**](https://www.linkedin.com/in/jayant-kumar-%F0%9F%87%AE%F0%9F%87%B3-262597171/)_,_ [**_Substack_**](https://nameisjayant.substack.com/), _and_ [**_YouTube_**](https://www.youtube.com/@ProgrammingSimplified0)**_._**