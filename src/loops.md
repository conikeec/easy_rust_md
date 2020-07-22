# Chapter 23
# Loops

With loops you can tell Rust to continue something until you want it to stop. With ```loop``` you can start a loop that does not stop, unless you tell it when to ```break```.

```rust
fn main() { // This program will never stop
    loop {
 	       
    }
}
```

So let's tell the compiler when it can break.

```rust
fn main() {
    let mut counter = 0; // set a counter to 0
    loop {
        counter +=1; // increase the counter by 1
        println!("The counter is now: {}", counter);
        if counter == 5 { // stop when counter == 5
            break;
        }
    }
}
```

This will print:

```
The counter is now: 1
The counter is now: 2
The counter is now: 3
The counter is now: 4
The counter is now: 5
```

If you have a loop inside of a loop, you can give them names. With names, you can tell Rust which loop to ```break``` out of. Use ```'``` (called a "tick") and a ```:``` to give it a name:

```rust
fn main() {
    let mut counter = 0;
    let mut counter2 = 0;
    println!("Now entering the first loop.");

    'first_loop: loop { // Give the first loop a name
        counter +=1;
        println!("The counter is now: {}", counter);
        if counter > 9 { // Starts a second loop inside this loop
            println!("Now entering the second loop.");

            'second_loop: loop { // now we are inside `second_loop
                println!("The second counter is now: {}", counter2);
                counter2 +=1;
                if counter2 == 3 {
                    break 'first_loop; // Break out of `first_loop so we can exit the program
                }
            }
        }
    }
}
```

A ```while``` loop is a loop that continues while something is still ```true```. Each loop, Rust will check if it is still ```true```. If it becomes ```false```, Rust will stop the loop.

```rust
fn main() {
    let mut counter = 0;

    while counter < 5 {
        counter +=1;
        println!("The counter is now: {}", counter);
    }
}
```

A ```for``` loop lets you tell Rust what to do each time. But in a ```for``` loop, the loop stops after a certain number of times. ```for``` loops use **ranges** very often. You use ```..``` and ```..=``` to create a range.

* ```..``` creates an **exclusive** range: ```0..3``` creates ```0, 1, 2```.
* ```..=``` creates an **inclusive** range: ```0..=3``` = ```0, 1, 2, 3```.

```rust
fn main() {
    for number in 0..3 {
        println!("The number is: {}", number);
    }

    for number in 0..=3 {
        println!("The next number is: {}", number);
    }
}
```

Also notice that ```number``` becomes the variable name for 0..3. We can then use that name in ```println!```.

If you don't need a variable name, use ```_```.

```rust
fn main() {
    for _ in 0..3 {
        println!("Printing the same thing three times");
    }
}
```

Actually, if you give a variable name and don't use it, Rust will tell you:

```rust
fn main() {
    for number in 0..3 {
        println!("Printing the same thing three times");
    }
}
```

This is not an error, but Rust will remind you that you didn't use ```number```:

```rust
warning: unused variable: `number`
 --> src\main.rs:2:9
  |
2 |     for number in 0..3 {
  |         ^^^^^^ help: if this is intentional, prefix it with an underscore: `_number`
```

Rust also suggests ```_number```. Putting ```_``` in front of a variable name means "maybe I will use it later". But using just ```_``` means "I don't care about this variable at all".

You can also use ```break``` to return a value. You write the value right after ```break``` and use a ```;```. Here is an example with a ```loop``` and a break that gives ```my_number``` its value.

```rust
fn main() {
    let mut counter = 5;
    let my_number = loop {
        counter +=1;
        if counter % 53 == 3 {
            break counter;
        }
    };
    println!("{}", my_number);
}
```

```break counter;``` means "break with the value of counter". And because the whole bloock starts with ```let```, ```my_number``` gets the value.
