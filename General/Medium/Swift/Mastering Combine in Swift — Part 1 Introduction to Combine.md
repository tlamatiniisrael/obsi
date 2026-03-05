
# Mastering Combine in Swift — Part 1: Introduction to Combine

![](https://miro.medium.com/v2/resize:fit:1400/1*B28NrN6gJMgqXDclt2z1Ag.png)

Mastering Combine in Swift

When Apple introduced **Combine** at WWDC 2019, it gave Swift developers a modern way to deal with **asynchronous programming** and **data streams**.

If you’ve ever worked with **delegates**, **completion handlers**, **NotificationCenter**, or **KVO (Key-Value Observing)**, you probably know how messy it can get. Combine was designed to unify all of that into one elegant, composable, and declarative framework.

👉 _“You can read the_ [**_full article here_**](https://medium.com/@alokupadhyay1192/mastering-combine-in-swift-part-1-introduction-to-combine-0a9ca1f4a9b1?sk=cfe5aa55bae7916c44a14a03665a8976) _(no membership needed)”_

In this first part of the **“Mastering Combine in Swift”** series, we’ll cover the basics of Combine with simple examples to get you started.

### 🔹 What is Combine?

Combine is Apple’s **reactive programming framework**. It allows you to:

- Define **publishers** that emit values over time.
- Define **subscribers** that react to those values.
- Use **operators** to transform and manipulate data streams.

Think of Combine as a **pipeline**:

```
Publisher → Operator(s) → Subscriber
```

Example analogy:

- A **publisher** is like a news channel.
- A **subscriber** is like a viewer who tunes in.
- **Operators** are filters/editors that modify the news before it reaches the viewer.

### 🔹 Why Combine?

Before Combine, iOS developers relied on:

- Delegates
- Closures
- NotificationCenter
- KVO

All of these worked, but they were **fragmented** and often led to **complex, hard-to-maintain code**. Combine unifies them into a **consistent API**.

✅ Less boilerplate  
✅ Composable operators  
✅ Unified error handling  
✅ Works great with SwiftUI

### 🔹 Core Concepts

Before writing code, let’s break down the **4 key building blocks** of Combine:

1. **Publisher** → Emits values over time.
2. **Subscriber** → Receives and reacts to values.
3. **Operator** → Transforms or filters values between publisher and subscriber.
4. **Cancellable** → A token to stop the subscription.

### 🔹 A First Example with `Just`

Let’s write our very first Combine code using `Just` – a simple publisher that emits **one value and completes**.

```
import Combine  
  
var cancellables = Set<AnyCancellable>()  
  
// Publisher  
let publisher = Just("Hello, Combine!")  
  
// Subscriber  
publisher  
    .sink { value in  
        print("Received value: \(value)")  
    }  
    .store(in: &cancellables)
```

### 🔍 Explanation:

- `Just("Hello, Combine!")` → A publisher that emits one string.
- `.sink { value in ... }` → A subscriber that prints the value.
- `.store(in: &cancellables)` → Keeps the subscription alive.

**Output:**

```
Received value: Hello, Combine!
```

### 🔹 Example 2: Transforming Data with an Operator

Let’s say we want to take numbers, double them, and print the result.

``` swift
import Combine  
  
let numbers = [1, 2, 3, 4, 5]  
  
numbers.publisher  
    .map { $0 * 2 }  
    .sink { value in  
        print("Doubled value: \(value)")  
    }  
    .store(in: &cancellables)
```

**Output:**

Doubled value: 2  
Doubled value: 4  
Doubled value: 6  
Doubled value: 8  
Doubled value: 10

Here, `numbers.publisher` creates a publisher from an array. The `map` operator transforms each value before the subscriber receives it.

### 🔹 Example 3: Handling Completion

Publishers can emit values and then **complete** or **fail**. Let’s print both values and completion status:

let publisher2 = ["A", "B", "C"].publisher  
  
publisher2  
    .sink(  
        receiveCompletion: { completion in  
            print("Completed with: \(completion)")  
        },  
        receiveValue: { value in  
            print("Received: \(value)")  
        }  
    )  
    .store(in: &cancellables)

**Output:**

Received: A  
Received: B  
Received: C  
Completed with: finished

### 🔹 Key Takeaways from Part 1

- **Combine = Unified framework** for handling asynchronous values.
- A **publisher** emits values → a **subscriber** consumes them.
- **Operators** allow transforming, filtering, and combining values.
- Everything in Combine is **composable** and **declarative**.

### ✅ What’s Next?

In **Part 2: Deep Dive into Publishers**, we’ll explore different kinds of publishers in Combine (`Just`, `Future`, `PassthroughSubject`, `CurrentValueSubject`, etc.) and how they differ.

Stay tuned — it’s where Combine starts to get really powerful 🚀.

💡 _Follow me here on Medium for the full series:_ [**_Mastering Combine in Swift_**](https://medium.com/@alokupadhyay1192)  
🚀 Next up: [**_Part 2 — Publishers in Depth_**](https://medium.com/@alokupadhyay1192/c31ea9350421)

## A message from our Founder

**Hey,** [**Sunil**](https://linkedin.com/in/sunilsandhu) **here.** I wanted to take a moment to thank you for reading until the end and for being a part of this community.

Did you know that our team run these publications as a volunteer effort to over 3.5m monthly readers? **We don’t receive any funding, we do this to support the community. ❤️**

If you want to show some love, please take a moment to **follow me on** [**LinkedIn**](https://linkedin.com/in/sunilsandhu)**,** [**TikTok**](https://tiktok.com/@messyfounder), [**Instagram**](https://instagram.com/sunilsandhu). You can also subscribe to our [**weekly newsletter**](https://newsletter.plainenglish.io/).

And before you go, don’t forget to **clap** and **follow** the writer️!