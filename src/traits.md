# Chapter 28
# Traits

We have seen traits before: Debug, Copy, Clone are all traits. To give a type a trait, you have to implement it. Because Debug and the others are so common, it's easy to do:

```rust
#[derive(Debug)]
struct MyStruct {
    number: usize,
}
```

But other traits are more difficult, so you need to implement them manually with ```impl```. For example, std::ops::Add is used to add two things. But you can add in many ways.

```rust
struct ThingsToAdd {
    first_thing: u32,
    second_thing: f32,
}
```

We can add ```first_thing``` and ```second_thing```, but we need to give more information. Maybe we want an f32, so something like this:

```rust
let result = self.second_thing + self.first_thing as f32
```

But maybe we want an integer, so like this:

```rust
let result = self.second_thing as u32 + self.first_thing
```

Or maybe we want to just put ```self.first_thing``` next to ```self.second_thing``` and say that this is how we want to add. So if we add 55 to 33.4, we want to see 5533, not 88.

So first let's look at how to make a trait. To make a trait, write ```trait``` and then create some functions.

```rust
struct Animal { // A simple struct - an Animal only has a name
    name: String,
}

trait Dog { // The dog trait gives some functionality
    fn bark(&self) { // It can bark
        println!("Woof woof!");
    }
    fn run(&self) { // and it can run
        println!("The dog is running!");
    }
}

impl Dog for Animal {} // Now Animal has the trait Dog

fn main() {
    let rover = Animal {
        name: "Rover".to_string(),
    };

    rover.bark(); // Now Animal can use bark()
    rover.run();  // and it can use run()
}
```

This is okay, but we don't want to print "The dog is running". We can change the method .run(), but we have to follow the signature. The signature says:

```rust
    fn run(&self) { 
        println!("The dog is running!");
    }
```

The signature says "fn run() takes &self, and returns nothing". So you can't do this:

```rust
    fn run(&self) -> i32 { 
        5
    }
```

Rust will say:

```
   = note: expected fn pointer `fn(&Animal)`
              found fn pointer `fn(&Animal) -> i32`
```

But we can do this:

```rust
impl Dog for Animal {
    fn run(&self) {
        println!("{} is running!", self.name);
    }
}
```

Now it prints ```Rover is running!```. This is okay because we are returning (), or nothing, which is what the trait says.

Actually, a trait doesn't need to write out the whole function. Now we change bark() and run() to just say ```fn bark(&self)``` and ```fn run(&self);```. This is not a full function, so the user must write it.


```rust
struct Animal {
    name: String,
}

trait Dog {
    fn bark(&self); // bark() says it needs a &self and returns nothing
    fn run(&self); // run() says it needs a &self and returns nothing.
                   // So now we have to write them ourselves.
}

impl Dog for Animal {
    fn bark(&self) {
        println!("{}, stop barking!!", self.name);
    }
    fn run(&self) {
        println!("{} is running!", self.name);
    }
}

fn main() {
    let rover = Animal {
        name: "Rover".to_string(),
    };

    rover.bark();
    rover.run();
}
```

So when you create a trait, you must think: "Which functions should I write? And which functions should the user write?" If you think the user will use the function the same way every time, then write out the function. If you think the user will use it differently, then just write the function signature.

So let's try implementing the Display trait for our struct. First we will make a simple struct:

```rust
struct Cat {
    name: String,
    age: u8,
}

fn main() {
    let mr_mantle = Cat {
        name: "Reggie Mantle".to_string(),
        age: 4,
    };
}
```

Now we want to print ```mr_mantle```. Debug is easy to derive:

```rust
#[derive(Debug)]
struct Cat {
    name: String,
    age: u8,
}

fn main() {
    let mr_mantle = Cat {
        name: "Reggie Mantle".to_string(),
        age: 4,
    };

    println!("Mr. Mantle is a {:?}", mr_mantle);
}
```

but Debug print is not what we want.

```rust
Mr. Mantle is a Cat { name: "Reggie Mantle", age: 4 }
```

