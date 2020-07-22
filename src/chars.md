# Chapter 3
# Chars

A ```char``` is one character. For a ```char```, use ```''``` instead of ```""```.

All chars are 4 bytes. They are 4 bytes because some characters in a string are more than one byte. For example:

```rust
fn main() {
    let slice = "Hello!";
    println!("Slice is {:?} bytes.", std::mem::size_of_val(slice)); // std::mem::size_of_val gives the size in bytes
    let slice2 = "안녕!"; // Korean for "hi"
    println!("Slice2 is {:?} bytes.", std::mem::size_of_val(slice2));
}
```

```slice``` is six characters in length and six bytes, but ```slice2``` is three characters in length and seven bytes. ```char``` needs to fit any character in any language, so it is 4 bytes long.


 