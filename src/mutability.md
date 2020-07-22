# Chapter 7
# Mutability (changing)

When you declare a variable with ```let```, it is immutable (cannot be changed).

This will not work:

```rust
fn main() {
    let my_number = 8;
    my_number = 10;
}
```

The compiler says: ```error[E0384]: cannot assign twice to immutable variable `my_number```. This is because variables are immutable if you only write ```let```.

To change a variable, add ```mut```:

```rust
fn main() {
    let mut my_number = 8;
    my_number = 10;
}
```

Now there is no problem.

However, you cannot change the type even with ```mut```. This will not work:

```rust
fn main() {
    let mut my_variable = 8;
    my_variable = "Hello, world!";
}
```

You will see the same "expected" message from the compiler: ```expected integer, found `&str````. ```&str``` is a string type that we will learn soon.

## Shadowing

Shadowing means using ```let``` to declare a new variable with the same name as another variable. It looks like mutability, but it is completely different. Shadowing looks like this:

```rust
fn main() {
    let my_number = 8; // This is an i32
    println!("{}", my_number); // prints 8
    let my_number = 9.2; // This is an f64. It is not my_number - it is completely different!
    println!("{}", my_number) // Prints 9.2
}
```

Here we say that we "shadowed" ```my_number``` with a new "let binding".

So is the first ```my_number``` destroyed? No, but when we call ```my_number``` we now get ```my_number``` the ```f64```. And because they are in the same scope block, we can't see the first ```my_number``` anymore.

But if they are in different blocks, we can see both. For example:

So when you shadow a variable, you don't destroy it. You **block** it.

```rust
fn main() {
    let my_number = 8; // This is an i32
    println!("{}", my_number); // prints 8
    {
        let my_number = 9.2; // This is an f64. It is not my_number - it is completely different!
        println!("{}", my_number) // Prints 9.2
                                  // But the shadowed my_number only lives until here.
                                  // The first my_number is still alive!
    }
    println!("{}", my_number); // prints 8
}
```

So what is the advantage of shadowing? Shadowing is good when you need to change a variable a lot.

```rust
fn main() {
    let final_number = {
        let y = 10;
        let x = 9; // x starts at 9
        let x = times_two(x); // shadow with new x: 18
        let x = x + y; // shadow with new x: 28
        x // return x: final_number is now the value of x
    };
    println!("The number is now: {}", final_number)
}

fn times_two(number: i32) -> i32 {
    number * 2
}
```

Without shadowing you would have to think of different names, even though you don't care about x:

```rust
fn main() {
	// Pretending we are using Rust without shadowing
    let final_number = {
        let y = 10;
        let x = 9; // x starts at 9
        let x_twice = times_two(x); // second name for x
        let x_twice_and_y = x + y; // third name for x
        x_twice_and_y // too bad we didn't have shadowing - we could have just used x
    };
    println!("The number is now: {}", final_number)
}

fn times_two(number: i32) -> i32 {
    number * 2
}
```
