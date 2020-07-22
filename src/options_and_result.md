# Chapter 26
# Option and Result

We understand enums and generics now, so we can understand ```Option``` and ```Result```. Rust uses these two enums to make code safer. We will start with Option.

You use Option when you have a value that might exist, or might not exist. Here is an example of bad code that can be improved with Option.

```rust
fn main() {
    let new_vec = vec![1, 2];
    let index = take_fifth(new_vec);
}

fn take_fifth(value: Vec<i32>) -> i32 {
    value[4]
}
```

When we run the code, it panics. Here is the message:

```
thread 'main' panicked at 'index out of bounds: the len is 2 but the index is 4', src\main.rs:34:5
```

Panic means that the program stops before the problem happens. Rust sees that the function wants something impossible, and stops. It "unwinds the stack" (takes the values off the stack) and tells you "sorry, I can't do that".

So now we will change the return type from ```i32``` to ```Option<i32>```. This means "give me an i32 if it's there, and give me ```None``` if it's not". We say that the i32 is "wrapped" in an Option, which means that it's inside an Option.

```rust
fn main() {
    let new_vec = vec![1, 2];
    let bigger_vec = vec![1, 2, 3, 4, 5];
    println!("{:?}, {:?}", take_fifth(new_vec), take_fifth(bigger_vec)
    );
}

fn take_fifth(value: Vec<i32>) -> Option<i32> {
    if value.len() < 5 { // .len() gives the length of the vec.
                         // It must be at least 5.
        None
    } else {
        Some(value[4])
    }
}
```

This prints ```None, Some(5)```. This is good, because now we don't panic anymore. But how do we get the value 5?

We can get the value inside an option with ```.unwrap()```, but be careful with ```.unwrap()```. If you unwrap a value that is ```None```, the program will panic.

```rust
fn main() {
    let new_vec = vec![1, 2];
    let bigger_vec = vec![1, 2, 3, 4, 5];
    println!( // with .unwrap() the code is getting longer
              // so we will write on more than one line.
        "{:?}, {:?}",
        take_fifth(new_vec).unwrap(), // this one is None. .unwrap() will panic!
        take_fifth(bigger_vec).unwrap()
    );
}

fn take_fifth(value: Vec<i32>) -> Option<i32> {
    if value.len() < 4 {
        None
    } else {
        Some(value[4])
    }
}
```

But we don't need to use ```.unwrap()```. We can use a ```match```, and print when we have ```Some```, and not print if we have ```None```. For example:

```rust
fn main() {
    let new_vec = vec![1, 2];
    let bigger_vec = vec![1, 2, 3, 4, 5];
    let mut option_vec = Vec::new(); // Make a new vec to hold our options
                                     // The vec is type: Vec<Option<i32>>. That means a vec of Option<i32>.

    option_vec.push(take_fifth(new_vec)); // This pushes "None" into the vec
    option_vec.push(take_fifth(bigger_vec)); // This pushes "Some(5)" into the vec

    handle_option(option_vec); // handle_option looks at every option in the vec.
                               // It prints the value if it is Some. It doesn't print if it is None.
}

fn take_fifth(value: Vec<i32>) -> Option<i32> {
    if value.len() < 4 {
        None
    } else {
        Some(value[4])
    }
}

fn handle_option(my_option: Vec<Option<i32>>) { 
  for item in my_option {
    match item {
      Some(number) => println!("{}", number),
      None => {}, // do nothing
    }
  }
}
```

Because we know generics, we can understand Option. It looks like this:

```rust
enum Option<T> {
    None,
    Some(T),
}
```

The important point to remember: with ```Some```, you have a value of type ```T``` (any type). But with ```None```, you don't have anything. So in a ```match``` statement for Option you can't say:

```rust
Some(value) => println!("The value is {}", value), 
None(value) => println!("The value is {}", value), 
```

because ```None``` is just ```None```.

Of course, there are easier ways to use Option. In this easier way, we don't need ```handle_option()``` anymore. We also don't need a vec for the Options.

```rust
fn main() {
    let new_vec = vec![1, 2];
    let bigger_vec = vec![1, 2, 3, 4, 5];
    let vec_of_vecs = vec![new_vec, bigger_vec];
    for vec in vec_of_vecs {
        let inside_number = take_fifth(vec);
        if inside_number.is_some() { // .is_some() returns true if we get Some, false if we get None
            println!("{}", inside_number.unwrap()); // now it is safe to use .unwrap() because we already checked
                                                    // println! does not happen if we have a None
        }
    }
}

fn take_fifth(value: Vec<i32>) -> Option<i32> {
    if value.len() < 4 {
        None
    } else {
        Some(value[4])
    }
}
```

## Result 

Result is similar to Option, but here is the difference:

