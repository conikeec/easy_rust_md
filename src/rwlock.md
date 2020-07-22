# Chapter 37
# RwLock

```RwLock``` means "read write lock". It is like a ```Mutex``` but also like a ```RefCell```. You use ```.write().unwrap()``` instead of ```.lock().unwrap()``` to change it. But you can also use ```.read().unwrap()``` to get read access. It is like ```RefCell``` because it follows the rules:

* many ```.read()``` variables is okay,
* one ```.write()```` variable is okay,
* but more than one ```.read()``` or ```.read()``` together with ```.write()``` is not okay.

The program will run forever if you try to ```.write()``` when you can't get access:

```rust
use std::sync::RwLock;

fn main() {
    let my_rwlock = RwLock::new(5);

    let read1 = my_rwlock.read().unwrap(); // one .read() is fine
    let read2 = my_rwlock.read().unwrap(); // two .read()s is also fine

    println!("{:?}, {:?}", read1, read2);

    let write1 = my_rwlock.write().unwrap(); // uh oh, now the program will wait forever
}
```

So we use ```std::mem::drop```, just like in a ```Mutex```.

```rust
use std::sync::RwLock;
use std::mem::drop; // We will use drop() many times

fn main() {
    let my_rwlock = RwLock::new(5);

    let read1 = my_rwlock.read().unwrap();
    let read2 = my_rwlock.read().unwrap();

    println!("{:?}, {:?}", read1, read2);

    drop(read1);
    drop(read2); // we dropped both, so we can use .write() now

    let mut write1 = my_rwlock.write().unwrap();
    *write1 = 6;
    drop(write1);
    println!("{:?}", my_rwlock);
}
```

And you can use ```try_read()``` and ```try_write()``` too.

```rust
use std::sync::RwLock;

fn main() {
    let my_rwlock = RwLock::new(5);

    let read1 = my_rwlock.read().unwrap();
    let read2 = my_rwlock.read().unwrap();

    if let Ok(mut number) = my_rwlock.try_write() {
        *number += 10;
        println!("Now the number is {}", number);
    } else {
        println!("Couldn't get write access, sorry!")
    };
    
}
```
