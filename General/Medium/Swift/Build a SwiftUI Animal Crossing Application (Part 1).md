
## Combine, services, models, and EnvironmentObject

![](https://miro.medium.com/v2/resize:fit:1400/1*YUocOak5Ptn7gz1VqaSwpQ.png)

Photo by the author.

In this new series of articles, I’ll guide you through the making of my latest [open-source project](https://github.com/Dimillian/ACHNBrowserUI).

It is once again a fully open-source, fully SwiftUI application.

And this time, it’s even available on the App Store! This goes to prove my point that you can have SwiftUI code in production.

[

## ‎AC Helper

### ‎AC Helper is a companion app for Animal Crossing: New Horizons. You'll access to all the information you need to catch…

apps.apple.com



](https://apps.apple.com/us/app/ac-helper/id1508764244?ls=1&source=post_page-----aaf3528c1df---------------------------------------)

But unlike MovieSwiftUI, it doesn’t use the Redux architecture but just the very core of the SwiftUI built-in data flow that I’ve already covered in the past. If you need an introduction or refresher, you should read [this article](https://medium.com/better-programming/understanding-swiftui-data-flow-79429a49ae35).

I’m using a combination of view, view models, and some environment objects. By following this series of articles, you’ll be able to fully grasp a very simple yet effective SwiftUI application architecture.

Let’s begin with the services and models layer because for the application to be fun, we’ll first need to gather, compute, and fetch some data to put in our data sources. We’ll use Combine, a new (WWDC 19) framework from Apple that allows users to beautifully process data in a reactive way.

[

## Combine

### The Combine framework provides a declarative Swift API for processing values over time. These values can represent many…

developer.apple.com







](https://developer.apple.com/documentation/combine?source=post_page-----aaf3528c1df---------------------------------------)

For people not familiar with _Animal Crossing_, the game is all about playing a bit daily. The game uses the real-time clock to progress. And it’s all about collecting items, furniture to decorate your house and island, and also catching critters, fish, bugs, and fossils. That depends on the time of day and the date.

This is all to say that ==_Animal Crossing_== ==is the perfect game to make an app for because it has a ton of data (more than 5,000 items to catalog, display, and play with) in the form of information, images, and such. It’s a good candidate for which to make a pretty and playful application that is a bit more than a glorified REST client.==

## The Combine Part

So let’s start with the data. The community did an incredible job, and to this date, it’s still dumping, organizing, and editing the [master spreadsheet](https://docs.google.com/spreadsheets/d/13d_LAJPlxMa_DubPTuirkIV4DERBMXbrWQsmSh8ReK4/edit#gid=1413435769). It’s an incredible work that contains all of the game’s data. A spreadsheet is not that convenient for working with an app, so the owner of [Nook Plaza](https://nookplaza.net/) made an awesome pipeline to convert the spreadsheet into a very simple JSON API.

I then dumped this API and put the [JSON directly in the Xcode](https://github.com/Dimillian/ACHNBrowserUI/tree/master/ACHNBrowserUI/Resources/json) project. It’ll allow us to work with Swift’s [Codable](https://developer.apple.com/documentation/swift/codable) protocol to directly instantiate the JSON into native Swift objects.

So let’s first make our service that will read the JSON and decode it into objects:

The function returns a publisher that you can subscribe to in order to get the decoded result. The final type of the object is inferred by the user of this function. It’s generic to work with any type you need from within the JSON. We just need to know that the generic type conforms to `Codable`. We could even specify Decodable, as we don’t need to encode it back (for now).

If we read the Combine chain, it would look like so:

1. First, we try to get the JSON URL from the bundle resources.
2. Then we try to read it into `Data`.
3. Then we decode the object to the type using our static `JSONDecoder`.
4. If we get any error in the above, we publish the error into a known `APIError` type with the message.
5. And finally, we want all that to happen in a specific background queue once a subscriber starts the work.

I think this looks much cleaner than how we could have done it without Combine. Even if using Combine is not entirely justified in this case, it was more an exercise to get familiar with it than to use it to its full potential.

Let’s now look at the consumer classes, the front end that will use this service, and how we’ll use it. We’ll still use Combine, and this will be our first `EnvironmentObject` — the one that stores all the items (all the decoded objects) in those JSON files:

The data is split into various categories, so we’ll reference it in an enum. In the app, this enum has a lot of functions (e.g. to return the correct label and icon for the UI).

Here is also an extract of our `Codable` object that represents what is stored in our JSON files:

And finally the `Items` class:

This is our `Items` class that will store all the items by category. It’s an `ObservableObject` with one `Published` property. SwiftUI views will able to subscribe to this object and reload their body whenever it gets updated.

In this class, we consume our API service and call the `fetch` function for each category. We don’t want to handle the error for now, so we replace it with an empty `ItemResponse`. We then extract only the results (the array of Item objects). We specify that we want to receive the final data in the main queue. It’s important because we’ll update our published property and thus most probably update the UI. And you can’t do that on a background thread. Remember, we specified that our service should do all its job into a background queue.

Finally, we use `sink`, which allows us to get a closure on the subscriber when it receives the final transformed value. We then store the decoded list of items into the corresponding category.

## The SwiftUI Part

Now that we have our Items ready to be used in the app, let’s make them available to the UI. As we’ll reuse those Items in various views, as we want them to be available everywhere. So we’ll use `[EnvironmentObject](https://developer.apple.com/documentation/swiftui/environmentobject)`, a SwiftUI property wrapper that allows us to recover an object from the environment.

And to add our Items to our application environment, we need to attach it to the root view of our view hierarchy. The best place to do that is in the `SceneDelegate`:

On line 7, when we create our `TabbarView`, we add an instance of `Items` to the environment. This instance will be kept around as long as our application process is alive. And then we use our `TabbarView` as the root view of our application (line 11).

Let’s now build a list of those categories of items and a list of items from this `EnvironmentObject`.

Let’s first look at our root view, the `TabbarView`:

It’s good practice to have an enum for your `Tab`. Since `TabView` from SwiftUI needs a binding, you’ll need a `@State` property wrapper to store your currently selected tab. It’ll change as you touch the tab of your Tabbar. It’s a two-way street, so maybe later you’ll want to have a sort of UIState that has the selected tab as a `@Published` property and then store it in an `EnvironmentObject`. It’ll allow you to swap tabs programmatically from anywhere in the app.

This is what I actually do in the full project, but I wanted to simplify it for this article.

Finally, our `TabedView` has one tab for now. This is the `HomeView`:

As we saw previously, we read our Items environment object using the `@EnvironmentObject` property wrapper.

Then I made a convenience dynamic property on the view (categories), which returned a tuple (`(Category, Items)`). This will be much easier to use with SwiftUI’s `ForEach`.

If you take a look at the body of our `HomeView`, we have a `List`, and for every category, we’ll make a section that will have a Scrolling `HStack` of ten items.

And voilà!

![](https://miro.medium.com/v2/resize:fit:1000/1*scJhp_OuuliQR1eAGpN6Fg.png)

==This is not pretty,== but we have our prototype of a SwiftUI app. And it’s fully functional. It uses various SwiftUI dataflow property wrappers to read and publish live back-end data into your app.

This is now what the application looks like for categories and items:

![](https://miro.medium.com/v2/resize:fit:800/1*UH_G3U8DuppG5Ar0945zgg.png)

![](https://miro.medium.com/v2/resize:fit:800/1*_A9MLp6iD59P4wb1z1MBPQ.png)

I’ve made it much simpler for the article, of course. It would have been too much code to embed if I wanted to show you the full application. But you can take a look at the full repository here and build it yourself:

[

## Dimillian/ACHNBrowserUI

### This is a full featured Animal Crossing helper application entirely in SwiftUI. Dashboard to track your fishes, bugs…

github.com



](https://github.com/Dimillian/ACHNBrowserUI?source=post_page-----aaf3528c1df---------------------------------------)

There is one more thing I wanted to talk about: the view model. A view model is an important building block for a basic SwiftUI application. It’s the layer between your back-end services and raw models and your view. It allows you to put all the business logic that should not be in your views and expose only what your views need. You don’t want heavy calculation or anything of the sort directly in the body of your SwiftUI view.

Here is a look at the `ItemsViewModel`, the view model that handles a list of items (as you can see in the screenshoot above):

Just as the screen has various functions, this view model does too. It’ll hold a list of items but also the search text we directly bind in the `TextField` that we use as our search bar (the searched items, etc.).

And here is how we use it in our current flow:

The categories list will open an `ItemsListView` with an `ItemsViewModel` scoped on the selected category. And there you have it!

This is a bit more complex, but it’ll allow you to get the gist of what you could do in a real-world application.

Let me know what you think of this first article in the new series. I try to keep it simple and embed all the code so you can get something working yourself without looking at the repository too much. For the rest of the series, do you want something more in-depth or more code to make it work on your own?

If you wish to contribute, you’re more than welcome. We already have a few contributors and it’s really a great project if you want to try out SwiftUI. So clone it and open your first PR. We won’t bite!

## Conclusion

Thank you for reading this article!

I hope you enjoyed this first part and I’ll see you in part 2, where we’ll dig deeper into view models and views. We’ll try to build more complex views, some animations, and explore internal state management.

[

  




](https://medium.com/better-programming/build-a-swiftui-animal-crossing-application-part-2-82b4ac6695e6?source=post_page-----aaf3528c1df---------------------------------------)