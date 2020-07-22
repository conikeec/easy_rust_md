# Chapter 43
# Channels

A channel is an easy way to use many threads that send to one place. You can create a channel in Rust with ```std::sync::mpsc```. ```mpsc``` means "multiple producer, single consumer", so "many threads sending to one place". To start a channel, you use ```channel()```. This creates a ```Sender``` and a ```Receiver``` that are tied together. You can see this in the function signature:

```rust
pub fn channel<T>() -> (Sender<T>, Receiver<T>)
```

So you have to choose one name for the sender and one for the receiver. Usually you see ```let (sender, receiver) = channel();``` to start. Because it's generic, Rust won't know the type if that is all you write:

```rust
use std::sync::mpsc::channel;

fn main() {
    let (sender, receiver) = channel();
}
```

The compiler says:

```
error[E0282]: type annotations needed for `(std::sync::mpsc::Sender<T>, std::sync::mpsc::Receiver<T>)`
  --> src\main.rs:30:30
   |
30 |     let (sender, receiver) = channel();
   |         ------------------   ^^^^^^^ cannot infer type for type parameter `T` declared on the function `channel`
   |         |
   |         consider giving this pattern the explicit type `(std::sync::mpsc::Sender<T>, std::sync::mpsc::Receiver<T>)`, where 
the type parameter `T` is specified
```

It suggests adding a type for the ```Sender``` and ```Receiver```. You can do that if you want:

```rust
use std::sync::mpsc::{channel, Sender, Receiver}; // Added Sender and Receiver here

fn main() {
    let (sender, receiver): (Sender<i32>, Receiver<i32>) = channel();
}
```

but you don't have to. Once you start using the ```Sender``` and ```Receiver```, Rust can guess the type.

So let's look at the simplest way to use a channel.

```rust
use std::sync::mpsc::channel;

fn main() {
    let (sender, receiver) = channel();

    sender.send(5);
    receiver.recv();

}
```

Now the compiler knows the type. ```sender``` is a ```Result<(), SendError<i32>>``` and ```receiver``` is a ```Result<i32, RecvError```. So you can use ```unwrap``` to see if the sending works, or use better error handling. Let's add ```.unwrap()``` and also ```println!``` to see what we get:

```rust
use std::sync::mpsc::channel;

fn main() {
    let (sender, receiver) = channel();

    sender.send(5).unwrap();
    println!("{}", receiver.recv().unwrap());

}
```

This prints ```5```.

A ```channel``` is like an ```Arc``` because you can clone it and send the clones into other threads. Let's make two threads and send values to ```receiver```. This code will work, but it is not exactly what we want.

```rust
use std::sync::mpsc::channel;

fn main() {
    let (sender, receiver) = channel();
    let sender_clone = sender.clone();
    
    std::thread::spawn(move|| { // move sender in
        sender.send("Send a &str this time").unwrap();
    });

    std::thread::spawn(move|| { // move sender_clone in
        sender_clone.send("And here is another &str");
    });

    println!("{}", receiver.recv().unwrap());

}
```

The two threads start sending, and then we ```println!```. Sometimes it will say ```Send a &str this time``` and sometimes it will say ```And here is another &str```. Let's make a join handle to make them wait.

```rust
use std::sync::mpsc::channel;

fn main() {
    let (sender, receiver) = channel();
    let sender_clone = sender.clone();
    let mut handle_vec = vec![]; // Put our handles in here
    
    handle_vec.push(std::thread::spawn(move|| {  // push this into the vec
        sender.send("Send a &str this time").unwrap();
    }));

    handle_vec.push(std::thread::spawn(move|| {  // and push this into the vec
        sender_clone.send("And here is another &str").unwrap();
    }));

    for _ in handle_vec { // now handle_vec has 2 items. Let's print them
        println!("{:?}", receiver.recv().unwrap());
    }
}
```

This prints:

```
"Send a &str this time"
"And here is another &str"
```

Now let's make a ```results_vec``` instead of printing.

```rust
use std::sync::mpsc::channel;

fn main() {
    let (sender, receiver) = channel();
    let sender_clone = sender.clone();
    let mut handle_vec = vec![];
    let mut results_vec = vec![];
    
    handle_vec.push(std::thread::spawn(move|| {
        sender.send("Send a &str this time").unwrap();
    }));

    handle_vec.push(std::thread::spawn(move|| {
        sender_clone.send("And here is another &str").unwrap();
    }));

    for _ in handle_vec { 
        results_vec.push(receiver.recv().unwrap());
    }

    println!("{:?}", results_vec);
}
```

Now the results are in our vec: ```["Send a &str this time", "And here is another &str"]```.

Now let's pretend that we have a lot of work to do, and want to use threads. We have a big vec with 10,000 items, all 0. We want to change each 0 to a 1. We will use ten threads, and each thread will do one tenth of the work. We will create a new vec and use ```.extend()``` to put the work in.

```rust
use std::sync::mpsc::channel;

fn main() {
    let (sender, receiver) = channel();
    let hugevec = vec![0; 1000];
    let mut newvec = vec![];

    for i in 0..10 {
        let sender_clone = sender.clone();
        let mut work: Vec<u8> = Vec::with_capacity(hugevec.len() / 10); // new vec to put the work in. 1/10th the size
        work.extend(&hugevec[i*100..(i+1)*100]); // first part gets 0..100, next gets 100..200, etc.
        let handle = std::thread::spawn(move || { // make a handle
            
            for number in work.iter_mut() { // do the actual work
                *number += 1;
            };
            sender_clone.send(work).unwrap(); // use the sender_clone to send the work to the receiver
        });

        handle.join().unwrap(); // stop the thread until it's done
        newvec.push(receiver.recv().unwrap()); // push the results from receiver.recv() into the vec
    }

	// Now we have a Vec<Vec<u8>>. To put it together we can use .flatten()
    let newvec = newvec.into_iter().flatten().collect::<Vec<u8>>(); // Now it's one vec of 1000 u8 numbers
}
```

If you print this you can see 1000 number 1s.
