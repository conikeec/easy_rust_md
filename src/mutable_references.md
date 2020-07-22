# Chapter 12
# Mutable references

If you want to use a reference to change data, you can use a mutable reference. For a mutable reference, you write ```&mut```.

```rust
fn main() {
    let mut my_number = 8; // don't forget to write mut here!
    let num_ref = &mut my_number;
}
```

So what are the two types? ```my_number``` is an ```i32```, and ```num_ref``` is ```&mut i32``` (a "mutable reference to an i32).

So let's use it to add 10 to my_number. But you can't write ```num_ref += 10```, because ```num_ref``` is not the ```i32``` value. To reach the value, we use ```*```. ```*``` means "I don't want the reference, I want the value behind the reference". In other words, one ```*``` erases one ```&```.

```rust
fn main() {
    let mut my_number = 8; 
    let num_ref = &mut my_number;
    *num_ref += 10; // Use * to change the i32 value.
    println!("{}", my_number);
}
```

Because using ```&``` is called "referencing", using ```*``` is called "**de**referencing".


Rust has rules for mutable and immutable references.

Rule 1: If you have only immutable references, you can have as many as you want.
Rule 2: If you have a mutable references, you can only have one. You can't have an immutable reference **and** a mutable reference.

This is because mutable references can change the data. You could get problems if you change the data when other references are reading it.

A good way to understand is to think of a Powerpoint presentation.

Situation one is about only one mutable reference. 

Situation one: An employee is writing a Powerpoint presentation. He wants his manager to help him. The employee gives his login information to his manager, and asks him to help by making edits. Now the manager has a "mutable reference" to the employee's presentation. This is fine, because nobody else is looking at the presentation.

Situation two is about only immutable references.

Situation two: The employee is giving the presentation to 100 people. All 100 people can now see the employee's data. This is fine, because nobody can change the data.

Situation three is the problem situation.

Situation three: The Employee his manager his login information. Then the employee went to give the presentation to 100 people. This is not fine, because the manager can log in and do anything. Maybe his manager will log into the computer and type an email to his mother? Now the 100 people have to watch the manager write an email to his mother instead of the presentation. That's not what they expected to see.

Here is an example of a mutable borrow with an immutable borrow:

```rust
fn main() {
    let mut number = 10;
    let number_ref = &number; 
    let number_change = &mut number; 
    *number_change += 10;
    println!("{}", number_ref); 
}
```

The compiler prints a helpful message to show us the problem.

```
error[E0502]: cannot borrow `number` as mutable because it is also borrowed as immutable
 --> src\main.rs:4:25
  |
3 |     let number_ref = &number;
  |                      ------- immutable borrow occurs here
4 |     let number_change = &mut number;
  |                         ^^^^^^^^^^^ mutable borrow occurs here
5 |     *number_change += 10;
6 |     println!("{}", number_ref);
  |                    ---------- immutable borrow later used here
```

However, this code will work. Why?

```rust
fn main() {
    let mut number = 10;
    let number_change = &mut number; // create a mutable reference
    *number_change += 10; // use mutable reference to add 10
    let number_ref = &number; // create an immutable reference
    println!("{}", number_ref); // print the immutable reference
}
```

The compiler knows that we used ```number_change``` to change ```number```, but didn't use it again. So here there is no problem. We are not using immutable and mutable references together.

## Shadowing again

Remember when we said that shadowing doesn't **destroy** a value but **blocks** it? Now we can use references to see this.

```rust
fn main() {
    let country = String::from("Austria");
    let country_ref = &country;
    let country = 8;
    println!("{}, {}", country_ref, country);

}
```

Does this print ```Austria, 8``` or ```8, 8```? It prints ```Austria, 8```. First we declare a ```String``` called ```country```. Then we create a reference ```country_ref``` to this string. Then we shadow country with 8, which is an ```i32```. But the first ```country``` was not destroyed, so ```country_ref``` still says "Austria", not "8".
