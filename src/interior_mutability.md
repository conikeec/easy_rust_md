# Chapter 34
# Interior mutability
## Cell

**Interior mutability** means having a little bit of mutability on the inside. Rust has some ways to let you safely change values inside of a struct that is immutable. First, let's look at a simple example where we would want this. Imagine a ```struct``` called ```PhoneModel``` with many fields:

```rust
struct PhoneModel {
    company_name: String,
    model_name: String,
    screen_size: f32,
    memory: usize,
    date_issued: u32,
    on_sale: bool,
}

fn main() {
    let super_phone_3000 = PhoneModel {
        company_name: "YY Electronics".to_string(),
        model_name: "Super Phone 3000".to_string(),
        screen_size: 7.5,
        memory: 4_000_000,
        date_issued: 2020,
        on_sale: true,
    };

}
```

It is better for the fields in ```PhoneModel``` to be immutable, because we don't want the data to change. The ```date_issued``` and ```screen_size``` never change, for example.

But inside is one field called ```on_sale```. A phone model will first be on sale (```true```), but later the company will stop selling it. Can we make just this one field mutable? Because we don't want to write ```let mut super_phone_3000```. If we do, then every field will become mutable.

Rust has many ways to allow some safe mutability inside of something that is immutable. The most simple is called ```Cell```. First we use ```use std::cell::Cell``` so that we can just write ```Cell``` instead of ```std::cell::Cell``` every time.

Then we change ```on_sale: bool``` to ```on_sale: Cell<bool>```. Now it is a ```bool``` inside of the ```Cell```.

```Cell``` has a method called ```.set()``` where you can change the value. We use ```.set()``` to change ```on_sale: true``` to ```on_sale: Cell::new(true)```.

```rust
use std::cell::Cell;

struct PhoneModel {
    company_name: String,
    model_name: String,
    screen_size: f32,
    memory: usize,
    date_issued: u32,
    on_sale: Cell<bool>,
}

fn main() {
    let super_phone_3000 = PhoneModel {
        company_name: "YY Electronics".to_string(),
        model_name: "Super Phone 3000".to_string(),
        screen_size: 7.5,
        memory: 4_000_000,
        date_issued: 2020,
        on_sale: Cell::new(true),
    };

    // 10 years later, super_phone_3000 is not on sale anymore
    super_phone_3000.on_sale.set(false);    

}
```

```Cell``` works for all types, but works best for simple Copy types because it gives values, not references. ```Cell``` has a method called ```get()``` for example that only works on Copy types.

Another type you can use is ```RefCell```.