* Option is about Some or None (value or no value),
* Result is about Ok or Err (okay result, or error result).

To compare, here are the signatures for Option and Result.

```rust
enum Option<T> {
    None,
    Some(T),
}

enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

So Result has a value inside of Ok, and a value inside of Err. That is because errors usually have information inside them.

```Result<T, E>``` means you need to think of what you want to return for Ok, and what you want to return for Err. Actually, you can decide anything. Even this is okay:

```rust
fn main() {
    check_error();
}

fn check_error() -> Result<(), ()> {
    Ok(()) 
}
```

```check_error``` says "return () if we get Ok, and return () if we get Err". Then we return Ok with a ().

Sometimes a function with Result will use a ```String``` for the Err value. This is not the best method to use, but sometimes it is okay.

```rust
fn main() {
    let mut result_vec = Vec::new(); // Create a new vec for the results

    for number in 2..7 {
        result_vec.push(check_if_five(number)); // push each result into the vec
    }

    println!("{:?}", result_vec);
}

fn check_if_five(number: i32) -> Result<i32, String> {
    match number {
        5 => Ok(number),
        _ => Err("Sorry, the number wasn't five.".to_string()), // This is our error message
    }
}
```

Our vec prints:

```
[Err("Sorry, the number wasn\'t five."), Err("Sorry, the number wasn\'t five."), Err("Sorry, the number wasn\'t five."), Ok(5), 
Err("Sorry, the number wasn\'t five.")]
```

Just like Option, .unwrap() on Err will panic.

```rust
fn main() {
    let error_value: Result<i32, &str> = Err("There was an error"); // Create a Result that is already an Err
    println!("{}", error_value.unwrap()); // Unwrap it
}
```

The program panics, and prints:

```
thread 'main' panicked at 'called `Result::unwrap()` on an `Err` value: "There was an error"', src\main.rs:30:20
```

This information helps you fix your code. src\main.rs:30:20 means "inside main.rs in directory src, on line 30 and column 20". So you can go there to look at your code and fix the problem.

You can also create your own error types. Result functions in the standard library usually have their own error types. For example:

```
pub fn from_utf8(vec: Vec<u8>) -> Result<String, FromUtf8Error>
```

This function take a vector of bytes (u8) and tries to make a ```String```. So the success case for the Result is a ```String``` and the error case is ```FromUtf8Error```. You can give your error type any name you want. We will create our own error types later, because first we need to learn other things.

Using a ```match``` with Option and Result sometimes requires a lot of code. For example, the ```.get()``` method returns an Option on a Vec.

```rust
fn main() {
    let my_vec = vec![2, 3, 4];
    let get_one = my_vec.get(0); // 0 to get the first number
    let get_two = my_vec.get(10); // Returns None
    println!("{:?}", get_one);
    println!("{:?}", get_two);
}
```

This prints 
```
Some(2)
None
```

So now we can match to get the values. Let's use a range from 0 to 10 to see if it matches the numbers in ```my_vec```.

```rust
fn main() {
    let my_vec = vec![2, 3, 4];

    for index in 0..10 {
      match my_vec.get(index) {
        Some(number) => println!("The number is: {}", number),
        None => {}
      }
    }
    
}
```

This is good, but we don't do anything for ```None```. Here we can make the code smaller by using ```if let```. ```if let``` means "do something if it matches, and don't do anything if it doesn't". ```if let``` is when you don't care about matching for everything.

```rust
fn main() {
    let my_vec = vec![2, 3, 4];

    for index in 0..10 {
      if let Some(number) = my_vec.get(index) {
        println!("The number is: {}", number);
      }
    }   
}
```

```if let Some(number) = my_vec.get(index)``` means "if you get Some(number) from my_vec.get(index)". Also note: it uses one ```=```. It is not a boolean.

```while let``` is like a while loop for ```if let```. Imagine that we have weather station data like this:

```
["Berlin", "cloudy", "5", "-7", "78"]
["Athens", "sunny", "not humid", "20", "10", "50"]
```

We want to get the numbers, but not the words. For the numbers, we can use a method called ```parse::<i32>()```. ```parse()``` is the method, and ```::<i32>``` is the type. It will try to turn the &str into an ```i32```, and give it to us if it can.

We will also use ```.pop()```. This takes the last item off of the vector.

```rust
fn main() {
    let mut weather_vec = vec!["Berlin", "cloudy", "5", "-7", "78"];
    while let Some(information) = weather_vec.pop() { // This means: keep going until you can't pop anymore
                                                      // When the vector reaches 0 items, it will return None
                                                      // and it will stop.
        if let Ok(number) = information.parse::<i32>() { // Try to parse the variable we called information
                                                         // This returns a result. If it's Ok(number), it will print it
            println!("The number is: {}", number);
        }
    }
}
```
