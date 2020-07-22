# Chapter 6
# Display and debug

Simple variables in Rust can be printed with ```{}``` inside ```println!()```. But some variables can't, and you need to **debug print**. Debug print is printing for the programmer, because it usually shows more information. Debug sometimes doesn't look pretty, because it has extra information to help you.

How do you know if you need ```{:?}``` and not ```{}```? The compiler will tell you. For example:

```rust
fn main() {
    let doesnt_print = ();
    println!("This will not print: {}", doesnt_print);
}
```

When we run this, the compiler says:

```
error[E0277]: `()` doesn't implement `std::fmt::Display`
 --> src\main.rs:3:41
  |
3 |     println!("This will not print: {}", doesnt_print);
  |                                         ^^^^^^^^^^^^ `()` cannot be formatted with the default formatter
  |
  = help: the trait `std::fmt::Display` is not implemented for `()`
  = note: in format strings you may be able to use `{:?}` (or {:#?} for pretty-print) instead
  = note: required by `std::fmt::Display::fmt`
  = note: this error originates in a macro (in Nightly builds, run with -Z macro-backtrace for more info)
```

This is a lot of information. But the important part is: ```you may be able to use `{:?}` (or {:#?} for pretty-print) instead```. This means that you can try ```{:?}```, and also ```{:#?}``` (```{:#?}``` prints with different formatting).

So Display means printing with ```{}```, and Debug means printing with ```{:?}```.

One more thing: you can also use print!() without ```ln``` if you don't want a new line.

```rust
fn main() {
    print!("This will not print a new line");
    println!(" so this will be on the same line");
}
```

This prints ```This will not print a new line so this will be on the same line```.

## Smallest and largest numbers

If you want to see the smallest and biggest numbers, you can use MIN and MAX.

```rust
fn main() {
    println!("The smallest i8 is {} and the biggest i8 is {}.", std::i8::MIN, std::i8::MAX);
    println!("The smallest u8 is {} and the biggest u8 is {}.", std::u8::MIN, std::u8::MAX);
    println!("The smallest i16 is {} and the biggest i16 is {}.", std::i16::MIN, std::i16::MAX);
    println!("The smallest u16 is {} and the biggest u16 is {}.", std::u16::MIN, std::u16::MAX);
    println!("The smallest i32 is {} and the biggest i32 is {}.", std::i32::MIN, std::i32::MAX);
    println!("The smallest u32 is {} and the biggest u32 is {}.", std::u32::MIN, std::u32::MAX);
    println!("The smallest i64 is {} and the biggest i64 is {}.", std::i64::MIN, std::i64::MAX);
    println!("The smallest u64 is {} and the biggest u64 is {}.", std::u64::MIN, std::u64::MAX);
    println!("The smallest i128 is {} and the biggest i128 is {}.", std::i128::MIN, std::i128::MAX);
    println!("The smallest u128 is {} and the biggest u128 is {}.", std::u128::MIN, std::u128::MAX);

}
```

This will print:
```rust
The smallest i8 is -128 and the biggest i8 is 127.
The smallest u8 is 0 and the biggest u8 is 255.
The smallest i16 is -32768 and the biggest i16 is 32767.
The smallest u16 is 0 and the biggest u16 is 65535.
The smallest i32 is -2147483648 and the biggest i32 is 2147483647.
The smallest u32 is 0 and the biggest u32 is 4294967295.
The smallest i64 is -9223372036854775808 and the biggest i64 is 9223372036854775807.
The smallest u64 is 0 and the biggest u64 is 18446744073709551615.
The smallest i128 is -170141183460469231731687303715884105728 and the biggest i128 is 170141183460469231731687303715884105727.  
The smallest u128 is 0 and the biggest u128 is 340282366920938463463374607431768211455.
```


