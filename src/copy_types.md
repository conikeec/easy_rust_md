# Chapter 14
# Copy types

Some types in Rust are very simple. They are called **copy types**. These simple types are all on the stack, and the compiler knows their size. That means that they are very easy to copy, so the compiler always copies when you send it to a function. So you don't need to worry about ownership.

These simple types include: integers, floats, booleans (true and false), and char.

How do you know if a type **implements** copy? (implements = can use) You can check the documentation. For example, here is the documentation for char:

https://doc.rust-lang.org/std/primitive.char.html

On the left you can see **Trait Implementations**. You can see for example **Copy**, **Debug**, and **Display**. So you know that a ```char```:

* is copied when you send it to a function (**Copy**)
* can use ```{}``` to print (**Display**)
* can use ```{:?}``` to print (**Debug**)


```rust
fn main() {
    let my_number = 8;
    prints_number(my_number); // Prints 8. prints_number gets a copy of my_number
    prints_number(my_number); // Prints 8 again.
                              // No problem, because my_number is copy type!
}

prints_number(number: i32) { // No return with ->
                             // If number was not copy type, it would take it
                             // and we couldn't use it again
    println!("{}", number);
}
```

But if you look at the documentation for String, it is not copy type.

https://doc.rust-lang.org/std/string/struct.String.html

On the left in **Trait Implementations** you can look in alphabetical order. A, B, C... there is no **Copy** in C. But there is **Clone**. **Clone** is similar to **Copy**, but needs more memory.

In this example, ```prints_country()``` prints the country name, a ```String```. We want to print it two times, but we can't:

```rust
fn main() {
    let country = String::from("Kiribati");
    prints_country(country);
    prints_country(country);
}

fn prints_country(country_name: String) {
    println!("{}", country_name);
}
```

But now we understand the message.

```
error[E0382]: use of moved value: `country`
 --> src\main.rs:4:20
  |
2 |     let country = String::from("Kiribati");
  |         ------- move occurs because `country` has type `std::string::String`, which does not implement the `Copy` trait
3 |     prints_country(country);
  |                    ------- value moved here
4 |     prints_country(country);
  |                    ^^^^^^^ value used here after move
```

The important part is ```which does not implement the `Copy` trait```. But in the documentation we saw that String implements the Clone trait. So we can add ```.clone()```. This creates a clone, and we send the clone to the function. ```country``` is still alive, so we can use it.

```rust
fn main() {
    let country = String::from("Kiribati");
    prints_country(country.clone()); // make a clone and give it to the function
    prints_country(country);
}

fn prints_country(country_name: String) {
    println!("{}", country_name);
}
```

Of course, if the ```String``` is very large, ```.clone()``` can use a lot of memory. For example, one ```String``` can be a whole book, and every time we call ```.clone()``` it will copy the book. So using ```&``` for a reference is faster, if you can.

```rust
fn main() {
    let mut country = String::from("Kiribati"); // country is mutable
    let country_ref = &country; // country_ref needs a reference
    changes_country(&mut country); // changes_country needs a &mut ref
    println!("{}", country_ref); // immutable and mutable borrow together

}

fn prints_country(country_name: String) {
    println!("{}", country_name);
}

fn changes_country(country_name: &mut String) {
    country_name.push_str(" is a country");
    println!("{}", country_name);
}
```

Maybe we can change the order, but we can also just ```.clone()```.

```rust
fn main() {
    let mut country = String::from("Kiribati"); // country is mutable
    let country_ref = &country; // country_ref needs a reference
    changes_country(&mut country.clone()); // give changes_country a clone instead
    println!("{}", country_ref); // now the code works

}

fn prints_country(country_name: String) {
    println!("{}", country_name);
}

fn changes_country(country_name: &mut String) {
    country_name.push_str(" is a country");
    println!("{}", country_name);
}
```

## Variables without values

A variable without a value is called an "uninitialized" variable. Uninitialized means "hasn't started yet". They are simple, just ```let``` and the name:

```rust
let my_variable;
```

But you can't use it yet. Your program won't compile if it tries to use it.

But sometimes they can be useful. A good example is when:

* You have a code block and inside that is the value for your variable, and
* The variable needs to live outside of the code block.

```rust
fn loop_then_return(mut counter: i32) -> i32 {
    loop {
        counter += 1;
        if counter % 50 == 0 {
            break;
        }
    }
    counter
}

fn main() {
    let my_number;

    {
        // Pretend we need to have this code block
        let number = {
            // Pretend there is code here to make a number
            7
        };

        my_number = loop_then_return(number);
    }

    println!("{}", my_number);
}
```

It is important to know that ```my_number``` was declared in the ```main()``` function, so it lives until the end. But it gets its value from inside a loop. However, that value lives as long as ```my_number```, because ```my_number``` has the value.

It helps to imagine if you simplify the code. ```loop_then_return(number)``` gives the result 50, so let's delete it and write ```50``` instead. Also, now we don't need ```number``` so we will delete it too. Now it looks like this:

```rust
fn main() {
    let my_number;
    {
        my_number = 50;
    }

    println!("{}", my_number);
}
```

So it's almost like saying ```let my_number = { 50 };```.

Also note that ```my_number``` is not ```mut```. We didn't give it a value until we gave it 50, so it never changed its value.

