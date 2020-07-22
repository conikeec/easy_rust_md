# Chapter 42
# Arc

You remember that we used an ```Rc``` to give a variable more than one owner. If we are doing the same thing in a thread, we need an ```Arc```. ```Arc``` means "atomic reference counter". Atomic means that it uses the computer's processor so that data only gets written once each time. This is important because if two threads write data at the same time, you will get the wrong result. For example, imagine if you could do this in Rust:

```rust
    let mut x = 10;

    for i in 0..10 { // Thread 1
        x += 1
    }
    for i in 0..10 { // Thread 2
        x += 1
    }
```

If Thread 1 and Thread 2 just start together, maybe this will happen:

* Thread 1 sees 10, writes 11. Then Thread 2 sees 11, writes 12. No problem so far.
* Thread 1 sees 12. At the same time, Thread 2 sees 12. Thread 1 writes 13. And Thread 2 writes 13. Now we have 13, but it should be 14.

An ```Arc``` uses the processor to make sure this doesn't happen, so it is the method you must use when you have threads. You don't want an ```Arc``` for just one thread though, because ```Rc``` is a bit faster.

You can't change data with just an ```Arc``` though. So you wrap the data in a ```Mutex```, and then you wrap the ```Mutex``` in an ```Arc```.

So let's use a ```Mutex``` inside an ```Arc``` to change the value of a number. First let's set up one thread:

```rust
fn main() {
    let my_number = 0;

    let handle = std::thread::spawn(|| {
        println!("The thread is working!") // Just testing the thread
    });

    handle.join().unwrap(); // Make the threads wait here until they are done
    println!("Exiting the program");
}
```

Good. Now let's put it in a ```for``` loop for 0..10:

```rust
fn main() {
    let my_number = 0;

    let handle = std::thread::spawn(|| {
        for _ in 0..10 {
            println!("The thread is working!")
        }
    });

    handle.join().unwrap();
    println!("Exiting the program");
}
```

Now let's make one more thread. Each thread 

```rust
fn main() {
    let my_number = 0;

    let thread1 = std::thread::spawn(|| {
        for _ in 0..10 {
            println!("Thread 1 is working!")
        }
    });

    let thread2 = std::thread::spawn(|| {
        for _ in 0..10 {
            println!("Thread 2 is working!")
        }
    });

    thread1.join().unwrap();
    thread2.join().unwrap();
    println!("Exiting the program");
}
```

Now we want to change the value of ```my_number```. Right now it is an ```i32```. We will change it to an ```Arc<Mutex<i32>>```: an ```i32``` that can be changed, protected by an ```Arc```.

```rust
let my_number = Arc::new(Mutex::new(0));
```

Now that we have this, we can clone it. Each clone can go into a different thread. We have two threads, so we will make two clones:

```rust
    let my_number = Arc::new(Mutex::new(0));

    let my_number1 = Arc::clone(&my_number); // This clone goes into Thread 1
    let my_number2 = Arc::clone(&my_number); // This clone goes into Thread 2
```

Now that we have safe clones attached to ```my_number```, we can ```move``` them into other threads with no problem.

```rust
use std::sync::{Arc, Mutex};

fn main() {
    let my_number = Arc::new(Mutex::new(0));

    let my_number1 = Arc::clone(&my_number);
    let my_number2 = Arc::clone(&my_number);

    let thread1 = std::thread::spawn(move || { // Only the clone goes into Thread 1
        for _ in 0..10 {
            *my_number1.lock().unwrap() +=1; // Lock the Mutex, change the value
        }
    });

    let thread2 = std::thread::spawn(move || { // Only the clone goes into Thread 2
        for _ in 0..10 {
            *my_number2.lock().unwrap() += 1;
        }
    });

    thread1.join().unwrap();
    thread2.join().unwrap();
    println!("Value is: {:?}", my_number);
    println!("Exiting the program");
}
```

The program prints:

```
Value is: Mutex { data: 20 }
Exiting the program
```

So it was a success.

Then we can join the two threads together in a single ```for``` loop, and make the code smaller.

```rust
use std::sync::{Arc, Mutex};

fn main() {
    let my_number = Arc::new(Mutex::new(0));

    for _ in 0..2 { // do this twice
        let my_number_clone = Arc::clone(&my_number); // Make the clone before starting the thread
        let handle = std::thread::spawn(move || {
            for _ in 0..10 {
                *my_number_clone.lock().unwrap() += 1;
            }
        });
        handle.join().unwrap(); // wait here
    }

    println!("{:?}", my_number);
}
```

This looks complicated but ```Arc<Mutex>>``` is used very often in Rust, so it becomes natural. Also, you can always write your code to make it cleaner. Here is the same code with one more ```use``` statement and two functions. The functions don't do anything new, but they move some code out of ```main()```. You can try rewriting code like this if it is hard to read.

```rust
use std::sync::{Arc, Mutex};
use std::thread::spawn; // Now we just write spawn

fn make_arc(number: i32) -> Arc<Mutex<i32>> { // Just a function to make a Mutex in an Arc
    Arc::new(Mutex::new(number))
}

fn new_clone(input: &Arc<Mutex<i32>>) -> Arc<Mutex<i32>> { // Just a function so we can write new_clone
    Arc::clone(&input)
}

// Now main() is easier to read

fn main() {
    let my_number = make_arc(0);

    for _ in 0..2 {
        let my_number_clone = new_clone(&my_number);
        let handle = spawn(move || {
            for _ in 0..10 {
                let mut value_inside = my_number_clone.lock().unwrap(); // Give the MutexGuard to a variable so it's clear
                *value_inside += 1;  // Now it is clear that the value inside is changing
            }
        });
        handle.join().unwrap();
    }

    println!("{:?}", my_number);
}
```

You can also make a vector of handles, and use ```.join().unwrap()``` on them. Then you can do what you want with each handle inside. Here is ```main()``` with the handles in a vector:

```rust
fn main() {
    let mut handle_vec = vec![]; // each handle will go in here
    let my_number = make_arc(0);

    for _ in 0..2 {
        let my_number_clone = new_clone(&my_number);
        let handle = spawn(move || {
            for _ in 0..10 {
                let mut value_inside = my_number_clone.lock().unwrap();
                *value_inside += 1;
            }
        });
        handle_vec.push(handle);    // the handle is done, so put it in the vector
    }

    handle_vec.into_iter().for_each(|handle| handle.join().unwrap()); // Make each one wait

    println!("{:?}", my_number);
}
```
