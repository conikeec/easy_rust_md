# Chapter 8
# The stack, the heap, and pointers

The stack, the heap, and pointers are very important in Rust.

The stack and the heap are two places to keep memory. The important differences are:

* The stack is very fast, but the heap is not so fast.
* The stack needs to know the size of a variable at compile time. So simple variables like i32 go on the stack, because we know their exact size.
* Some types don't know the size at compile time. But the stack needs to know the exact size. What do you do? You put the data in the heap, because the heap can have any size of data. And to find it, a pointer goes on the stack, because we always know the size of a pointer.

A pointer is like a table of contents in a book.

```
MY BOOK

Chapter                        Page
Chapter 1: My life              1
Chapter 2: My cat               15
Chapter 3: My job               23
Chapter 4: My family            30
Chapter 5: Future plans         43
```

So this is like five pointers. Where is the chapter "My life"? It's on page 1 (it points to page 1). Where is the chapter "My job?" It's on page 23.

A pointer in Rust is usually called a **reference**. A reference means you *borrow* the value, but you don't own it. In Rust, references have a ```&```. So:

* ```let my_variable = 8``` makes a regular variable, but 
* ```let my_reference = &my_variable``` makes a reference. 

This means that ```my_reference``` is only looking at the data of ```my_variable```. ```my_variable``` still owns its data.

## More about printing

We know that ```println!``` can print with ```{}``` (for Display) and ```{:?}``` (for Debug), plus ```{:#?}``` for pretty printing. But there are many other ways to print.

For example, if you have a reference, you can use {:p} to print the pointer address.

```ref
fn main() {
    let number = 9;
    let number_ref = &number;
    println!("{:p}", number_ref);
}
```
This prints ```0xe2bc0ffcfc``` or some other address (it can be different every time).

Or you can print binary, hexadecimal and octal:

```ref
fn main() {
    let number = 555;
    println!("Binary: {:b}, hexadecimal: {:x}, octal: {:o}", number, number, number);
}
```

Or you can add numbers to change the order:

```rust
fn main() {
    let father_name = "Vlad";
    let son_name = "Adrian Fahrenheit";
    let family_name = "Țepeș";
    println!("This is {1} {2}, son of {0} {2}.", father_name, son_name, family_name);
}
```

```father_name``` is in position 0, ```son_name``` is in position 1, and ```family_name``` is in position 2. So it prints ```This is Adrian Fahrenheit Țepeș, son of Vlad Țepeș```.

Maybe you have a very complex string to print and want to add names to the ```{}```. You can do that:

```rust
fn main() {
    println!("{city1} is in {country} and {city2} is also in {country},
but {city3} is not in {country}.", city1 = "Seoul", city2 = "Busan", city3 = "Tokyo", country = "Korea");
}
```

Very complex printing is not used too much in Rust. But here is how to do it.

{variable:padding alignment minimum.maximum}

1) Do you want a variable name?
(Then add a ```:```)
2) Do you want a padding character?
3) What alignment (left / middle / right) or the padding?
4) Do you want a minimum length? (just write a number)
5) Do you want a maximum length? (write a number with a ```.``` in front)


For example, if I want to write "a" with five ㅎ characters on the left and five ㅎ characters on the right:

```rust
fn main() {
    let letter = "a";
    println!("{:ㅎ^11}", letter);
}
```

This prints ```ㅎㅎㅎㅎㅎaㅎㅎㅎㅎㅎ```. Let's look at 1) to 5) for this.

* Do you want a variable name? ```{:ㅎ^11}``` No variable name: it comes before ```:```.
* Do you want a padding character? ```{:ㅎ^11}``` Yes. ㅎ comes after the ```:``` and has a ```^```. ```<``` means padding with the character on the left, ```>``` means on the right, and ```^``` means in the middle.
* Do you want a minimum length? ```{:ㅎ^11}``` Yes: there is an 11 after.
* Do you want a maximum length? ```{:ㅎ^11}``` No: there is no number with a ```.``` before.

Here is an example of many types of formatting.
```rust
fn main() {
    let title = "TODAY'S NEWS";
    println!("{:-^30}", title); // no variable name, pad with -, put in centre, 30 characters long
    let bar = "|";
    println!("{: <15}{: >15}", bar, bar); // no variable name, pad with space, 15 characters each, one to the left, one to the right
    let a = "SEOUL";
    let b = "TOKYO";
    println!("{city1:-<15}{city2:->15}", city1 = a, city2 = b); // variable names city1 and city2, pad with -, one to the left, one to the right
}
```

It prints:
```rust
---------TODAY'S NEWS---------
|                            |
SEOUL--------------------TOKYO
```


