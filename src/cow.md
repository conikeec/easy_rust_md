# Chapter 38
# Cow

Cow is a very convenient enum. It means "clone on write" and lets you return a ```&str``` if you don't need a ```String```, and a ```String``` if you need it. (It can also do the same with arrays vs. Vecs, etc.)

To understand it, let's look at the signature. It says:

```rust
pub enum Cow<'a, B> 
where
    B: 'a + ToOwned + ?Sized, 
 {
    Borrowed(&'a B),
    Owned(<B as ToOwned>::Owned),
}
```

You know right away that ```'a``` means it works with references. The ```ToOwned``` trait means that it is a type that can be turned into an owned type. For example, ```str``` is usually a reference (```&str```) and you can turn it into an owned ```String```.

Next is ```?Sized```. This means "maybe Sized, but maybe not". Almost every type in Rust is Sized, but types like ```str``` are not. That is why we need a ```&``` for a ```str```, because the compiler doesn't know the size. So if you want a trait that can use something like a ```str```, you add ```?Sized.```

Next are enum variants. They are ```Borrowed``` and ```Owned```.

Imagine that you have a function that returns ```Cow<'static, str>```. If you tell the function to return ```"My message".into()```, it will look at the type: "My message" is a ```str```. This is a ```Borrowed``` type, so it chooses ```Borrowed(&'a B)```. So it becomes Cow::Borrowed(&'static str)```.

And if you give it a ```format!("{}", "My message".into()``` then it will look at the type. This time it is a ```String```, because ```format!``` makes a ```String```. So this time it will select "Owned".

Here is an example to test ```Cow```. We will put a number into a function that returns a ```Cow<'static, str>```. Depending on the number, it will create a ```&str``` or a ```String```. Then it uses ```.into()``` to turn it into a ```Cow```. When you do that, it will choose either ```Cow:::Borrowed``` or ```Cow::Owned```. Then we will match to see which one it chose.

```rust
use std::borrow::Cow;

fn modulo_3(input: u8) -> Cow<'static, str> {
    
    match input % 3 {
        0 => "Remainder is 0".into(),
        1 => "Remainder is 1".into(),
        remainder => format!("Remainder is {}", remainder).into(),
    }
}

fn main() {
    for number in 1..=6 {
        match modulo_3(number) {
            Cow::Borrowed(message) => println!("{} went in. The Cow is borrowed with this message: {}", number, message),
            Cow::Owned(message) => println!("{} went in. The Cow is owned with this message: {}", number, message),
        }
    }    
}
```

This prints:

```
1 went in. The Cow is borrowed with this message: Remainder is 1
2 went in. The Cow is owned with this message: Remainder is 2
3 went in. The Cow is borrowed with this message: Remainder is 0
4 went in. The Cow is borrowed with this message: Remainder is 1
5 went in. The Cow is owned with this message: Remainder is 2
6 went in. The Cow is borrowed with this message: Remainder is 0
```

```Cow``` has some other methods like ```into_owned``` or ```into_borrowed``` so you can change it if you need to.
