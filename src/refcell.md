# Chapter 35
# RefCell

A ```RefCell``` is another way to change values without needing to declare ```mut```. It is like a ```Cell``` but uses references instead of copies.

We will create a ```User``` struct. So far you can see that it is similar to Cell:

```rust
use std::cell::RefCell;

#[derive(Debug)]
struct User {
    id: u32,
    year_registered: u32,
    username: String,
    active: RefCell<bool>,
    // Many other fields
}

fn main() {
    let user_1 = User {
        id: 1,
        year_registered: 2020,
        username: "User 1".to_string(),
        active: RefCell::new(true),
    };

    println!("{:?}", user_1.active);

}
```

This prints ```RefCell { value: true }```.

There are many methods for ```RefCell```. Two of them are ```.borrow()``` and ```.borrow_mut()```. With these methods, you can do the same thing you do with ```&``` and ```&mut```. The rules are the same: 

* Many borrows is fine, 
* one mutable borrow is fine, 
* but mutable and immutable together is not fine.

So changing the value in a ```RefCell``` is very easy:

```rust
    user_1.active.replace(false);
    println!("{:?}", user_1.active);
```

And there are many other methods like ```replace_with``` that uses a closure:

```rust
    let date = 2020;

    user_1
        .active
        .replace_with(|_| if date < 2000 { true } else { false });
    println!("{:?}", user_1.active);
```

But you have to be careful with a ```RefCell```, because it checks borrows at runtime, not compilation time. So this will compile:

```rust
use std::cell::RefCell;

#[derive(Debug)]
struct User {
    id: u32,
    year_registered: u32,
    username: String,
    active: RefCell<bool>,
    // Many other fields
}

fn main() {
    let user_1 = User {
        id: 1,
        year_registered: 2020,
        username: "User 1".to_string(),
        active: RefCell::new(true),
    };

    let borrow_one = user_1.active.borrow_mut(); // first mutable borrow - okay
    let borrow_two = user_1.active.borrow_mut(); // second mutable borrow - not okay
}
```

But if you run it, it will immediately panic.

```rust
thread 'main' panicked at 'already borrowed: BorrowMutError', C:\Users\mithr\.rustup\toolchains\stable-x86_64-pc-windows-msvc\lib/rustlib/src/rust\src\libcore\cell.rs:877:9
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
error: process didn't exit successfully: `target\debug\rust_book.exe` (exit code: 101)
```

```already borrowed: BorrowMutError``` is the important part. So when you use a ```RefCell```, it is good to compile **and** run to check.