So we need to implement Display for Cat. On https://doc.rust-lang.org/std/fmt/trait.Display.html we can see the information for Display, and one example. It says:

```rust
use std::fmt;

struct Position {
    longitude: f32,
    latitude: f32,
}

impl fmt::Display for Position {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "({}, {})", self.longitude, self.latitude)
    }
}
```

Some parts of this we don't understand yet, like ```<'_>``` and what ```f``` is doing. But we understand the ```Position``` struct: it is just two ```f32```s. We also understand that ```self.longitude``` and ```self.latitude``` are the values in the struct. So maybe we can just use this for our struct, with ```self.name``` and ```self.age```. Also, ```write!``` looks a lot like ```println!```. So we write this:

```rust
use std::fmt;

struct Cat {
    name: String,
    age: u8,
}

impl fmt::Display for Cat {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "{} is a cat who is {} years old.", self.name, self.age)
    }
}
```

Now our code looks like this:

```rust
use std::fmt;
struct Cat {
    name: String,
    age: u8,
}

impl fmt::Display for Cat {
  fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
      write!(f, "{} is a cat who is {} years old.", self.name, self.age)
  }
}

fn main() {
    let mr_mantle = Cat {
        name: "Reggie Mantle".to_string(),
        age: 4,
    };

    println!("{}", mr_mantle);
}
```

Success! Now when we use ```{}``` to print, we get ```Reggie Mantle is a cat who is 4 years old.```. This looks much better.

## Taking a String and a &str in a function

Sometimes you want a function that can take both a ```String``` and a ```&str```. You can do this with generics and the ```AsRef``` trait. ```AsRef``` is used to give a reference from one type to another type. If you look at the documentation for ```String```, you can see that it has ```AsRef``` for many types:

https://doc.rust-lang.org/std/string/struct.String.html

Here are some function signatures for them.

```AsRef<str>```:

```rust
impl AsRef<str> for String

fn as_ref(&self) -> &str
```

```AsRef<[u8]>```:

```rust
impl AsRef<[u8]> for String

fn as_ref(&self) -> &[u8]
```

```AsRef<OsStr>```:

```rust
impl AsRef<OsStr> for String

fn as_ref(&self) -> &OsStr
```

You can see that it takes ```&self``` and gives a reference to the other type. This means that if you have a generic type T, you can say that it needs ```AsRef<str>```. If you do that, it will be able to take a ```&str``` and a ```String```.

Let's start with the generic function. This doesn't work yet:

```rust
fn print_it<T>(input: T) {
    println!("{}", input)
}

fn main() {
    print_it("Please print me");
}
```

Rust says ```error[E0277]: `T` doesn't implement `std::fmt::Display```. So we will require T to implement Display.

```rust
use std::fmt::Display;

fn print_it<T: Display>(input: T) {
    println!("{}", input)
}

fn main() {
    print_it("Please print me");
}
```

Now it works and prints ```Please print me```. That is good, but T can still be too many things. It can be an ```i8```, an ```f32``` and anything else with just ```Display```. So we add ```AsRef<str>```, and now T needs both ```AsRef<str>``` and ```Display```.

```rust
use std::fmt::Display;

fn print_it<T: AsRef<str> + Display>(input: T) {
    println!("{}", input)
}

fn main() {
    print_it("Please print me");
    print_it("Also, please print me".to_string());
    // print_it(7); <- This will not print
}
```

Now it won't take types like ```i8```.

Don't forget that you can write the function differently when it gets long. If we add Debug then it becomes ```fn print_it<T: AsRef<str> + Display + Debug>(input: T)``` which is long for one line. So we can write it like this:

```rust
use std::fmt::{Debug, Display}; // add Debug

fn print_it<T>(input: T) // Now this line is easy to read
where
    T: AsRef<str> + Debug + Display, // and these traits are easy to read
{
    println!("{}", input)
}

fn main() {
    print_it("Please print me");
    print_it("Also, please print me".to_string());
}
```

