# Chapter 9
# Strings

Rust has two main types of strings: ```String``` and ```&str```. What is the difference?

* ```&str``` is a simple string. When you write ```let my_variable = "Hello, world!"```, you create a ```&str```. A ```&str``` is very fast.
* ```String``` is a more complicated string. It is a bit slower, and has more functions. A ```String``` is a pointer, with data on the heap.

Both ```&str``` and ```String``` are UTF-8. For example, you can write:

```rust
fn main() {
    let name = "서태지"; // This is a Korean name. No problem, because a &str is UTF-8.
    let other_name = String::from("Adrian Fahrenheit Țepeș"); // Ț and ș are no problem in UTF-8.
}
```

You can even write emojis thanks to UTF-8.

```rust
fn main() {
    let name = "😂";
    println!("My name is actually {}", name);   
}
```

So why do we need a ```&``` in front of ```str```, but not for ```String```?

* ```str``` is a dynamically sized type (dynamically sized = the size can be different). For example, the names "서태지" and "Adrian Fahrenheit Țepeș" are not the same size on the stack:

```rust
fn main() {

    println!("A String is always {:?} bytes. It is Sized.", std::mem::size_of::<String>()); // std::mem::size_of::<Type>() gives you the size in bytes of a type
    println!("And an i8 is always {:?} bytes. It is Sized.", std::mem::size_of::<i8>());
    println!("And an f64 is always {:?} bytes. It is Sized.", std::mem::size_of::<f64>());
    println!("But a &str? It can be anything. '서태지' is {:?} bytes. It is not Sized.", std::mem::size_of_val("서태지")); // std::mem::size_of_val() gives you the size in bytes of a variable
    println!("And 'Adrian Fahrenheit Țepeș' is {:?} bytes. It is not Sized.", std::mem::size_of_val("Adrian Fahrenheit Țepeș"));
    
}
```

That is why we need a &, because ```&``` makes a pointer, and Rust knows the size of the pointer. So the pointer goes on the stack. If we wrote ```str```, Rust wouldn't know what to do because it doesn't know the size.

There are many ways to make a ```String```. Here are some:

* ```String::from("This is the string text");``` This a method for String that takes text and creates a String.
* ```"This is the string text".to_string()```. This is a method for &str that makes it a String.
* The ```format!``` macro. This is like ```println!``` except it creates a String instead of printing. So you can do this:

```rust
fn main() {
    let my_name = "Billybrobby";
    let my_country = "USA";
    let my_home = "Korea";

    let together = format!(
        "I am {} and I come from {} but I live in {}.",
        my_name, my_country, my_home
    );
}
```

Now we have a string named together but did not print it yet.

One other way to make a String is called ```.into()``` but it is a bit different. ```.into()``` takes a reference type and makes it a non-reference type. In other words, it makes a non-owned type an owned type. ```&str``` does not own its data because it is just a reference, but ```String``` owns its data. But String is not the only owned type, so this won't work:

```rust
fn main() {
    let my_string = "Try to make this a String".into();
}
```

Rust doesn't know what type you want.

```rust
error[E0282]: type annotations needed
 --> src\main.rs:2:9
  |
2 |     let my_string = "Try to make this a String".into();
  |         ^^^^^^^^^ consider giving `my_string` a type
```

So you can do this:

```rust
fn main() {
    let my_string: String = "Try to make this a String".into();
}
```

And now you get a String.
