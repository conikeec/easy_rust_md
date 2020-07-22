# Chapter 27
# The ? operator

There is an even shorter way to deal with Result (and Option), shorter than ```match``` and even shorter than ```if let```. It is called the "question mark operator", and is just ```?```. After a function that returns a result, you can add ```?```. This will:

* return the result if it is Ok
* pass the error back if it is Err

In other words, it does almost everything for you.

We can try this with ```.parse()``` again. We will write a function called ```parse_str``` that tries to turn a ```&str``` into a ```i32```. It looks like this:

```rust
fn parse_str(input: &str) -> Result<i32, std::num::ParseIntError> {
    let parsed_number = input.parse::<i32>()?; // Here is the question mark
    Ok(parsed_number)
}
```

This function takes a ```&str```. If it is Ok, it gives an ```i32``` wrapped in ```Ok```. If it is an Err, it returns a std::num::ParseIntError. Then we try to parse the number, and add ```?```. That means "check if it is an error, and give the result if it is okay". If it is not okay, it will return the error and end. But if it is okay, it will go to the next line. On the next line is the number inside of ```Ok()```. We need to wrap it in Ok because the return is ```Result<i32, std::num::ParseIntError>```, not ```i32```.

Now, we can try out our function. Let's see what it does with a vec of ```&str```s.

```rust
fn main() {
    let str_vec = vec!["Seven", "8", "9.0", "nice", "6060"];
    for item in str_vec {
        let parsed = parse_str(item);
        println!("{:?}", parsed);
    }
}

fn parse_str(input: &str) -> Result<i32, std::num::ParseIntError> {
    let parsed_number = input.parse::<i32>()?;
    Ok(parsed_number)
}
```

This prints:

```
Err(ParseIntError { kind: InvalidDigit })
Ok(8)
Err(ParseIntError { kind: InvalidDigit })
Err(ParseIntError { kind: InvalidDigit })
Ok(6060)
```

How did we find std::num::ParseIntError? One easy way is to "ask" the compiler again. 

```rust
fn main() {
    let failure = "Not a number".parse::<i32>();
    failure.rbrbrb(); // Compiler: "What is rbrbrb()???"
}
```

The compiler doesn't understand, and says:

```
error[E0599]: no method named `rbrbrb` found for enum `std::result::Result<i32, std::num::ParseIntError>` in the current scope
 --> src\main.rs:3:13
  |
3 |     failure.rbrbrb();
  |             ^^^^^^ method not found in `std::result::Result<i32, std::num::ParseIntError>`
```

So ```std::result::Result<i32, std::num::ParseIntError>``` is the signature we need.

We don't need to write ```std::result::Result``` because ```Result``` is always "in scope" (in scope = ready to use). But std::num::ParseIntError is not in scope. We can bring it in scope if we want:

```use std::num::ParseIntError;```

Then we can write:

```rust

use std::num::ParseIntError;

fn parse_str(input: &str) -> Result<i32, ParseIntError> {
    let parsed_number = input.parse::<i32>()?;
    Ok(parsed_number)
}
```

## When panic and unwrap are good

Rust has a ```panic!``` macro that you can use to make it panic. It is easy to use:

```rust
fn main() {
    panic!("Time to panic!");
}
```

The message ```"Time to panic!"``` displays when you run the program: ```thread 'main' panicked at 'Time to panic!', src\main.rs:2:3```

You will remember that ```src\main.rs``` is the directory and file name, and ```2:3``` is the line and column name. With this information, you can find the code and fix it.

```panic!``` is a good macro to use when writing your code to make sure that you know when something changes. For example, our function ```prints_three_things``` always prints index [0], [1], and [2] from a vector. It is okay because we always give it a vector with three items:

```rust
fn main() {
    let my_vec = vec![8, 9, 10];
    prints_three_things(my_vec);
}

fn prints_three_things(vector: Vec<i32>) {
    println!("{}, {}, {}", vector[0], vector[1], vector[2]);
}
```

But later on we write more and more code, and maybe we forget that ```my_vec``` can only be three things. Now ```my_vec``` in this part has six things:

```rust
fn main() {
  let my_vec = vec![8, 9, 10, 10, 55, 99]; // Now my_vec has six things
  prints_three_things(my_vec);
}

fn prints_three_things(vector: Vec<i32>) {
  println!("{}, {}, {}", vector[0], vector[1], vector[2]);
}
```

