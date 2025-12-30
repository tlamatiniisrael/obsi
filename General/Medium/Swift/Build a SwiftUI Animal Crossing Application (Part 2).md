
## A deep dive into views and view models

![](https://miro.medium.com/v2/resize:fit:1400/1*uywS4tXTX02BS0_m2j_-EQ.png)

Photo by the author.

The [first part of this series](https://medium.com/better-programming/build-a-swiftui-animal-crossing-application-part-1-aaf3528c1df) of articles introduced you to the nature of the application and the core of its architecture.

In this chapter, we’ll take a deeper look at the various views composing the application and how it all works together with our data source and view models.

## The Case Study

Let’s take a look at the `ItemsListView` and `ItemsViewModel`. Here is a short video for you to see all the functionalities of this screen:

![](https://miro.medium.com/v2/resize:fit:542/1*Fk_Q62dvUdIZNwMw30lvnA.gif)

ItemsListView

The screen has a few features we need to consider:

- It displays a list of items from a specific category of items.
- It offers two display modes: compact rows and large rows.
- We can sort items by their various properties.
- We can search for items from within this list.

## ItemsViewModel

Having listed all that, we can now start to make our `ItemsViewModel` with the goal of preparing and fetching all the data we need from our back end/data source (in this case, fetching items from specific categories) and then computing those data to make them ready to be displayed by our views.

Let’s begin with our view model. Since we know it’ll be used by SwiftUI views, we make it an `ObservableObject`. This means we can now have properties wrapped in a `[Published](https://developer.apple.com/documentation/combine/published)` property wrapper. SwiftUI views will be able to subscribe to those properties and update the relevant views when they change.

As we saw in our features list, the view model needs to provide us with a list of `Items`, a list of `sortedItems`, and a list of `searchedItems`. It also needs to hold the `searchText` that we’ll bind to the TextField we’ll use for the search interface.

The `itemCancellable` and `searchCancellable` are `Cancellable` from Combine. We’ll use them to hold our `Subscriber` from which we’ll get our data from our back end.

Now let’s see what happens in the initializer of our `ItemsViewModel`:

In the initializer, we’ll connect both of our `subscribers` to their `publisher`.

For `searchCancellable`, we want to subscribe to our `searchText` `String` property. Why? Because it’ll allow us to receive its new value as the user type in the `TextField`. Let’s look at the Combine flow:

1. We want to first `debounce` it. This means that we want to wait for a pause in the delivery of events from the publisher. We want to receive an event only when the user stops typing for 300 milliseconds.
2. Then we remove duplicates. In case we receive the same text multiple times, we don’t want to trigger a search in our data source for the same value.
3. We also don’t want to trigger any search if the text is empty.
4. Finally, we execute our very simple search function using `map`. Our function will take the text input and filter our items to return only the one item with a matching name:

private func items(with string: String) -> [Item] {  
     items.filter {  
        $0.name.lowercased().contains(string.lowercased())  
     }  
}

5. We use `sink` (we also could use `assign`) to assign the final value we receive to our `ItemsViewModel` `searchItems` published property.

6. The part of the UI that uses `searchItems` from our view model will get updated.

Our second `Cancellable`, the one to actually get all the items, is much more straightforward. We have a shared object called Items, and it has a published `categories` property, as you can see below:

public class Items: ObservableObject {  
      public static let shared = Items()  
      @Published public var categories: [Category: [Item]] = [:]

The published part is important because it’s a service/`observableObject`, and we don’t know when or where it’ll get updated. So we want to be sure to receive the most up-to-date value.

That’s why, in our `ItemsViewModel`, we subscribe to `categories` and assign the received value whenever it changes (and we receive it the first time we subscribe anyway).

I’m taking the time to explain all the `Combine` flow because it also helps me in the end. This is a very different mindset to have from more standard programming skills. It was not feeling natural at all to me at the beginning. But in the end, it looks much cleaner than chaining functions or completion closures. We can clearly see the whole subscriber operation at once — from its initial value to its transformation until its assignment.

There is one missing part we didn’t cover: the sorting. It’s mostly boring, so I won’t spend time on it. But I’m leaving `ItemsViewModel` here as a full gist so you can see how it’s done. It’s relevant to look at because we’ll use it in the `View` in the next part:

## ItemsListView

Now that we have a pretty accurate understanding of our view model, let’s start the view:

struct ItemsListView: View {  
    @ObservedObject var viewModel: ItemsViewModel  
    @State private var showSortSheet = false  
    @State private var itemRowsDisplayMode: ItemRowView.DisplayMode = .large

As covered in our case study of the screen, we’ll need a few things on top of our view model. The `showShortSheet` state keeps track of the sort sheet. We’ll set it to `true` later when the sort button will be tapped. And the `itemRowsDisplayMode` keeps track of the current display mode of the rows: `Compact` or `Large`.

On top, we also have our `ItemsViewModel`. In the current flow of the application, we have a category screen that is responsible for creating `ItemsListView` with the appropriate `ItemsViewModel`, as the parent will provide the category.

So you can imagine we have a `CategoryRowView` that could look like this:

Now back to our `ItemsListView`. In order to display the appropriate items, we have a computed property: `currentItems`. Whenever our view model’s published properties change, our view body will get computed and use the appropriate items because of `currentItems`:

Let’s take a look at the body of our `View`:

There are a few new things here. We’re using `List`, as it’s the appropriate component, which we saw in our case study. A `VStack` wrapped in a `ScrollView` would not be the appropriate component to use. Because it’s a very long list of items, we want to use `List` (which is backed by UITableView for now). It reuses its rows and dequeues them only when needed (when it’s about to be displayed on screen).

I know this might sound obvious to almost all of you, but as SwiftUI allows complete freedom, it might be a good idea to get reminded of that.

As we saw, we’ll need a `TextField` for the search. As we want it to look like a search bar (and for some reason, SwiftUI lacks a bridge to `UISearchBar`), I’ve wrapped it in a view called `SearchField`.

The `SearchField` view looks like this:

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*TeJNTgj-Yb_GjglCgPr0GQ.png)

With the power of SwiftUI and its previews, it’s super easy to have self-contained views and preview all their states. In this case, as you can see in the screenshot above, I preview the normal state and the editing state.

Previews are a very powerful tool. I don’t use them a lot, but for those self-contained views like our search bar, it makes a lot of sense to use them. You can then iterate on the UI for your various states very fast.

Back to our `ItemsListView`. The next part is the use of `ForEach` with our computed property, `currentItems`. As I said before, as soon as the user triggers a change to `currentItems` (because the user starts a search or sorts the items), it’ll trigger a reload of our body (because our `ItemsViewModel` has published properties). And so our list will always show the correct and relevant items.

We then display our `ItemRowView`, which is the view that will display one item on the list.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*Tw4LNmeZgZakz6wBIyX82w.png)

With the power of previews, and because we saw in our case study that the row should have a compact and a large mode, we can display both at once. Very handy while you work on the UI.

I won’t go into too much detail for the `[ItemRowView](https://github.com/Dimillian/ACHNBrowserUI/blob/master/ACHNBrowserUI/ACHNBrowserUI/views/items/ItemRowView.swift)`. You can find the code on our GitHub. Just take a careful look at the enum `DisplayMode`. `ItemRowView` has a property of this type, and so the parent view can decide on its display mode.

Back to the body of `ItemsListView`. You’ll notice that we wrap our row in a `NavigationLink`. So when the row is tapped, it’ll open `ItemDetailView` with the selected item:

NavigationLink(destination: ItemDetailView(item: item)) {  
    ItemRowView(displayMode: self.itemRowsDisplayMode, item: item)  
  }  
}

And as you can see, we use the current `displayMode` of our `ItemsListView`.

### But how do we change the display mode?

With the power of the `State` property wrapper, we saw earlier that we have:

@State private var itemRowsDisplayMode: ItemRowView.DisplayMode

So now the missing part is to trigger the change. If you look back at the body of our `ItemsListView`, you’ll notice something like this:

.navigationBarItems(trailing: HStack(spacing: 16) {   
                                 layoutButton   
                                 sortButton   
                               }  
                    )

With that modifier on our list, we’re adding the two buttons we need: the change display mode and sort buttons to the navigation bar of our current view.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*W9rNAymFu6wIbqXxsnXlIQ.png)

Let’s take a look at the code for our two computed views, `layoutButton` and `sortButton`:

Whenever you feel like your view body is getting clogged, don’t hesitate to extract part of your view as computed properties or create a new view struct. Your body should always stay small and readable. It’s much easier and faster to make standalone components/views in SwiftUI than in UIKit.

The actions in those two buttons are pretty straightforward. The `sortButton` will toggle the `Bool` that controls our sort sheet, and if you look back at our view body, we have this line:

.actionSheet(isPresented: $showSortSheet, content: { self.sortSheet })

So it’ll present the `ActionSheet` computed by the property `sortSheet`, which look like this:

It constructs and returns our sort `ActionSheet`. And whenever we select an action in this `ActionSheet`, it’ll set the sort on our `ItemsViewModel`, which will set our `sortedItems` published property within this view model and trigger an update on our `currentItems`. And there you have your sort logic.

The `layoutButton` is much simpler. It just changes the current `displayMode` to the other one. It will trigger an update of all of our rows and display them in the new display mode.

And that’s about it. Here is a gist of the whole `ItemsListView` for reference:

## Conclusion

Thanks for reading!

I hope you enjoyed this second part of the series and welcome all your feedback. I’ve tried to explain everything so it can be helpful to beginners in iOS programming and SwiftUI as well as veterans who want to grab some tidbits around here.

Part 3 should be about more complex view layouts and animations — a slightly more advanced topic.

See you soon!
