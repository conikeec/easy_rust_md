# Chapter 36
# Mutex

```Mutex``` is another way to change values without declaring ```mut```. Mutex means ```mutual exclusion```, which means "only one at a time". This is why a ```Mutex``` is safe, because it only lets one process change it at a time. To do this, it uses ```.lock()```. ```Lock``` is like locking a door from the inside. You go into a room, lock the door, and now you can change things inside the room. Nobody else can come in and stop you, because you locked the door.

A ```Mutex``` is easier to understand through examples.

```rust
use std::sync::Mutex;

fn main() {
    let my_mutex = Mutex::new(5); // A new Mutex<i32>. We don't need to say mut
    let mut mutex_changer = my_mutex.lock().unwrap(); // mutex_changer is a MutexGuard
                                                     // It has to be mut because we will change it
                                                     // Now it has access to the Mutex
                                                     // Let's print my_mutex to see:
    
    println!("{:?}", my_mutex); // This prints "Mutex { data: <locked> }"
                                // So we can't access the data with my_mutex now,
                                // only with mutex_changer
    
    println!("{:?}", mutex_changer); // This prints 5. Let's change it to 6.

    *mutex_changer = 6; // mutex_changer is a MutexGuard<i32> so we use * to change the i32

    println!("{:?}", mutex_changer); // Now it says 6
    
}
```

But ```mutex_changer``` still has a lock. How do we stop it? A ```Mutex``` is unlocked when the ```MutexGuard``` goes out of scope. "Go out of scope" means the code block is finished. For example:

```rust
use std::sync::Mutex;

fn main() {
    let my_mutex = Mutex::new(5);
    {
        let mut mutex_changer = my_mutex.lock().unwrap();
        *mutex_changer = 6;
    } // mutex_changer goes out of scope - now it is gone

    println!("{:?}", my_mutex); // Now it says 6
}
```

If you don't want to use a different ```{}``` code block, you can use ```std::mem::drop(mutex_changer)```. ```std::mem::drop``` means "make this go out of scope".

use std::sync::Mutex;

```rust
fn main() {
    let my_mutex = Mutex::new(5);
    let mut mutex_changer = my_mutex.lock().unwrap();
    *mutex_changer = 6;
    std::mem::drop(mutex_changer); // drop mutex_changer - it is gone now
                                   // and my_mutex is unlocked

    println!("{:?}", my_mutex); // Now it says 6
}
```

You have to be careful with a ```Mutex``` because if another variable tries to ```lock``` it, it will wait:

```rust
use std::sync::Mutex;

fn main() {
    let my_mutex = Mutex::new(5);
    let mut mutex_changer = my_mutex.lock().unwrap(); // mutex_changer has the lock
    let mut other_mutex_changer = my_mutex.lock().unwrap(); // other_mutex_changer wants the lock
                                                            // the program is waiting
                                                            // and waiting
                                                            // and will wait forever.

    println!("This will never print...");

}
```

One other method is ```try_lock()```. Then it will try once, and if it doesn't get the lock it will give up. Don't do ```try_lock().unwrap()```, because it will panic if it doesn't work. ```if let``` or ```match``` is better:

```rust
use std::sync::Mutex;

fn main() {
    let my_mutex = Mutex::new(5);
    let mut mutex_changer = my_mutex.lock().unwrap();
    let mut other_mutex_changer = my_mutex.try_lock(); // try to get the lock

    if let Ok(value) = other_mutex_changer { // 
        println!("The MutexGuard has: {}", value)
    } else {
        println!("Didn't get the lock")
    }
}
}
```

Also, you don't need to make a variable to change the ```Mutex```. You can just do this:

```rust
use std::sync::Mutex;

fn main() {
    let my_mutex = Mutex::new(5);

    *my_mutex.lock().unwrap() = 6;

    println!("{:?}", my_mutex);
}
```

```*my_mutex.lock().unwrap() = 6;``` means "unlock my_mutex and make it 6". There is no variable that holds it so you don't need to call ```std::mem::drop```. You can do it 100 times if you want - it doesn't matter:

```rust
use std::sync::Mutex;

fn main() {
    let my_mutex = Mutex::new(5);

    for _ in 0..100 {
        *my_mutex.lock().unwrap() += 1; // locks and unlocks 100 times
    }

    println!("{:?}", my_mutex);
}
```
