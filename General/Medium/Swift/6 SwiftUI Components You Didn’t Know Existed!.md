
![](https://miro.medium.com/v2/resize:fit:1400/0*B96Fi3yDscHu9cFM)

Photo by [Vitaly Gariev](https://unsplash.com/@silverkblack?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

You have used **VStack**, **HStack**, and **ZStack** Views while writing UI in **SwiftUI**, but **SwiftUI** offers more than just those.

**Read part 2 of this article👇**

[

## 6 SwiftUI Components You Didn’t Know Existed (Part-2)

### Not a Medium Member? “Read For Free”

medium.com



](https://medium.com/ios-lab/6-swiftui-components-you-didnt-know-existed-part-2-12ebb9d66240?source=post_page-----c43eeb9f2752---------------------------------------)

> In this article, we will look at those you thought did not exist.

## 1. TimelineView

If you want to create a **clock** and a **countdown timer**, then **TimelineView** is the perfect View.

**Let’s build a clock**

``` swift
  TimelineView(.animation) { context in  
                Text(context.date, style: .time)  
                    .font(.system(size: 48, weight: .medium, design: .monospaced))  
                    .padding()  
            }
```

**Output:**

![](https://miro.medium.com/v2/resize:fit:848/1*sUZpmhdVWzODVZrSHIvjIw.png)

It automatically refreshes when the system time updates.

**Let’s create a countdown timer.**

```swift
 let endDate = Date().addingTimeInterval(30)   
  
 TimelineView(.periodic(from: .now, by: 1)) { context in  
                let remaining = max(Int(endDate.timeIntervalSince(context.date)), 0)  
                Text("\(remaining)s left")  
                    .font(.system(size: 36, weight: .bold, design: .rounded))  
                    .foregroundColor(remaining == 0 ? .red : .green)  
                    .animation(.easeInOut, value: remaining)  
            }
```

**Output:**

![](https://miro.medium.com/v2/resize:fit:780/1*4KfnbxECD-28Ys_-6OHYag.png)

You can also refresh weather data every minute using **TimelineView.**

```swift
 TimelineView(.periodic(from: .now, by: 60)) { context in  
            VStack(spacing: 8) {  
                Image(systemName: "cloud.sun.fill")  
                    .font(.largeTitle)  
                    .foregroundStyle(.white)  
                Text("25°C")  
                    .font(.title)  
                Text("Updated at \(context.date.formatted(date: .omitted, time: .shortened))")  
                    .font(.caption)  
                    .foregroundStyle(.secondary)  
            }  
            .padding()  
        }
```

**Output:**

![](https://miro.medium.com/v2/resize:fit:652/1*lW7IFjxyJVUps1Lag6A32A.png)

This will update the Weather data every minute.

## 2. DisclosureGroup

This creates an **Expandable View** — perfect for use in **Setting Menus.**

struct ContentView: View {  
      
    @State var darkMode = false  
    @State var biometric = false  
      
    var body: some View {  
          
        VStack{  
            DisclosureGroup("More Settings") {  
                Toggle("Enable Dark Mode", isOn: $darkMode)  
                Toggle("Use Biometrics", isOn: $biometric)  
            }  
        }  
          
    }  
}

**Output:**

![](https://miro.medium.com/v2/resize:fit:1100/1*6_20x9E2QIkdgr7-yeLBHw.png)

## 3. Canvas

This SwiftUI view is used for drawing anything, like **custom shapes**, **custom effects**, and even **mini-games**.

Let’s draw a **circle** **shape** with **Canvas**.

 Canvas { context, size in  
            let rect = CGRect(x: 0, y: 0, width: size.width, height: size.height)  
            context.fill(Path(ellipseIn: rect), with: .color(.blue))  
        }  
        .frame(width: 100, height: 100)

**Output:**

![](https://miro.medium.com/v2/resize:fit:484/1*1Tisy_pTOqp9NfeYsLfUUg.png)

This is great for creating **charts**, **graphs**, and any **custom shapes**.

## 4. ScenePhase

This will **detect** when your app goes to **foreground**, **background**, or **inactive** mode, **directly from the** **view**.

struct ContentView: View {  
      
    @Environment(\.scenePhase) var scenePhase  
      
    var body: some View {  
          
        VStack{  
            Text("Hello, SwiftUi")  
        }  
        .onChange(of: scenePhase) { oldPhase, newPhase in  
            switch newPhase {  
            case .active: print("App active")  
            case .inactive: print("App inactive")  
            case .background: print("App in background")  
            default: break  
            }  
        }  
    }  
}

Now you have all three **phases** — do whatever you want there.

## 5. ColorPicker

**SwiftUI** provides a ready-to-use **color picker** **View**.

struct ContentView: View {  
      
    @State private var selectedColor: Color = .blue  
      
    var body: some View {  
          
        ColorPicker("Choose Color", selection: $selectedColor)  
          
    }  
}

**Output:**

![](https://miro.medium.com/v2/resize:fit:1076/1*MD9nA8Py-ZS27Sh7ufih3g.png)

![](https://miro.medium.com/v2/resize:fit:1032/1*MKz7glUNDHp8seGrVESxjQ.png)

## 6. Label

Through the **Label** View, we can display both the **Text** and **Icon** together.

 Label("Cart", systemImage: "cart")  
            .font(.largeTitle)

**Output:**

![](https://miro.medium.com/v2/resize:fit:688/1*XFx6uq4z94yYpvML4syunQ.png)

## Conclusion

These are the hidden Views that definitely improve the developer experience, and you don’t need to create any Custom View for that.

_Thank you for reading until the end. Before you go:_

- _Please consider_ **_clapping_** _and_ **_following_** _👏_
- _Follow us on_ [**_Twitter(X)_**](https://x.com/nameisjayant)_,_ [**_LinkedIn_**](https://www.linkedin.com/in/jayant-kumar-%F0%9F%87%AE%F0%9F%87%B3-262597171/)_,_ [**_Substack_**](https://nameisjayant.substack.com/), _and_ [**_YouTube_**](https://www.youtube.com/@ProgrammingSimplified0)**_._**