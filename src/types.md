# Chapter 2
# Types

## Primitive types
Rust has simple types that are called **primitive types**. We will start with integers. Integers are whole numbers with no decimal point. There are two types of integers:

- Signed integers,
- Unsigned integers.

Signs means ```+``` (plus sign) and ```-``` (minus sign), so signed integers can be positive or negative (e.g. +8, -8). But unsigned integers can only be positive, because they do not have a sign.

The signed integers are: ```i8```, ```i16```, ```i32```, ```i64```, ```i128```, and ```isize```.
The unsigned integers are: ```u8```, ```u16```, ```u32```, ```u64```, ```u128```, and ```usize```.

The number after the i or the u means the number of bits for the number, so numbers with more bits can be larger. 8 bits = one byte, so i8 is one byte, i64 is 8 bytes, and so on. Number types with larger sizes can hold larger numbers. For example, a u8 can hold up to 255, but a u16 can hold up to 65535. And a u128 can hold up to 340282366920938463463374607431768211455.

So what is ```isize``` and ```usize```? This means the number of bits on your type of computer. (This is called the **architecture** of your computer.) So ```isize``` and ```usize``` on a 32-bit computer is like ```i32``` and ```u32```, and ```isize``` and ```usize``` on a 64-bit computer is like ```i64``` and ```u64```.

There are many reasons for the different types of integers. One reason is computer performance: a smaller number of bytes is faster to process. But here are some other uses:

Characters in Rust are called ```char```. ```u8``` numbers only go up to 255, and Unicode and ASCII use the same numbers for these numbers. This means that Rust can safely **cast** a ```u8``` into a ```char```, using ```as```. (Cast ```u8``` as ```char``` means "pretend ```u8``` is a ```char```")

Casting with ```as``` is useful because Rust always needs to know the type of the integer. For example, this will not compile:

```rust
fn main() {
    let my_number = 100; // We didn't write a type of integer, 
                         // so Rust chooses i32. Rust always
			 // chooses i32 for integers if you don't
			 // tell it to use a different type
    println!("{}", my_number as char);

}
```

Here is the reason:

```
error[E0604]: only `u8` can be cast as `char`, not `i32`
 --> src\main.rs:3:20
  |
3 |     println!("{}", my_number as char);
  |                    ^^^^^^^^^^^^^^^^^
```

One easy way to fix this is with ```as```. First we use ```as``` to make my_number a ```u8```, then one more ```as``` to make it a ```char```. Now it will compile:

```rust
fn main() {
    let my_number = 100;
    println!("{}", my_number as u8 as char);
}
```
