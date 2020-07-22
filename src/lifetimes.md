# Chapter 33
# Lifetimes

A lifetime means "how long the variable lives". You only need to think about lifetimes with references. This is because references can't live longer than the object they come from. For example, this function does not work:

```rust
fn returns_reference() -> &str {
    let my_string = String::from("I am a string");
    &my_string
}
```

The problem is that ```my_string``` only lives inside ```returns_reference```. We try to return ```&my_string```, but ```&my_string``` can't exist without ```my_string```. So the compiler says no.

This code also doesn't work:

```rust
fn main() {
    let my_str = returns_str();
    println!("{}", my_str);
}

fn returns_str() -> &str {
    let my_string = String::from("I am a string");
    "I am a str"
}
```

But it almost works. The compiler says:

```
error[E0106]: missing lifetime specifier
 --> src\main.rs:6:21
  |
6 | fn returns_str() -> &str {
  |                     ^ expected named lifetime parameter
  |
  = help: this function's return type contains a borrowed value, but there is no value for it to be borrowed from
help: consider using the `'static` lifetime
  |
6 | fn returns_str() -> &'static str {
  |                     ^^^^^^^^
```


```missing lifetime specifier``` means that we need to add a ```'``` with the lifetime. Then it says that it ```contains a borrowed value, but there is no value for it to be borrowed from```. That means that ```I am a str``` isn't borrowed from anything. It says ```consider using the `'static` lifetime``` by writing ```&'static str```. So it thinks we should try saying that this is a string literal.

Now it works:

```rust
fn main() {
    let my_str = returns_str();
    println!("{}", my_str);
}

fn returns_str() -> &'static str {
    let my_string = String::from("I am a string");
    "I am a str"
}
```

So now ```fn returns_str() -> &'static str``` tells Rust: "don't worry, we will only return a string literal". String literals live for the whole program, so Rust is happy.

But ```'static``` is not the only lifetime. Actually, every variable has a lifetime, but usually we don't have to write it. We only have to write the lifetime when the compiler doesn't know.

Here is an example of another lifetime. Imagine we want to create a ```City``` struct and give it a ```&str``` for the name. Later we will have many more ```&str```s because we need faster performance than with ```String```. So we write it like this, but it won't work:

```rust
#[derive(Debug)]
struct City {
    name: &str,
    date_founded: u32,
}

fn main() {
    let my_city = City {
        name: "Ichinomiya",
        date_founded: 1921,
    };
}
```

The compiler says:

```
error[E0106]: missing lifetime specifier
 --> src\main.rs:3:11
  |
3 |     name: &str,
  |           ^ expected named lifetime parameter
  |
help: consider introducing a named lifetime parameter
  |
2 | struct City<'a> {
3 |     name: &'a str,
  |
```

Rust needs a lifetime for ```&str``` because ```&str``` is a reference. What happens when the value that ```name``` points to is dropped? That would be unsafe.

What about ```'static```, will that work? We used it before. Let's try:

```rust
#[derive(Debug)]
struct City {
    name: &'static str, // change &str to &'static str
    date_founded: u32,
}

fn main() {
    
    let my_city = City {
        name: "Ichinomiya",
        date_founded: 1921,
    };  

    println!("{} was founded in {}", my_city.name, my_city.date_founded);

}
```

Okay, that works. And maybe this is what you wanted for the struct. However, note that we can only take "string literals", so not references to something else. So this will not work:

```rust
#[derive(Debug)]
struct City {
    name: &'static str, // must live for the whole program
    date_founded: u32,
}

fn main() {
    
    let city_names = vec!["Ichinomiya".to_string(), "Kurume".to_string()]; // city_names does not live for the whole program

    let my_city = City {
        name: &city_names[0], // This is a &str, but not a &'static str. It is a reference to a value inside city_names
        date_founded: 1921,
    };  

    println!("{} was founded in {}", my_city.name, my_city.date_founded);

}
```

The compiler says:

```
error[E0597]: `city_names` does not live long enough
  --> src\main.rs:12:16
   |
12 |         name: &city_names[0],
   |                ^^^^^^^^^^
   |                |
   |                borrowed value does not live long enough
   |                requires that `city_names` is borrowed for `'static`
...
18 | }
   | - `city_names` dropped here while still borrowed
```

So now we will try what the compiler suggested before. It said to try writing ```struct City<'a>``` and ```name: &'a str```. This means that it will only take a reference for ```name``` if it lives as long as ```City```.

```rust
#[derive(Debug)]
struct City<'a> { // City has lifetime 'a
    name: &'a str, // and name also has lifetime 'a.
    date_founded: u32,
}

fn main() {
    
    let city_names = vec!["Ichinomiya".to_string(), "Kurume".to_string()];

    let my_city = City {
        name: &city_names[0],
        date_founded: 1921,
    };  

    println!("{} was founded in {}", my_city.name, my_city.date_founded);

}
```

Also remember that you can write anything instead of ```'a``` if you want:

```rust
#[derive(Debug)]
struct City<'city> { // The lifetime is now called 'city
    name: &'city str, // and name has the 'city lifetime
    date_founded: u32,
}
```

So usually you will write ```'a, 'b, 'c``` etc. because it is quick and the usual way to write. But you can always change it if you want.

Also remember this important fact: ```'a``` etc. don't change the actual lifetime of variables. They are like traits for generics. Remember when we wrote generics? For example:

```rust
fn prints<T: Display>(input: T) {
    println!("T is {}", input);
}
```

When you write ```T: Display```, it means "please only take T if it has Display".
It does not mean: "I am giving Display to T".

The same is true for lifetimes. When you write 'a here:

```rust
#[derive(Debug)]
struct City<'a> { 
    name: &'a str,
    date_founded: u32,
}
```

It means "please only take an input for ```name``` if it lives at least as long as ```City```". 
It does not mean: "I will make the input for ```name``` live as long as ```City```".


