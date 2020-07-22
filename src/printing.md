# Chapter 5
# Printing hello, world!

A new Rust program always starts with this:

```rust
fn main() {
    println!("Hello, world!");
}
```

* ```fn``` means function,
* ```main``` is the function that starts the program,
* ```()``` means that we didn't give the function anything to start.

```{}``` is a **code block**.

```println!``` is a **macro** that prints to the console. A **macro** is like a function that writes code for you. Macros have a ```!``` after them. We will learn about making macros later. For now, remember that ```!``` means that it is a macro.

To learn about the ```;```, we will create another function. First, in ```main``` we will print a number 8:

```rust
fn main() {
    println!("Hello, world number {}!", 8);
}
```

The ```{}``` in ```println!``` means "put the variable inside here". This prints ```Hello world number 8```.

We can put more in:

```rust
fn main() {
    println!("Hello, worlds number {} and {}!", 8, 9);
}
```

This prints ```Hello, worlds number 8 and 9!```.

Now let's create the function.

```rust
fn main() {
    println!("Hello, world number {}", number());
}

fn number() -> i32 {
    8
}
```

This also prints ```Hello, world number 8!```. When Rust looks at ```number()``` it sees a function. This function:
- Does not take anything (because it has ```()```)
- Returns an ```i32```. The ```->``` (called a "skinny arrow") shows what the function returns.

Inside the function is just ```8```. Because there is no ```;```, this is the value it returns. If it had a ```;```, it would not return anything. Rust will not compile this if it has a ```;```, because the return is ```i32``` and ```;``` returns ```()```, not ```i32```:

```rust
fn main() {
    println!("Hello, world number {}", number());
}

fn number() -> i32 {
    8;
}
```

```rust
5 | fn number() -> i32 {
  |    ------      ^^^ expected `i32`, found `()`
  |    |
  |    implicitly returns `()` as its body has no tail or `return` expression
6 |     8;
  |      - help: consider removing this semicolon
```

This means "you told me that ```number()``` returns an ```i32```, but you added a ```;``` so it doesn't return anything". So the compiler suggests removing the semicolon.

You can also write ```return 8;``` but in Rust it is normal to just remove the ```;``` to ```return```.

When you want to give variables to a function, put them inside the ```()```. You have to give them a name and write the type.

```rust
fn main() {
    multiply(8, 9); // We can give the numbers directly
    let some_number = 10; // Or we can declare two variables
    let some_other_number = 2;
    multiply(some_number, some_other_number); // and put them in the function
}

fn multiply(number_one: i32, number_two: i32) { // Two i32s will enter the function. We will call them number_one and number_two.
    let result = number_one * number_two;
    println!("{} times {} is {}", number_one, number_two, result);
}
```

Of course, we can also return an i32:

```rust
fn main() {
    let multiply_result = multiply(8, 9); // We used multiply() to print and to give the result to multiply_result
}

fn multiply(number_one: i32, number_two: i32) -> i32 {
    let result = number_one * number_two;
    println!("{} times {} is {}", number_one, number_two, result);
    result // this is the i32 that we return
}
```

## Declaring variables and code blocks

Use ```let``` to declare a variable (declare a variable = tell Rust to make a variable).

```rust
fn main() {
    let my_number = 8;
    println!("Hello, number {}", my_number);
}
```

Variables start and end inside a code block ```{}```. In this example, ```my_number``` ends before we call ```println!```, because it is inside its own code block.

```rust
fn main() {
    {
        let my_number = 8; // my_number starts here
                           // my_number ends here!
    }
    
    println!("Hello, number {}", my_number); // there is no my_number and
                                             // println!() can't find it
}
```

You can use a code block to return a value:

```rust
fn main() {
    let my_number = {
	let second_number = 8;
        second_number + 9 // No semicolon, so the code block returns 8 + 9.
	                  // It works just like a function
    };

    println!("My number is: {}", my_number);
}
```

If you add a semicolon inside the block, it will return ```()``` (nothing):

```rust
fn main() {
    let my_number = {
	let second_number = 8; // declare second_number,
        second_number + 9; // add 9 to second_number
		           // but we didn't return it!
                           // second_number dies now
    };

    println!("My number is: {:?}", my_number); // my_number is ()
}
```

So why did we write ```{:?}``` and not ```{}```? We will talk about that now.
