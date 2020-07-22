# Chapter 17
# Tuples

Tuples in Rust use ```()```. We have seen many empty tuples already. ```fn do_something() {}``` has an empty tuple. Also, when you don't return anything in a function, you actually return an empty tuple.

```rust
fn main() {
  
}

fn just_prints() {
    println!("I am printing"); // Adding ; means we return an empty tuple
}
```

But tuples can hold many things, and can hold different types too. To access the items inside of a tuple, don't use ```[]```, use ```.```.

```rust
fn main() {
    let mut new_vec = Vec::new();
    new_vec.push('a');
    let random_tuple = ("Here is a name", 8, new_vec, 'b', [8, 9, 10], 7.7);
    println!(
        "Inside the tuple is: First item: {:?} 
Second item: {:?} 
Third item: {:?} 
Fourth item: {:?} 
Fifth item: {:?} 
Sixth item: {:?}",
        random_tuple.0,
        random_tuple.1,
        random_tuple.2,
        random_tuple.3,
        random_tuple.4,
        random_tuple.5,
    )
}
```


You can use a tuple to create multiple variables.

```rust
fn main() {
    let str_vec = vec!["one", "two", "three"];

    let (a, b, c) = (str_vec[0], str_vec[1], str_vec[2]);
    println!("{:?}", b);
}
```

There are many more collection types, and many more ways to use arrays, vecs, and tuples. We will learn more about them. But first we will learn control flow.
