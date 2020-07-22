# Chapter 21
# References and the dot operator

We learned that when you have a reference, you need to use ```*``` to get to the value. A reference is a different type, so this won't work:

```rust
fn main() {
    let my_number = 9;
    let reference = &my_number;

    println!("{}", my_number == reference);

}
```

The compiler prints:

```rust
error[E0277]: can't compare `{integer}` with `&{integer}`
 --> src\main.rs:5:30
  |
5 |     println!("{}", my_number == reference);
  |                              ^^ no implementation for `{integer} == &{integer}`
```

So we change line 5 to ```println!("{}", my_number == *reference);``` and now it prints ```true```. This is called dereferencing.

But when you use a method, Rust will dereference for you. The ```.``` in a function is called the dot operator.

First, let's make a struct with one ```u8``` field. Then we will make a reference to it and try to compare. It will not work:

```rust
struct Item {
    number: u8,
}

fn main() {
    let item = Item {
        number: 8,
    };

    let reference_number = &item.number; // reference number type is &u8

    println!("{}", reference_number == 8); // &u8 and u8 cannot be compared
}
```

To make it work, we need to dereference: ```println!("{}", *reference_number == 8);```.

But with the dot operator, we don't need ```*```. For example:

```rust
struct Item {
    number: u8,
}

fn main() {
    let item = Item {
        number: 8,
    };

    let reference_item = &item;

    println!("{}", reference_item.number == 8); // we don't need to write *reference_item.number
}
```

Now let's create a method for ```Item``` that compares ```number``` to another number. We don't need to use ```*``` anywhere:

```rust
struct Item {
    number: u8,
}

impl Item {
    fn compare_number(&self, other_number: u8) { // takes a reference to self
        println!("Are {} and {} equal? {}", self.number, other_number, self.number == other_number); 
            // We don't need to write *self.number
    }
}

fn main() {
    let item = Item {
        number: 8,
    };

    let reference_item = &item; // This is type &Item
    let reference_item_two = &reference_item; // This is type &&Item

    item.compare_number(8); // the method works
    reference_item.compare_number(8); // it works here too
    reference_item_two.compare_number(8); // and here

}
```

So just remember: when you use the ```.``` operator, you don't need to worry about ```*```.

