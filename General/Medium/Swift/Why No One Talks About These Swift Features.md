
Let’s get started!

## 1. Pattern Matching with where clause

**`where`** means adding an **extra condition**, when it’s true, then the case will be executed.

Let’s understand with an example;

switch value {  
    case let x where x > 100:  
        print("Large number")  
          
    case let x where x < 0:  
        print("Negative number")  
          
    default:  
        print("Normal")  
    }

Here, in the switch cases, we added a **`where`** clause that checks additional conditions.

If we don’t have a **`where`** clause, then we have to write like this;

switch value {  
case let x:  
    if x > 100 {  
        print("Large number")  
    } else if x < 0 {  
        print("Negative number")  
    } else {  
        print("Normal")  
    }  
}

This makes the code weird and more challenging to read.

## 2. Match Multiple Patterns in a Single Case

If **multiple patterns** have the same output, it’s better to put them in a single case like this;

enum Direction {  
    case NORTH  
    case SOUTH  
    case EAST  
    case WEST  
}  
  
  
func checkDirection(direction:Direction){  
      
    switch direction {  
    case .NORTH,.SOUTH:  
        print("Vertical Direction")  
          
    case .EAST, .WEST:  
        print("Horizontal Direction")  
    }  
}

As you can see, `**North**` and `**South**` have the same logic, and `East` and `West` are the same, so put them in the same cases.

## 3. Use _ to Ignore Values Explicitly

**_ (underscore)** means ignoring or not caring about the value.

Suppose we have this enum,

enum ApiResult {  
    case success(data: String)  
    case failure(error: String)  
}

You don’t want to care about the `**data**` and `**error**`; you only want to check whether the request **succeeds** or **fails**.

Then you can use **_(underscore)** in place of data and error like this,

func CheckResponse(result:ApiResult){  
      
    switch result {  
    case .success(_):  
        print("Request succeeded")  
    case .failure(_):  
        print("Request failed")  
    }  
}

Here, no need of **`data`** and **`error`**, that’s why I added **_(underscore).**

If you care about values, then write like this:

func CheckResponse(result:ApiResult){  
      
    switch result {  
    case .success(let data):  
        print("Request succeeded \(data)")  
    case .failure(let error):  
        print("Request failed \(error)")  
    }  
}

## 4. Match Optionals Without Unwrapping

As we all know, optionals may have a **value** or be **nil**.

var age: Int? = 25    
var score: Int? = nil

Most developers unwrap the optionals like this;

if let number = value {  
    print(number)  
} else {  
    print("No value")  
}

That’s fine, but Swift provides another way to do this.

**Optionals** are just an **enum**, enum defines optional like this;

// this is an inbuilt code, no need to write it.  
enum Optional<T> {  
    case some(T)  
    case none  
}

Let’s understand with an example;

func getResult(){  
      
    let value: Int? = 10  
      
    switch value {  
    case .some(let number):  
        print("Number is \(number)")  
  
    case .none:  
        print("No value")  
    }  
}

In **case 1**, if the value is any number, the case will be executed; otherwise, it will fall into **case 2.**

## 5. Pattern Matching Reads Better Than Logic

Instead of writing the logic like this;

if x == 0 || x == 1 || x == 2 {  
      
}

Write like this;

switch x {  
case 0...2:  
      // logic  
default:  
    break  
}

This is much more readable and has less boilerplate code.

_Thank you for reading until the end. Before you go:_

- _Please consider_ **_clapping_** _and_ **_following_** _👏_
- _Follow us on_ [**_Twitter(X)_**](https://x.com/nameisjayant)_,_ [**_LinkedIn_**](https://www.linkedin.com/in/jayant-kumar-%F0%9F%87%AE%F0%9F%87%B3-262597171/)_,_ [**_Substack_**](https://nameisjayant.substack.com/), _and_ [**_YouTube_**](https://www.youtube.com/@ProgrammingSimplified0)**_._**