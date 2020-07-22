# Chapter 31
# Closures

Closures are like quick functions that don't need a name. Sometimes they are called lambdas. Closures are easy to find because they use ```||``` instead of ```()```.

You can bind a closure to a variable, and then it looks like a function:

```rust
fn main() {
    let my_closure = || println!("This is a closure");
    my_closure();
}
```

So this closure takes nothing: ```||``` and prints a message.

In between the ```||``` we can add input variables and types:

```rust
fn main() {
    let my_closure = |x: i32| println!("{}", x);

    my_closure(5);
}
```
When the closure becomes more complicated, you can add a code block. Then it can be as long as you want.

```rust
fn main() {
    let my_closure = || {
        let number = 7;
        let other_number = 10;
        println!("The two numbers are {} and {}.", number, other_number);
          // This closure can be as long as we want, just like a function.
    };

    my_closure();
}
```

But closures are special because they can take variables outside the closure. So you can do this:

```rust
fn main() {
    let number_one = 6;
    let number_two = 10;

    let my_closure = || println!("{}", number_one + number_two);
    my_closure();
}
```

So this prints ```16```. You didn't need to put anything inside ```||``` because the closure can just take them.

And you can do this:

```rust
fn main() {
    let number_one = 6;
    let number_two = 10;

    let my_closure = |x: i32| println!("{}", number_one + number_two + x);
    my_closure(5);
}
```

This closure takes ```number_one``` and ```number_two```. We also gave it a new variable ```x``` and said that ```x``` is 5. Then it adds all three together.

Usually you see closures in Rust inside of a method, because it is very convenient to have a closure inside. For example, there is the ```unwrap_or``` method that we know that you can use to give a value if ```unwrap``` doesn't work. Before, we wrote: ```let fourth = my_vec.get(3).unwrap_or_(&0);```. But there is also an ```unwrap_or_else``` method that has a closure inside. So you can do this:

```rust
fn main() {
    let my_vec = vec![8, 9, 10];

    let fourth = my_vec.get(3).unwrap_or_else(|| { // try to unwrap. If it doesn't work,
        if my_vec.get(0).is_some() { // see if my_vec has something at index [0]
            &my_vec[0] // Give the number at index 0 if there is something
        } else {
            &0 // otherwise give a &0
        }
    });

    println!("{}", fourth);
}
```

Of course, a closure can be very simple. You can just write ```let fourth = my_vec.get(3).unwrap_or_else(|| &0);``` for example. As long as you put the ```||``` in, the compiler knows that you have put in the closure that you need.

The most frequent closure method is maybe ```.map()```. This method does something to each item. Here is one way to use it:

```rust
fn main() {
    let num_vec = vec![2, 4, 6];

    let double_vec = num_vec  // take num_vec
        .iter() // iterate over it
        .map(|number| number * 2) // for each item, multiply by two
        .collect::<Vec<i32>>(); // then make a new Vec from this
    println!("{:?}", double_vec);
}
```

Another good example is with ```.enumerate()```. This gives an iterator with the index number, and the item. For example: ```[10, 9, 8]``` becomes ```(0, 10), (1, 9), (2, 8)```. So you can do this:

```rust
fn main() {
    let num_vec = vec![10, 9, 8];

    num_vec
        .iter() // iterate over num_vec
        .enumerate() // get (index, number)
        .for_each(|(index, number)| println!("Index number {} has number {}", index, number)); // do something for each one
}
```

This prints:

```
Index number 0 has number 10
Index number 1 has number 9
Index number 2 has number 8
```

In this case we use ```for_each``` instead of ```map```. ```map``` is for **doing something to** each item and passing it on, and ```for_each``` is **doing something when you see each item**. Also, ```map``` doesn't do anything unless you use a method like ```collect```.

Actually, this is the interesting thing about iterators. If you try to ```map``` without a method like ```collect```, the compiler will tell you that it doesn't do anything:

```rust
fn main() {
    let num_vec = vec![10, 9, 8];

    num_vec
        .iter()
        .enumerate()
        .map(|(index, number)| println!("Index number {} has number {}", index, number));

}
```

It says:

```
warning: unused `std::iter::Map` that must be used
 --> src\main.rs:4:5
  |
4 | /     num_vec
5 | |         .iter()
6 | |         .enumerate()
7 | |         .map(|(index, number)| println!("Index number {} has number {}", index, number));
  | |_________________________________________________________________________________________^
  |
  = note: `#[warn(unused_must_use)]` on by default
  = note: iterators are lazy and do nothing unless consumed
