# Chapter 18
# Control flow

Control flow means telling your code what to do in different situations. The simplest control flow is ```if```.

```
fn main() {
    let my_number = 5;
    if my_number == 7 {
        println!("It's seven");
    }
}
```

Please note that we wrote ```if my_number == 7``` and not ```if (my_number == 7)```. You don't need ```()``` with ```if``` in Rust.

```else if``` and ```else``` gives you more control:

```rust
fn main() {
    let my_number = 5;
    if my_number == 7 {
        println!("It's seven");
    } else if my_number == 6 {
        println!("It's six")
    } else {
        println!("It's a different number")
    }
}
```

You can add more conditions with ```&&``` (and) and ```||``` (or).

Too much ```if```, ```else```, and ```else if``` can be difficult to read. You can use ```match``` instead. But you must match for every possible result. For example, this will not work:

```rust
fn main() {
    let my_number: u8 = 5;
    match my_number {
        0 => println!("it's zero"),
        1 => println!("it's one"),
        2 => println!("it's two"),
    }
}
```

The compiler says:

```
error[E0004]: non-exhaustive patterns: `3u8..=std::u8::MAX` not covered
 --> src\main.rs:3:11
  |
3 |     match my_number {
  |           ^^^^^^^^^ pattern `3u8..=std::u8::MAX` not covered
```

This means "you told me about 0 to 2, but u8s can go up to 255. What about 3? What about 4? What about 5?" And so on. So you can add ```_``` which means "anything else".


```rust
fn main() {
    let my_number: u8 = 5;
    match my_number {
        0 => println!("it's zero"),
        1 => println!("it's one"),
        2 => println!("it's two"),
        _ => println!("It's some other number"),
    }
}
```

Remember this for match:

* You write ```match``` and then make a ```{}``` code block.
* Write the pattern on the left and use a ```=>``` fat arrow to say what to do when it matches.
* Each line is called an "arm".
* Put a comma between the arms (not a semicolon).

You can declare a value with a match:

```rust
fn main() {
    let my_number = 5;
    let second_number = match my_number {
        0 => 0,
        5 => 10,
        _ => 2,
    };
}
```

Do you see the semicolon at the end? That is because, after the match is over, we actually told the compiler this: ```let second_number = 10;```

You can match on more complicated things too. You use a tuple to do it.

```rust
fn main() {
    let sky = "cloudy";
    let temperature = "warm";

    match (sky, temperature) {
        ("cloudy", "cold") => println!("It's dark and unpleasant today"),
        ("clear", "warm") => println!("It's a nice day"),
        ("cloudy", "warm") => println!("It's dark but not bad"),
        _ => println!("Not sure what the weather is."),
    }
}
```

You can even put ```if``` inside of ```match```.

```rust
fn main() {
    let children = 5;
    let married = true;

    match (children, married) {
        (children, married) if married == false => println!("Not married with {} children", children),
        (children, married) if children == 0 && married == true => println!("Married but no children"),
        _ => println!("Married? {}. Number of children: {}.", married, children),
    }
}
```
