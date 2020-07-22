# Chapter 29
# Chaining methods

Rust is a systems programming language, but it also has a functional style. Both styles are okay, but functional style is usually shorter. Here is an example of declarative style to make a Vec from 1 to 10:

```rust
fn main() {
    let mut new_vec = Vec::new();
    let mut counter = 1;

    while counter < 11 {
        new_vec.push(counter);
        counter += 1;
    }

    println!("{:?}", new_vec);
}
```

And here is an example of functional style:

```rust
fn main() {
    let new_vec = (1..=10).collect::<Vec<i32>>();
	// Or you can write it like this:
	// let new_vec: Vec<i32> = (1..=10).collect();
    println!("{:?}", new_vec);
}
```

```.collect()``` can make collections of many types, so we have to tell it the type.

With functional style you can chain methods. That means to put many methods together in a single statement. Here is an example of many methods chained together:

```rust
fn main() {
    let my_vec = vec![0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

    let new_vec = my_vec.into_iter().skip(3).take(4).collect::<Vec<i32>>();

    println!("{:?}", new_vec);
}
```

This creates a Vec with ```[3, 4, 5, 6]```. It is a good idea to put each method on a new line if you have many chained methods. This helps you to read the code. Here is the same code with each method on a new line:

```rust
fn main() {
    let my_vec = vec![0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

    let new_vec = my_vec
        .into_iter() // "iterate" over the items (iterate = work with each separately). into_iter() gives us owned values, not references
        .skip(3) // skip over three items: 0, 1, and 2
        .take(4) // take the next four: 3, 4, 5, and 6
        .collect::<Vec<i32>>(); // put them in a new Vec<i32>

    println!("{:?}", new_vec);
}
```

You can use this functional style best when you understand closures and iterators. So we will learn them next.