```

This is a **warning**, so it's not an error: the program runs fine. But why doesn't num_vec do anything? We can look at the types to see.

* ```let num_vec = vec![10, 9, 8];``` Right now it is a ```Vec<i32>```.
* ```.iter()``` Now it is an ```Iter<i32>```. So it is an iterator with items of ```i32```.
* ```.enumerate()``` Now it is an ```Enumerate<Iter<i32>>```. So it is a type ```Enumerate``` of type ```Item``` of ```i32```s.
* ```.map()``` Now it is a type ```Map<Enumerate<Iter<i32>>>```. So it is a type ```Map``` of type ```Enumerate``` of type ```Item``` of ```i32```s.

So this ```Map<Enumerate<Iter<i32>>>``` is a structure that is ready to go, when we tell it what to do. Rust does this because it needs to be fast. It doesn't want to do this:

* iterate over all the i32s in the Vec
* enumerate over all the i32s from the iterator
* map over all the enumerated i32s

Rust only wants to do one calculation, so it creates the structure and waits. Then if we say ```.collect::<Vec<i32>>()``` it knows what to do, and starts moving. This is what ```iterators are lazy and do nothing unless consumed``` means. The iterators don't do anything until you "consume" them (use them up).

## |_| in a closure

Sometimes you see ```|_|``` in a closure. This means that the closure needs an argument, but you don't need to use it. So ```|_|``` means "Okay, here is the argument but I won't give it a name because I don't care about it".

Here is an example of an error:

```rust
fn main() {
    let my_vec = vec![8, 9, 10];

    println!("{:?}", my_vec.iter().for_each(|| println!("We didn't use the variables at all")));
}
```

Rust says that 

```
error[E0593]: closure is expected to take 1 argument, but it takes 0 arguments
  --> src\main.rs:28:36
   |
28 |     println!("{:?}", my_vec.iter().for_each(|| println!("We didn't use the variables at all")));
   |                                    ^^^^^^^^ -- takes 0 arguments
   |                                    |
   |                                    expected closure that takes 1 argument
```

The compiler actually gives you some help:

```
help: consider changing the closure to take and ignore the expected argument
   |
28 |     println!("{:?}", my_vec.iter().for_each(|_| println!("We didn't use the variables at all")));
```

This is good advice. If you change ```||``` to ```|_|``` then it will work.




## The dbg! macro and .inspect
dbg!() is a very useful macro that prints quick information. Sometimes you use it instead of ```println!``` because it is faster to type:

```rust
fn main() {

    let my_number = 8;
    dbg!(my_number);

}
```

This prints ```[src\main.rs:4] my_number = 8```.

But actually, you can put ```dbg!``` in many other places. Look at this code for example:

```rust
fn main() {

    let mut my_number = 9;
    my_number += 10;

    let new_vec = vec![8, 9, 10];

    let double_vec = new_vec.iter().map(|x| x * 2).collect::<Vec<i32>>();

}
```

This code creates a new mutable number and changes it. Then it creates a vec, and uses ```iter``` and ```map``` and ```collect``` to create a new vec. We can put ```dbg!``` almost everywhere in this code. ```dbg!``` asks the compiler: "What are you doing here?"

```rust
fn main() {

    let mut my_number = dbg!(9);
    dbg!(my_number += 10);

    let new_vec = dbg!(vec![8, 9, 10]);

    let double_vec = dbg!(new_vec.iter().map(|x| x * 2).collect::<Vec<i32>>());

    dbg!(double_vec);

}
```

So this prints:

```rust
[src\main.rs:3] 9 = 9
```

and:

```rust
[src\main.rs:4] my_number += 10 = ()
```

and:

```rust
[src\main.rs:6] vec![8, 9, 10] = [
    8,
    9,
    10,
]
```

and:

```rust
[src\main.rs:8] new_vec.iter().map(|x| x * 2).collect::<Vec<i32>>() = [
    16,
    18,
    20,
]
```

and:

```rust
[src\main.rs:10] double_vec = [
    16,
    18,
    20,
]
```


```.inspect``` is a bit similar to ```dbg!``` but you use it like ```map```. For example, let's look at our ```double_vec``` again.

```rust
fn main() {

    let new_vec = vec![8, 9, 10];

    let double_vec = new_vec
        .iter()
        .map(|x| x * 2)
        .collect::<Vec<i32>>();

}
```

We want to know more information about what the code is doing. So we add ```inspect``` in two places:

```rust
fn main() {

    let new_vec = vec![8, 9, 10];

    let double_vec = new_vec
        .iter()
        .inspect(|first_item| println!("The item is: {}", first_item))
        .map(|x| x * 2)
        .inspect(|next_item| println!("Then it is: {}", next_item))
        .collect::<Vec<i32>>();

}
```

This prints:

```
The item is: 8
Then it is: 16
The item is: 9
Then it is: 18
The item is: 10
Then it is: 20
```

And because ```.inspect``` takes a closure, we can write as much as we want:

```rust
fn main() {
    let new_vec = vec![8, 9, 10];

    let double_vec = new_vec
        .iter()
        .inspect(|first_item| {
            println!("The item is: {}", first_item);
            match **first_item % 2 { // first item is a &&i32 so we use **
                0 => println!("It is even."),
                _ => println!("It is odd."),
            }
            println!("In binary it is {:b}.", first_item);
        })
        .map(|x| x * 2)
        .collect::<Vec<i32>>();
}
```

This prints:

```
The item is: 8
It is even.
In binary it is 1000.
The item is: 9
It is odd.
In binary it is 1001.
The item is: 10
It is even.
In binary it is 1010.
```
