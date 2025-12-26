
![](https://miro.medium.com/v2/resize:fit:1400/0*V8OzsNskRvmRbaUV)

Photo by [Hannah Busing](https://unsplash.com/@hannahbusing?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

As we all know, SwiftUI is fast, declarative, easy to use, and takes less time to build UIs than UIKit.

But as beginners, we write **SwiftUI** in ways that ruin this beautiful framework.

In this article, we will look at a **guide to writing SwiftUI in a clean way.**

## 1. Stop Putting Everything Into One Big View

The biggest mistake we make is writing everything in a single file; as the project grows, this can become messy and make the code harder to read.

Instead, **break each component into its own common view** and include it in the main view, as this enhances component reusability.

Look at this example:

```
  struct HomeView: View {  
  
    var body: some View {  
        VStack {  
            Header()  
            SearchBar()  
            Stats()  
            ListSection()  
            Footer()  
        }  
    }  
}
```

As you can see, each component is defined separately and called in the Main view. Now our Main view is short, clean, and easy to read.

## 2. Don’t Put Your App’s Logic Inside the View

I have seen most of the codebase, where developers put all the logic inside the view. Remember this — **your** **view file is meant only to show the UI, not to handle business logic.**

It’s always better to move the logic out of the view and keep it somewhere, like in the **viewmodel**.

```
@MainActor  
final class MainViewModel: ObservableObject {  
    @Published var items: [TaskItem] = []  
  
    var overdueItems: [TaskItem] {  
        items.filter { $0.isOverdue }  
    }  
}
```

Here, we keep the logic in the view model and use it in the view file like this:

```
struct MainView: View {  
    @StateObject private var vm = MainViewModel()  
  
    var body: some View {  
        List(vm.overdueItems) { task in  
            Text(task.title)  
        }  
    }  
}
```

Now the view is clear and easy to understand because someone else handles the logic.

## 3. Use View Extensions for Reusable Modifiers

Most of the time, we often rewrite the same modifier repeatedly, as this increases the boilerplate.

Whenever we see any modifier used in multiple places in the app, make them common with a **View Extension.**

```
Text("Welcome")  
    .font(.headline)  
    .foregroundColor(.primary)  
    .padding(.vertical, 8)
```

If the above modifiers are used multiple times, instead of rewriting them each time, make them common and reuse them wherever needed.

```
extension View {  
    func header() -> some View {  
        self  
            .font(.headline)  
            .foregroundColor(.primary)  
            .padding(.vertical, 8)  
    }  
}
```

**Usages:**

```
Text("Welcome").header()
```

Now, whenever I need those modifiers, I call the **header()** method with the view.

[

## Creating Custom Modifiers is Super Easy with SwiftUI

### Repost Creating Custom Modifiers is Super Easy with SwiftUI Not a Medium Member? "Read For Free" Modifier is a function…

nameisjayant2.medium.com



](https://nameisjayant2.medium.com/creating-custom-modifiers-is-super-easy-with-swiftui-8d1c015a4bdd?source=post_page-----02df69c64b30---------------------------------------)

## 4. Don’t Use Too Many “If” Inside “If”

Sometimes we write code like this:

```
if isLoggedIn {  
    if hasSubscription {  
        PremiumDashboard()  
    } else {  
        SubscribeNow()  
    }  
} else {  
    LoginView()  
}
```

As this code is deeply nested, it becomes harder to read.

Instead of writing **nested if statements**, use a **switch statement** to make the logic clearer.

```
Group {  
    switch (isLoggedIn, hasSubscription) {  
    case (false, _):  
        LoginView()  
    case (true, false):  
        SubscribeNow()  
    case (true, true):  
        PremiumDashboard()  
    }  
}
```

This looks cleaner and more readable.

## 5. Use Constants and Design Tokens

Suppose the **spacing** and **corner radius** are consistent throughout the application; you can set them once and use them everywhere.

Instead of hardcoding this:

```
.padding(12.0)  
.cornerRadius(16.0)
```

Do it like this:

```
enum Spacing {  
    static let medium: CGFloat = 12  
}  
  
enum CornerRadius {  
    static let card: CGFloat = 16  
}  

  
  
// In the view file  
.padding(Spacing.medium)  
.cornerRadius(CornerRadius.card)
```

This ensures consistent design throughout the entire app.

## 6. Keep File Names Clean

Instead of writing the file name like this:

- `HomeView2.swift`
- `NewFile.swift`
- `User2List.swift`

Do this instead, as it should indicate the file's purpose.

- `HomeView.swift`
- `UserListView.swift`
- `UserListViewModel.swift`
- `TaskRow.swift`

Now any new developer can understand what each file does.

## 7. Use Meaningful Variable Names

As we know, SwiftUI is declarative, rather than naming the variable like this:

```
let t: String  
var v: Bool
```

Do it like this:

```
let title: String  
var isVisible: Bool
```

Now this is clearer and easier for any new developer to understand.

## Conclusion

> These are some practices I follow to write better SwiftUI code.
> 
> Let me know in the comments section what other practices you follow for writing clear SwiftUI code.