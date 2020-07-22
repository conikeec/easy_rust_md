# Chapter 41
# Multiple threads

If you use multiple threads, you can do many things at the same time. Rust uses threads that are called "OS threads". OS thread means the operating system creates the thread on a different core.

You create threads with ```std::thread::spawn``` and then a closure to tell it what to do. Threads are interesting because they run at the same time. Here is a simple example:

```rust
fn main() {
    std::thread::spawn(|| {
        println!("I am printing something");
    });
}
```

If you run this, it will be different every time. Sometimes it will print, and sometimes it won't print. That is because sometimes ```main()``` finishes before the thread finishes. And when ```main()``` finishes, the program is over. This is easier to see in a ```for``` loop:

```rust
fn main() {
    for _ in 0..10 { // set up ten threads
    std::thread::spawn(|| {
        println!("I am printing something");
    });
	// Now the threads start.
}
}       // How many can finish before main() ends here?
```

Usually about four threads will print before ```main``` ends, but it is always different. Also, sometimes the threads will panic:

```rust
thread 'thread 'I am printing something
thread '<unnamed><unnamed>thread '' panicked at '<unnamed>I am printing something
' panicked at 'thread '<unnamed>cannot access stdout during shutdown' panicked at '<unnamed>thread 'cannot access stdout during 
shutdown
```

This is the error when the thread tries to do something right when the program is shutting down.

You can give the computer something to do so it won't shut down right away:

```rust
fn main() {
    for _ in 0..10 {
    std::thread::spawn(|| {
        println!("I am printing something");
    });
    for i in 0..1_000_000 { // make the program declare "let x = 9" one million times
        let x = 9;
    }
}
```

But that is a silly way to give the threads time to finish. The better way is to bind the threads to a variable. If you add ```let```, then you will create a ```JoinHandle```.

```rust
fn main() {
    for _ in 0..10 {
        let handle = std::thread::spawn(|| {
            println!("I am printing something");
        });

    }
}
```

```handle``` is now a ```JoinHandle```, and it has the method ```.join()```. This method means "wait until all the threads are done" (it waits for the threads to join it). So now just write ```handle.join()``` and it will wait for all ten threads to finish.

```rust
fn main() {
    for _ in 0..10 {
        let handle = std::thread::spawn(|| {
            println!("I am printing something");
        });

        handle.join(); // Wait for the threads to finish
    }
}
```

Now we will learn about the three types of closures. The three types are:

* FnOnce: takes the whole value
* FnMut: takes a mutable reference
* Fn: takes a regular reference

A closure will try to use ```Fn``` if it can. But if it needs to mutate the value, it will use ```FnMut```. And if it needs to take the whole value, it will use ```FnOnce```. FnOnce is a good name because it explains what it does: it takes the value once, and then it can't take it again.

Here is an example:

```rust
fn main() {

    let my_string = String::from("I will go into the closure");
    let my_closure = || println!("{}", my_string);
    my_closure();
    my_closure();
}
```

String is not Copy, so my_closure() is Fn: it takes a reference.

If we change ```my_string```, it will be ```FnMut```.


```rust
fn main() {

    let mut my_string = String::from("I will go into the closure");
    let mut my_closure = || { 
        my_string.push_str(" now");
        println!("{}", my_string);
    };
    my_closure();
    my_closure();
```

This prints:

```
I will go into the closure now
I will go into the closure now now
```

And if you take by value, then it will be FnOnce.

```rust
fn main() {

    let my_vec: Vec<i32> = vec![8, 9, 10];
    let my_closure = || { 
        my_vec
            .into_iter() // into_iter takes ownership
            .map(|x| x as u8) // turn it into u8
            .map(|x| x * 2) // multiply by 2
            .collect::<Vec<u8>>() // collect into a Vec
    };
    let new_vec = my_closure();
    println!("{:?}", new_vec);
}
```

We took by value, so we can't run ```my_closure()``` more than once. That is where the name comes from.

So now back to threads. Let's try to use a value from outside:

```rust
fn main() {
    let mut my_string = String::from("Can I go inside the thread?");

    let handle = std::thread::spawn(|| {
        println!("{}", my_string);
    });

    handle.join();
}
```

The compiler says that this won't work.

```
error[E0373]: closure may outlive the current function, but it borrows `my_string`, which is owned by the current function      
  --> src\main.rs:28:37
   |
28 |     let handle = std::thread::spawn(|| {
   |                                     ^^ may outlive borrowed value `my_string`
29 |         println!("{}", my_string);
   |                        --------- `my_string` is borrowed here
   |
note: function requires argument type to outlive `'static`
  --> src\main.rs:28:18
   |
28 |       let handle = std::thread::spawn(|| {
   |  __________________^
29 | |         println!("{}", my_string);
30 | |     });
   | |______^
help: to force the closure to take ownership of `my_string` (and any other referenced variables), use the `move` keyword
   |
28 |     let handle = std::thread::spawn(move || {
   |                                     ^^^^^^^
```

It is a long message, but helpful: it says to ```use the `move` keyword```. The problem is that we can do anything to ```my_string``` while the thread is using it. That would be unsafe.

```rust
fn main() {
    let mut my_string = String::from("Can I go inside the thread?");

    let handle = std::thread::spawn(|| {
        println!("{}", my_string); // now my_string is being used as a reference
    });

    std::mem::drop(my_string); // Maybe we drop it. But the thread still needs it

    handle.join();
}
```

So you have to take the value with ```move```. Now it is safe:

```rust
fn main() {
    let mut my_string = String::from("Can I go inside the thread?");

    let handle = std::thread::spawn(move|| {
        println!("{}", my_string);
    });

    std::mem::drop(my_string); // we can't drop, because handle has it. So this won't work

    handle.join();
}
```

So we delete the ```std::mem::drop```, and now it is okay. ```handle``` takes ```my_string``` and our code is safe.

```rust
fn main() {
    let mut my_string = String::from("Can I go inside the thread?");

    let handle = std::thread::spawn(move|| {
        println!("{}", my_string);
    });

    handle.join();
}
```

So just remember: if you need a value in a thread from outside the thread, you need to use ```move```.