No error happens, because [0] and [1] and [2] are all inside. But maybe it was important to only have three things. So we should have done this:

```
fn main() {
    let my_vec = vec![8, 9, 10];
    prints_three_things(my_vec);
}

fn prints_three_things(vector: Vec<i32>) {
    if vector.len() != 3 {
        panic!("my_vec must always have three items") // will panic if the length is not 3
    }
    println!("{}, {}, {}", vector[0], vector[1], vector[2]);
}
```

Now we will know if the vector has six items:

```rust
fn main() {
    let my_vec = vec![8, 9, 10, 10, 55, 99];
    prints_three_things(my_vec);
}

fn prints_three_things(vector: Vec<i32>) {
    if vector.len() != 3 {
        panic!("my_vec must always have three items")
    }
    println!("{}, {}, {}", vector[0], vector[1], vector[2]);
}
```

This gives us ```thread 'main' panicked at 'my_vec must always have three items', src\main.rs:8:9```. Now we remember that ```my_vec``` should only have three items. So ```panic!``` is a good macro to create reminders in your code.

There are three other macros that are similar to ```panic!``` that you use a lot in testing. They are: ```assert!```, ```assert_eq!```, and ```assert_ne!```.

They mean:

* ```assert!()```: if the part inside ```()``` is not true, the program will panic.
*```assert_eq!()```: the two items inside ```()``` must be equal.
*```assert_ne!()```: the two items inside ```()``` must not be equal.


Some examples:

```rust
fn main() {
    let my_name = "Loki Laufeyson";

    assert!(my_name == "Loki Laufeyson");
    assert_eq!(my_name, "Loki Laufeyson");
    assert_ne!(my_name, "Mithridates");
}
```

This will do nothing, because all three assert macros are okay.

You can also add a message if you want.

```rust
fn main() {
    fn main() {
        let my_name = "Loki Laufeyson";

        assert!(
            my_name == "Loki Laufeyson",
            "{} should be Loki Laufeyson",
            my_name
        );
        assert_eq!(
            my_name, "Loki Laufeyson",
            "{} and Loki Laufeyson should be equal",
            my_name
        );
        assert_ne!(
            my_name, "Mithridates",
            "{} must not equal Mithridates",
            my_name
        );
    }
}
```

These messages will only display if the program panics. So if you run this:

```rust
fn main() {
    let my_name = "Mithridates";

    assert_ne!(
        my_name, "Mithridates",
        "{} must not equal Mithridates",
        my_name
    );
}
```

It will display:

```
thread 'main' panicked at 'assertion failed: `(left != right)`
  left: `"Mithridates"`,
 right: `"Mithridates"`: Mithridates must not equal Mithridates', src\main.rs:4:5
```

So it is saying "you said that left != right, but left == right". And it displays our message that says ```Mithridates must not equal Mithridates```.



```unwrap``` is also good when you want the program to crash when there is a problem. Later, when your code is finished it is good to change ```unwrap``` to something else that won't crash. You can also use ```expect```, which is like ```unwrap``` but with your own message.

This will crash:

```rust
fn main() {
    let my_vec = vec![9, 0, 10];
    let fourth = get_fourth(&my_vec);
}

fn get_fourth(input: &Vec<i32>) -> i32 {
    let fourth = input.get(3).unwrap();
    *fourth
}
```

The error message is ```thread 'main' panicked at 'called `Option::unwrap()` on a `None` value', src\main.rs:7:18```.

Now we write our own message with ```expect```:

```rust
fn main() {
    let my_vec = vec![9, 0, 10];
    let fourth = get_fourth(&my_vec);
}

fn get_fourth(input: &Vec<i32>) -> i32 {
    let fourth = input.get(3).expect("Input vector needs at least 4 items");
    *fourth
}
```

So the error message is ```thread 'main' panicked at 'Input vector needs at least 4 items', src\main.rs:7:18```.

You can also use ```unwrap_or``` if you want to always have a value that you choose.

```rust
fn main() {
    let my_vec = vec![8, 9, 10];

    let fourth = my_vec.get(3).unwrap_or(&0); // If .get doesn't work, we will make the value &0.
                                              // .get returns a reference, so we need &0 and not 0
                                              // to keep the types the same.
                                              // You can write "let *fourth" with a * if you want fourth to be
                                              // a 0 and not a &0, but here we just print so it doesn't matter

    println!("{}", fourth);
}
```

