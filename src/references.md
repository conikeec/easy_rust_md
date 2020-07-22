# Chapter 11
# More on references

References are very important in Rust. Rust uses references to make sure that all memory access is safe. We know that we use ```&``` to create a reference:

```rust
fn main() {
    let country = String::from("Austria");
    let ref_one = &country;
    let ref_two = &country;

    println!("{}", ref_one);
}
```

```country``` is a ```String```. We created two references to ```country```. They have the type ```&String```: a "reference to a String". We could create one hundred references to ```country``` and it would be no problem.

But this is a problem:

```rust
fn main() {
    let country = return_str();
}

fn return_str() -> &str {
    let country = String::from("Austria");
    let country_ref = &country;
    country_ref
}
```

The function ```return_str()``` creates a String, then it creates a reference to the string. Then it tries to return the reference. But ```country``` only lives inside the function. So after the function is over, ```country_ref``` is referring to memory that is already gone. Rust prevents us from making a mistake with memory.


