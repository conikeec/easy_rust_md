# Chapter 15
# Collection types

Here are some types for making a collection.

## Arrays

An array is data inside square brackets: ```[]```. Arrays:

* must not change their size,
* must only contain the same type.

They are very fast, however.

The type of an array is: ```[type; number]```. For example, the type of ["One", "Two"] is [&std; 2]. This means that even these two arrays have different types:

```rust
let array1 = ["One", "Two"];
let array["One", "Two", "Five"];
```

A good tip: to know the type of a variable, you can "ask" the compiler by giving it bad instructions. For example:

```rust
fn main() {
    let seasons = ["Spring", "Summer", "Autumn", "Winter"];
    let seasons2 = ["Spring", "Summer", "Fall", "Autumn", "Winter"];
    let () = seasons; // This will make an error
    let () = seasons2; // This will also make an error
}
```

The compiler says "seasons isn't type () and seasons2 isn't type () either!" as you can see:

```
error[E0308]: mismatched types
 --> src\main.rs:4:9
  |
4 |     let () = seasons;     
  |         ^^   ------- this expression has type `[&str; 4]`
  |         |
  |         expected array `[&str; 4]`, found `()`

error[E0308]: mismatched types
 --> src\main.rs:5:9
  |
5 |     let () = seasons2;
  |         ^^   -------- this expression has type `[&str; 5]`
  |         |
  |         expected array `[&str; 5]`, found `()`
```

If you want an array with all the same value, you can declare it like this:

```rust
fn main() {
    let my_array = ["a"; 20];
    println!("{:?}", my_array);
}
```

This prints ```["a", "a", "a", "a", "a", "a", "a", "a", "a", "a", "a", "a", "a", "a", "a", "a", "a", "a", "a", "a"]```.

This method is used a lot to create buffers. For example, ```let mut buffer = [0; 640]``` creates an array of 640 zeroes. Then we can change zero to other numbers in order to add data.

You can index (find) entries in an array with []. The first entry is [0], the second is [1], and so on.

```rust
fn main() { 
    let my_numbers = [0, 10, -20];
    println!("{}", my_numbers[1]); // prints 10
}
```

You can get a slice (a piece) of an array. First you need a &, because the compiler doesn't know the size. Then you can use .. to show the range.

For example, let's use this array: ```[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]```.

```rust
fn main() {
    let array_of_ten = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

    let three_to_five = &array_of_ten[2..5];
    let start_at_two = &array_of_ten[1..];
    let end_at_five = &array_of_ten[..5];
    let everything = &array_of_ten[..];

    println!("Three to five: {:?},
start at two: {:?}
end at five: {:?}
everything: {:?}", three_to_five, start_at_two, end_at_five, everything);
}
```

Remember that:
* Index numbers start at 0 (not 1)
* Index ranges are exclusive (they do not include the last number)

So [0..2] means the first index and the second index (0 and 1). Or you can call it the "zeroth and first" index.
