# Chapter 13
# Giving references to functions

References are very useful for functions. The rule in Rust on variables is: a variable can only have one owner.

This code will not work:

```ref
fn main() {
    let country = String::from("Austria");
    print_country(country); // We print "Austria"
    print_country(country); // That was fun, let's do it again!
}

fn print_country(country_name: String) {
    println!("{}", country_name);
}
```

It does not work because ```country``` is destroyed. Here's how:

- Step 1: We create the ```String``` ```country```. ```country``` is the owner.
- Step 2: We give ```country``` to ```print_country```. ```print_country``` doesn't have an ```->```, so it doesn't return anything. After ```print_country``` finishes, our ```String``` is now dead.
- Step 3: We try to give ```country``` to ```print_country```, but we already did that. We don't have ```country``` to give anymore.

We can fix this by adding ```&```.

```ref
fn main() {
    let country = String::from("Austria");
    print_country(&country); // We print "Austria"
    print_country(&country); // That was fun, let's do it again!
}

fn print_country(country_name: &String) {
    println!("{}", country_name);
}
```

Now ```print_country()``` is a function that takes a reference to a ```String```: a ```&String```. Also, we give it a reference to country by writing ```&country```. This says "you can look at it, but I will keep it".

Here is an example of a function that uses a mutable variable.

```ref
fn main() {
    let mut country = String::from("Austria");
    add_hungary(&mut country);
}

fn add_hungary(country_name: &mut String) {
    country_name.push_str("-Hungary"); // push_str() adds a &str to a String
    println!("Now it says: {}", country_name);
}
```

So to conclude:

* ```fn function_name(variable: String)``` takes a ```String``` and owns it. If it doesn't return anything, then the variable dies after the function is done.
* ```fn function_name(variable: &String)``` borrows a ```String``` and can look at it
* ```fn function_name(variable: &mut String)``` borrows a ```String``` and can change it

Here is an example that looks like a mutable reference, but it is different.

```rust
fn main() {
    let country = String::from("Austria"); // country is not mutable
    adds_hungary(country);
}

fn adds_hungary(mut country: String) { // but adds_hungary takes the string and it is mutable!
    country.push_str("-Hungary");
    println!("{}", country);
}
```

How is this possible? It is because ```mut hungary``` is not a reference: ```adds_hungary``` owns ```country``` now. (Remember, it takes ```String``` and not ```&String```). ```adds_hungary``` is the full owner, so it can take ```country``` as mutable.

