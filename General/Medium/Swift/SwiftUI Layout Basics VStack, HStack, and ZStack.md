
![](https://miro.medium.com/v2/resize:fit:1400/1*f0p8zHFp0zKMfIZ61lwVuA.png)

## Learn how to arrange items easily with SwiftUI.

It does not matter which technology you are working with; arranging items is essential everywhere.

Arranging items means deciding whether to arrange them **vertically**, **horizontally**, or in **another direction.**

[

## Make Your SwiftUI Text Stand Out with AttributedString

### Ever wondered how to apply multiple styles to the same Text? Make Your SwiftUI Text Stand Out with AttributedString…

nameisjayant2.medium.com



](https://nameisjayant2.medium.com/make-your-swiftui-text-stand-out-with-attributedstring-40b3d7a2ed14?source=post_page-----c2a5b209e500---------------------------------------)

In this article, we will explore how to arrange items using **VStack**, **HStack**, and **ZStack** in **SwiftUI**.

Before jumping into each one, let us know what each one does.

- **VStack** — It is used to **organize items vertically.**
- **HStack** — It is used to **align the items horizontally.**
- **ZStack** — It is used to **arrange items on top of each other.**

> Let’s explore each one in detail

## VStack

As you know, **VStack** is used to arrange items **vertically**; basically, it places the items one below the other.

In simpler terms, it arranges the items from **top** to **bottom**.

  VStack{  
            Text("Text 1")  
            Text("Text 2")  
            Text("Text 3")  
            Text("Text 4")  
        }

**Output:**

![](https://miro.medium.com/v2/resize:fit:572/1*XHoIDduLw6BMu0SCGR_S3A.png)

This is a simple example of using VStack; it arranges items vertically (from top to bottom).

By default, items in the VStack are positioned at the **center of the screen**.

Initially, the width and height of the VStack are equal to the width and height of the items.

![](https://miro.medium.com/v2/resize:fit:1216/1*nSMjiQ31T0hJMYYt5kgxAQ.png)

Let’s set the Vstack to full width and height and align the items at the top center (the default arrangement is centered on the screen).

 VStack{  
            Text("Text 1")  
            Text("Text 2")  
            Text("Text 3")  
            Text("Text 4")  
        }  
        .frame(maxWidth: .infinity,maxHeight: .infinity,alignment: .top)  
        .background(.red)

**Output:**

![](https://miro.medium.com/v2/resize:fit:1156/1*HH1RiL-6KfACjsaOdZbTGQ.png)

==Using the frame modifier, set the maximum width and height to full, and align it to the top using the alignment property.==

`alignment` parameter has additional properties:

- **top** — Arrange at the top center.
- **topLeading** — Arrange at the top start.
- **topTrailing** — Arrange at the top end.
- **bottom** — Arrange at the bottom center.
- **bottomLeading** — Arrange at the bottom start.
- **bottomTrailing** — Arrange at the bottom end.
- **center** — This is the default one. Arrange it at the center of the screen.
- **leading** — Arrange at the center start.
- **trailing** — Arrange at the center end.

> Use any property according to your needs.

As you can see in the image below, there is minimal space between the items.

![](https://miro.medium.com/v2/resize:fit:484/1*qM1Mvx80d_DCkpZBMRyT-A.png)

Let’s give them some space between the items.

  VStack(spacing : 16){  
            Text("Text 1")  
            Text("Text 2")  
            Text("Text 3")  
            Text("Text 4")  
        }

**Output:**

![](https://miro.medium.com/v2/resize:fit:484/1*2n5aIticO32qgYCAK2BSxA.png)

Using the **spacing** parameter, we set a **16 pt** gap between all items.

Let’s analyze the image output below:

![](https://miro.medium.com/v2/resize:fit:612/1*Z8kQpga0McPPT9scMMor7g.png)

As you can see, every item aligns with the center.

Let's get it aligned to the left.

  VStack(alignment: .leading,spacing: 16){  
            Text("Hello world.")  
            Text("How are you bro?")  
            Text("Swift UI")  
            Text("Text 4")  
        }

**Output:**

![](https://miro.medium.com/v2/resize:fit:540/1*_HStayJyILjqwROFSHrdXw.png)

We have the property called '**alignment**' in VStack; '**leading**' means it aligns from the start.

Similarly, we have another alignment property called **trailing** that aligns items at the end.

![](https://miro.medium.com/v2/resize:fit:580/1*KI336MTr2psS1T-rbZ4SFA.png)

Let’s move on to the last part of **VStack**.

Can we arrange items like this: one at the **top**, one in the **center**, and one at the **bottom** of the screen?

![](https://miro.medium.com/v2/resize:fit:616/1*EwtvYiDFP4AX4hgkQMVXIg.png)

It’s a simple technique: place the **Spacer()** view between the items.

 VStack(alignment: .trailing,spacing: 16){  
            Text("Text 1")  
            Spacer()  
            Text("Text 2")  
            Spacer()  
            Text("Text 3")  
        }  
        .frame(maxWidth: .infinity,maxHeight: .infinity)  
        .background(.red)

## HStack

As you know, **HStack** is used to arrange items **horizontally**; basically, it places items **side by side**.

In simpler terms, it arranges the items from **left** to **right**.

  HStack(){  
            Text("Text 1")  
            Text("Text 2")  
            Text("Text 3")  
        }

**Output:**

![](https://miro.medium.com/v2/resize:fit:860/1*sIB-x0sVeOYOzoUe3NrOqA.png)

This is a simple example of using **HStack**; it arranges items horizontally (from left to right).

By default, items in the **HStack** are positioned at the **center** of the screen.

Initially, the width and height of the HStack are equal to the width and height of the items.

![](https://miro.medium.com/v2/resize:fit:792/1*hjEiJrCU8BJcut6-yQ1lpA.png)

Let’s set the **HStack** to full width and height and align the items at the top center (the default arrangement is centered on the screen).

HStack(){  
            Text("Text 1")  
            Text("Text 2")  
            Text("Text 3")  
        }  
        .frame(maxWidth: .infinity,maxHeight: .infinity,alignment: .top)  
        .background(.red)

**Output:**

![](https://miro.medium.com/v2/resize:fit:1100/1*qVH_tAxs9_hfGmd6wuzTkg.png)

Using the frame modifier, set the maximum width and height to full, and align it to the top using the alignment property.

`alignment` parameter has additional properties:

- **top** — Arrange at the top center.
- **topLeading** — Arrange at the top start.
- **topTrailing** — Arrange at the top end.
- **bottom** — Arrange at the bottom center.
- **bottomLeading** — Arrange at the bottom start.
- **bottomTrailing** — Arrange at the bottom end.
- **center** — This is the default one. Arrange it at the center of the screen.
- **leading** — Arrange at the center start.
- **trailing** — Arrange at the center end.

> Use any property according to your needs.

As you can see in the image below, there is minimal space between the items.

![](https://miro.medium.com/v2/resize:fit:788/1*c-8R9rs6jEDGD9EpnKY-Qg.png)

Let’s give them some space between the items.

 HStack(  
            spacing:100  
        ){  
            Text("Text 1")  
            Text("Text 2")  
            Text("Text 3")  
        }

**Output:**

![](https://miro.medium.com/v2/resize:fit:1216/1*5S5UW9I2OT3rWg5FLBg4Pg.png)

Using the **spacing** parameter, we set a **100 pt** gap between all items.

Let’s analyze the image output below:

![](https://miro.medium.com/v2/resize:fit:888/1*OEXbRTdY1WXLLHZxOmjMEw.png)

As you can see, every item aligns with the center.

Let’s align it with the top.

 HStack(  
            alignment:.top,  
            spacing : 16  
        ){  
            Text("Text 1\nHi")  
            Text("Text 2\nhello\nhi")  
            Text("Text 3")  
        }

**Output:**

![](https://miro.medium.com/v2/resize:fit:780/1*tJI3xT__P-_S0traWA8MEg.png)

We have a property called ‘**alignment**’ in **HStack**; ‘**top**’ means it aligns with the top.

Similarly, we have another alignment property called '**bottom**' that aligns items to the bottom.

 HStack(  
            alignment:.bottom,  
            spacing : 16  
        ){  
            Text("Text 1\nHi")  
            Text("Text 2\nhello\nhi")  
            Text("Text 3")  
        }

**Output:**

![](https://miro.medium.com/v2/resize:fit:700/1*Gol78dH1sjHfFyK1qsZxag.png)

Let’s move on to the last part of HStack.

Can we arrange items like this: one at the **left**, one in the **center**, and one at the **right** of the screen?

![](https://miro.medium.com/v2/resize:fit:1284/1*nBGYwheYP5Dw9qOEHXPYpA.png)

It’s a simple technique: place the **Spacer()** view between the items.

 HStack(  
        ){  
            Text("Text 1")  
            Spacer()  
            Text("Text 2")  
            Spacer()  
            Text("Text 3")  
        }

## ZStack

When we place items here, each item **aligns** on **top of the others.**

    ZStack(  
        ){  
            Text("Text 1 is here")  
            Text("Text 2")  
            Text("Text 3")  
        }

**Output:**

![](https://miro.medium.com/v2/resize:fit:812/1*VrtzguDEq6pAXq1sU9gYOg.png)

As you can see, each item overlaps the one before it.

Let’s set the **ZStack** frame to maximum width and height, and also align the items at the **top center** (by default, it’s aligned at the center of the screen).

 ZStack(  
        ){  
            Text("Text 1 is here")  
            Text("Text 2")  
            Text("Text 3")  
        }  
        .frame(maxWidth: .infinity,maxHeight: .infinity,alignment: .top)  
        .background(.red)

**Output:**

![](https://miro.medium.com/v2/resize:fit:1152/1*FKEq09-KAgaoH7pSNtgUCQ.png)

Using the frame modifier, set the maximum width and height to full, and align it to the top using the alignment property.

`alignment` parameter has additional properties:

- **top** — Arrange at the top center.
- **topLeading** — Arrange at the top start.
- **topTrailing** — Arrange at the top end.
- **bottom** — Arrange at the bottom center.
- **bottomLeading** — Arrange at the bottom start.
- **bottomTrailing** — Arrange at the bottom end.
- **center** — This is the default one. Arrange it at the center of the screen.
- **leading** — Arrange at the center start.
- **trailing** — Arrange at the center end.

> Use any property according to your needs.

By default, every item is placed on top of each other; let’s rearrange all the items across the screen.

 ZStack(  
        ){  
            Text("Text 1 is here")  
            Text("Text 2")  
                .frame(maxWidth: .infinity,maxHeight: .infinity,alignment: .top)  
            Text("Text 3")  
                .frame(maxWidth: .infinity,maxHeight: .infinity,alignment: .trailing)  
        }  
        .frame(maxWidth: .infinity,maxHeight: .infinity,alignment: .top)  
        .background(.red)

**Output:**

![](https://miro.medium.com/v2/resize:fit:1176/1*zCGA5Rid6GlZCPiYN3jCqQ.png)

As you can see, the first item is positioned at the **center** (by default), the second item is positioned at the **top center**, and the third item is positioned at the **center right** of the screen.

Let’s look at a straightforward real-world example of using **ZStack**.

 ZStack(  
            alignment : .top  
        ){  
            Image("Image")  
                .resizable()  
                .frame(width: 300,height: 300)  
              
            Text("This is Image")  
                .foregroundColor(.red)  
                .font(.title)  
                .bold()  
        }

**Output:**

![](https://miro.medium.com/v2/resize:fit:1020/1*du5-yFGeVmHw5XLijXlXbw.png)

In the **ZStack**, we have an **Image** and a **Text**. By default, the text is placed at the center of the image. Using the **alignment** property, we position it at the top center of the screen. You can place it in any position with the alignment property.

_Thank you for reading until the end. Before you go:_

- _Please consider_ **_clapping_** _and_ **_following_** _👏_
- _Follow us on_ [**_Twitter(X)_**](https://x.com/nameisjayant)_,_ [**_LinkedIn_**](https://www.linkedin.com/in/jayant-kumar-%F0%9F%87%AE%F0%9F%87%B3-262597171/)_,_ [**_Substack_**](https://nameisjayant.substack.com/), _and_ [**_YouTube_**](https://www.youtube.com/@ProgrammingSimplified0)**_._**