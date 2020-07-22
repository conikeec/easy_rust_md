# Chapter 40
# The todo! macro

Sometimes you want to write code in general to help you imagine your project. For example, imagine a simple project to do something with books. Here's what you think as you write it:

```rust
struct Book {} // Okay, first I need a book struct.
               // Nothing in there yet - will add later

enum BookType { // A book can be hardcover or softcover, so add an enum
    HardCover,
    SoftCover,
}

fn get_book(book: &Book) -> Option<String> {} // get_book should take a &Book and return an Option<String>

fn delete_book(book: Book) -> Result<(), String> {} // delete_book should take a Book and return a Result...
                                                    // TODO: impl block and make these functions methods...
fn check_book_type(book_type: &BookType) { // Let's make sure the match statement works
    match book_type {
        BookType::HardCover => println!("It's hardcover"),
        BookType::SoftCover => println!("It's softcover"),
    }
}

fn main() {
    let book_type = BookType::HardCover;
    check_book_type(&book_type); // Okay, let's check this function!
}
```

But Rust is not happy with ```get_book``` and ```delete_book```. It says:

```
error[E0308]: mismatched types
  --> src\main.rs:32:29
   |
32 | fn get_book(book: &Book) -> Option<String> {}
   |    --------                 ^^^^^^^^^^^^^^ expected enum `std::option::Option`, found `()`
   |    |
   |    implicitly returns `()` as its body has no tail or `return` expression
   |
   = note:   expected enum `std::option::Option<std::string::String>`
           found unit type `()`

error[E0308]: mismatched types
  --> src\main.rs:34:31
   |
34 | fn delete_book(book: Book) -> Result<(), String> {}
   |    -----------                ^^^^^^^^^^^^^^^^^^ expected enum `std::result::Result`, found `()`
   |    |
   |    implicitly returns `()` as its body has no tail or `return` expression
   |
   = note:   expected enum `std::result::Result<(), std::string::String>`
           found unit type `()`
```

But you don't care about ```get_book``` and ```delete_book``` right now. This is where you can use ```todo!()```. If you add that to the function, Rust will not complain, and will compile.

```rust
fn get_book(book: &Book) -> Option<String> {
    todo!() // todo means "I will do it later, please be quiet"
}

fn delete_book(book: Book) -> Result<(), String> {
    todo!()
}
```

So now the code compiles and you can see the result of ```check_book_type```: ```It's hardcover```.

If you call a function with ```todo!()``` inside it, it will panic.

Also, ```todo!()``` functions still need real input and output types. If you just write this, it will not compile:

```rust
fn get_book(book: &Book) -> WorldsBestType {
    todo!()
}
```

It will say:

```
error[E0412]: cannot find type `WorldsBestType` in this scope
  --> src\main.rs:32:29
   |
32 | fn get_book(book: &Book) -> WorldsBestType {
   |                             ^^^^^^^^^^^^^^ not found in this scope
```

```todo!()``` is actually the same as another macro: ```unimplemented!()```. Programmers were using ```unimplemented()``` a lot but it was long to type, so they created ```todo!()``` which is shorter.

#Rc

Rc means "reference counter". You know that in Rust, every variable can only have one owner. That is why this doesn't work:

```rust
fn main() {
    let user_name = String::from("User MacUserson");

    takes_a_string(user_name);
    also_takes_a_string(user_name);
    
}

fn takes_a_string(input: String) {
    println!("It is: {}", input)
}

fn also_takes_a_string(input: String) {
    println!("It is: {}", input)
}
```

After ```takes_a_string``` takes ```user_name```, you can't use it anymore. Here that is no problem: you can just give it ```user_name.clone()```. But sometimes a variable is part of a struct, and maybe you can't clone the struct. Or maybe the String is really long and you don't want to clone it. These are some reasons for ```Rc```, which lets you have more than one owner. An ```Rc``` is like a good office worker: ```Rc``` writes down who has ownership, and how many. Then once the number of owners goes down to 0, the variable can disappear.

Here's how you use an ```Rc```. First imagine two structs: one called ```City```, and another called ```Cities```. ```City``` has information for one city, and ```Cities``` puts all the cities together in ```Vec```s.

```rust
#[derive(Debug)]
struct City {
    name: String,
    population: u32,
    city_history: String,
}

#[derive(Debug)]
struct Cities {
    names: Vec<String>,
    histories: Vec<String>,
}

fn main() {
    let calgary = City {
        name: "Seoul".to_string(),
        population: 1_200_000,
           // Pretend that this string is very very long
        city_history: "Calgary began as a fort called Fort Calgary that...".to_string(),
    };

    let canada_cities = CityData {
        names: vec![calgary.name], // This is using calgary.name, which is short
        histories: vec![calgary.city_history], // But this String is very long
    };

    println!("Calgary's history is: {}", calgary.city_history);
    
}
```

Of course, it doesn't work because ```canada_cities``` now owns the data and ```calgary``` doesn't. We can clone the name: ```names: vec![calgary.name.clone()]``` but we don't want to clone the ```city_history```, which is long. So we can use an ```Rc```.

Add the ```use``` declaration:

```rust
use std::rc::Rc;
```

Then put ```Rc``` around ```String```.

```rust
#[derive(Debug)]
struct City {
    name: String,
    population: u32,
    city_history: Rc<String>,
}

#[derive(Debug)]
struct Cities {
    names: Vec<String>,
    histories: <Vec<Rc<<String>>>,
}
```

To add a new reference, you have to ```clone``` the ```Rc```. You can clone an item with ```item.clone()``` or with ```Rc::clone(&item)```.

Now our code looks like this:

```rust
use std::rc::Rc;

#[derive(Debug)]
struct City {
    name: String,
    population: u32,
    city_history: Rc<String>, // String inside an Rc
}

#[derive(Debug)]
struct CityData {
    names: Vec<String>,
    histories: Vec<Rc<String>>, // A Vec of Strings inside Rcs
}

fn main() {
    let calgary = City {
        name: "Seoul".to_string(),
        population: 1_200_000,
           // Pretend that this string is very very long
        city_history: Rc::new("Calgary began as a fort called Fort Calgary that...".to_string()), // Rc::new() to make the Rc
    };

    let canada_cities = CityData {
        names: vec![calgary.name],
        histories: vec![calgary.city_history.clone()], // .clone() to increase the count
    };

    println!("Calgary's history is: {}", calgary.city_history);
    
}
```

So calgary.city_history has 2 owners. We can check the number of owners with ```Rc::strong_count(&item)```. Add this:

```rust
println!("{}", Rc::strong_count(&calgary.city_history));
```

This prints ```2```.

Let's add a new owner:

```rust
let new_owner = calgary.city_history.clone();
```

```new_owner``` is now an ```Rc<String>```. Now if we use ```println!("{}", Rc::strong_count(&calgary.city_history));```, we get ```3```.

So if there are strong pointers, are there weak pointers? Yes, there are. Weak pointers are useful because if two Rcs point at each other, they can't die. This is called a "reference cycle". If item 1 has an Rc to item 2, and item 2 has an Rc to item 1, they can't get to 0. In this case you want to use weak references. ```Rc``` will count the references, but if it only has weak references then it can die. You use ```Rc::downgrade(&item)``` instead of ```Rc::clone(&item)``` to make weak references. Also, you use ```Rc::weak_count(&item)``` to see the weak count.
