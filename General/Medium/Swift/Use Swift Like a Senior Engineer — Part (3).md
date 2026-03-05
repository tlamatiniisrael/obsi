
This is the continuation of the previous article on [**_Use Swift Like a Senior Engineer._**](https://medium.com/ios-lab/use-swift-like-a-senior-engineer-part-2-feb014be5731)

[

## Use Swift Like a Senior Engineer — Part (2)

### These Swift tricks will think you like a Senior Engineer

medium.com



](https://medium.com/ios-lab/use-swift-like-a-senior-engineer-part-2-feb014be5731?source=post_page-----f8fb71d825ba---------------------------------------)

Let’s get started!

## 1. Substring Without Memory Leaks

Imagine we have a string;

let text = "Hello, Swift"

Now you take some part of this string;

let sub = text.prefix(5) // "Hello"

The **`sub`** is not a new string; it’s pointing to the **original string (text).**

The **original string (text)** will remain in memory until you free it.

Swift cannot free the **original string (text)**, because **`sub`** still depends on it.

This can cause a **memory leak.**

To avoid, use this way;

let text = "Hello, Swift"  
let sub = text.prefix(5)  
let safeString = String(sub)

**string(sub)** creates a new string, and it copies the **“Hello”.**

The **original string (text)** will now be released from memory.

## 2. Swift Strings Are **Collections**, Not Arrays

When we have to **find any character from a string** in Swift, we think like this;

let text = "Swift"  
print(text[0]) // compile time error

It will give a compile-time error.

**Don’t treat strings as an array of characters;**

The correct way is like this;

let text = "Swift"  
let firstChar = text[text.startIndex]  
print(firstChar) // S

## 3. raw string

When we have to write **JSON** data, we usually write it like this;

let userData = "{\"name\":\"Jayant\"}"

This is acceptable but not very readable and may confuse.

It’s better to use it like this.

let userData = #"{"name":"Jayant"}"#

This is much more readable.

## 4. Convert Between String & Numbers Safely

This is how we can convert a string safely into a number.

let value:String? = "123"  
  
if let number = Int(value) {  
    print(number + 10)  
}

It will convert the string into an integer and print the result.

If the value is not a number, the **if block** will not execute.

Avoid **force-unwrapping like this.**

print(Int(value)! + 10) // this may cause crash

It can cause a **crash** if the value is not a number.

## 5. Multiline Strings

The old way of writing **multi-line** text like this;

let json = "{\n  \"name\": \"Swift\",\n  \"year\": 2026\n}"

This is fine, but instead, write like this;

let json = """  
{  
  "name": "Swift",  
  "year": 2026  
}  
"""

This looks more readable.

_Thank you for reading until the end. Before you go:_

- _Please consider_ **_clapping_** _and_ **_following_** _👏_
- _Follow us on_ [**_Twitter(X)_**](https://x.com/nameisjayant)_,_ [**_LinkedIn_**](https://www.linkedin.com/in/jayant-kumar-%F0%9F%87%AE%F0%9F%87%B3-262597171/)_,_ [**_Substack_**](https://nameisjayant.substack.com/), _and_ [**_YouTube_**](https://www.youtube.com/@ProgrammingSimplified0)**_._**