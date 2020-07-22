# Chapter 4
# Type inference

Type inference means that if you don't tell the compiler the type, but it can decide by itself, it will decide. The compiler always needs to know the type of the variables, but you don’t always need to tell it. For example, for ```let my_number = 8```, ```my_number``` will be an ```i32```. That is because the compiler chooses i32 for integers if you don't tell it. But if you say ```let my_number: u8 = 8```, it will make ```my_number``` a ```u8```, because you told it ```u8```.

So usually the compiler can guess. But sometimes you need to tell it, for two reasons:
1) You are doing something very complex and the compiler doesn't know the type you want.
2) You want a different type (for example, you want an ```i128```, not an ```i32```).

To specify a type, add a colon after the variable name.
```rust
fn main() {
    let small_number: u8 = 10;
}
```

For numbers, you can say the type after the number. You don't need a space - just type it right after the number.

```rust
fn main() {
    let small_number = 10u8; // 10u8 = 10 of type u8
}
```

You can also add ```_``` if you want to make the number easy to read.
```rust
fn main() {
    let small_number = 10_u8; // This is easier to read
    let big_number = 100_000_000_i32; // 100 million is easy to read with _
}
```

The ```_``` does not change the number. It is only to make it easy for you to read. And it doesn't matter how many ```_``` you use:

```rust
fn main() {
    let number = 0________u8; 
    let number2 = 1___6______2____4______i32;
    println!("{}, {}", number, number2);
}
```

This prints ```0, 1624```.



## Floats

Floats are numbers with decimal points. 5.5 is a float, and 6 is an integer. 5.0 is also a float, and even 5. is a float.

```rust
fn main() {
    let my_float = 5.; // Rust sees . and knows that it is a float
}
```

But the types are not called ```float```, they are called ```f32``` and ```f64```. It is the same as integers: the number after ```f``` shows the number of bits. If you don't write the type, Rust will choose ```f64```.

Of course, only floats of the same type can be used together. So you can't add an f32 to an f64.

```rust
fn main() {
    let my_float: f64 = 5.0; // This is an f64
    let my_other_float: f32 = 8.5; // This is an f32

    let third_float = my_float + my_other_float;
}
```

When you try to run this, Rust will say:

```
error[E0308]: mismatched types
 --> src\main.rs:5:34
  |
5 |     let third_float = my_float + my_other_float;
  |                                  ^^^^^^^^^^^^^^ expected `f64`, found `f32`
```

The compiler writes '''expected (type), found (type)''' when you use the wrong type. It reads your code like this:

```rust
let my_float: f64 = 5.0; // The compiler sees an f64
let my_other_float: f32 = 8.5; // The compiler sees an f32. It is a different type.
let third_float = my_float + // The compiler sees a new variable. It must be an f64 plus another f64. Now it expects an f64...
let third_float = my_float + my_other_float; // But it found an f32. It can't add them.
```

So when you see "expected (type), found (type)", you must find why the compiler expected a different type.

Of course, with simple numbers it is easy to fix. You can cast the f32 to an f64 with ```as```:

```rust
fn main() {
    let my_float: f64 = 5.0;
    let my_other_float: f32 = 8.5;

    let third_float = my_float + my_other_float as f64; // my_other_float as f64 = use my_other_float like an f64
}
```

Or even more simply, remove the type declarations. Rust will choose types that can add together.

```rust
fn main() {
    let my_float = 5.0; // Rust will choose f64
    let my_other_float = 8.5; // Here again it will choose f64

    let third_float = my_float + my_other_float;
}
```

The Rust compiler is smart and will not choose f64 if you need f32:

```rust
fn main() {
    let my_float: f32 = 5.0;
    let my_other_float = 8.5; // Rust will choose f32,

    let third_float = my_float + my_other_float; // because it knows you need to add it to an f32
}
```

