# Chapter 30
# Iterators

An iterator is a collection that can give you the items in the collection, one at a time. Actually, we have already used iterators: the ```for``` loop gives you an iterator. When you want to use an iterator other times, you have to choose what kind:

* ```.iter()``` for an iterator of references
* ```.iter_mut()``` for an iterator of mutable references
* ```.into_iter()``` for an iterator of values (not references)

We can use them like this:

```rust
fn main() {
    let vector1 = vec![1, 2, 3]; // for .iter() and .into_iter()
    let mut vector2 = vec![10, 20, 30]; // for .iter_mut()

    let vector1_a = vector1.iter().map(|x| x + 1).collect::<Vec<i32>>();
    vector2.iter_mut().for_each(|x| *x +=100);
    let vector1_b = vector1.into_iter().map(|x| x * 10).collect::<Vec<i32>>();

    println!("{:?}", vector1_a);
    println!("{:?}", vector2);
    println!("{:?}", vector1_b);

}
```

First we used ```.iter()``` on ```vector1``` to get references. We added 1 to each, and made it into a new Vec. ```vector1``` is still alive because we only used references: we didn't take by value. Now we have ```vector1```, and a new Vec called ```vector1_a```.

Then we used ```.iter_mut()``` for ```vector2```. It is mutable, so we don't need to use ```.collect()``` to create a new Vec. Instead, we change the values in the same Vec with mutable references. So ```vector2``` is still there. Because we don't need a new Vec, we use ```for_each```: it's just like a ```for``` loop.

Finally we used ```into_iter``` to get an iterator by value from ```vector1```. This destroys ```vector1```, so after we make ```vector1_b``` we can't use ```vector1``` again.

## How does an iterator work?

An iterator works by using a method called ```.next()```, which gives an ```Option```. When you use an iterator, Rust calls ```next()```. If it gets ```Some```, it keeps going. If it gets ```None```, it stops.

In documentation, you see examples like this to show how an iterator works.

```rust
fn main() {
    let my_vec = vec!['a', 'b', '거', '柳'];

    let mut my_vec_iter = my_vec.iter(); // This is an iterator type now, but we haven't called it yet

    assert_eq!(my_vec_iter.next(), Some(&'a')); // Call the first item with .next()
    assert_eq!(my_vec_iter.next(), Some(&'b')); // Call the next
    assert_eq!(my_vec_iter.next(), Some(&'거')); // Again
    assert_eq!(my_vec_iter.next(), Some(&'柳')); // Again
    assert_eq!(my_vec_iter.next(), None);        // Nothing is left: just None
    assert_eq!(my_vec_iter.next(), None); // You can keep calling .next() but it will always be None

}
```

Implementing Iterator for your own struct or enum is not too hard. First let's make a book library and think about it.

```rust
#[derive(Debug)] // we want to print it with {:?}
struct Library {
    library_type: LibraryType, // this is our enum
    books: Vec<String>, // list of books
}

#[derive(Debug)]
enum LibraryType { // libraries can be city libraries or country libraries
    City,
    Country,
}

impl Library {
    fn add_book(&mut self, book: &str) { // we use add_book to add new books
        self.books.push(book.to_string()); // we take a &str and turn it into a String, then add it to the Vec
    }

    fn new() -> Self { // this creates a new Library
        Self {
            library_type: LibraryType::City, // most are in the city so we'll choose City 
                                             // most of the time
            books: Vec::new(),
        }
    }
}

fn main() {
    let mut my_library = Library::new(); // make a new library
    my_library.add_book("The Doom of the Darksword"); // add some books
    my_library.add_book("Demian - die Geschichte einer Jugend");
    my_library.add_book("구운몽");
    my_library.add_book("吾輩は猫である");
    

    println!("{:?}", my_library.books); // we can print our list of books
}
```

That works well. Now we want to implement ```Iterator``` for the library so we can use it in a ```for``` loop. Right now if we try a ```for``` loop, it doesn't work:

```rust
   for item in my_library {
    println!("{}", item);
   }
```

It says: 

```
error[E0277]: `Library` is not an iterator
  --> src\main.rs:47:16
   |
47 |    for item in my_library {
   |                ^^^^^^^^^^ `Library` is not an iterator
   |
   = help: the trait `std::iter::Iterator` is not implemented for `Library`
   = note: required by `std::iter::IntoIterator::into_iter`
```

But we can make library into an iterator with ```impl Iterator for Library```. Information on the ```Iterator``` trait is here in the standard library: https://doc.rust-lang.org/std/iter/trait.Iterator.html

On the top left of the page it says: ```Associated Types: Item``` and ```Required Methods: next```. An "associated type" means "a type that goes together". Our associated type will be ```String```, because we want the iterator to give us Strings.

In the page it has an example that looks like this:

```rust
// an iterator which alternates between Some and None
struct Alternate {
    state: i32,
}

impl Iterator for Alternate {
    type Item = i32;

    fn next(&mut self) -> Option<i32> {
        let val = self.state;
        self.state = self.state + 1;

        // if it's even, Some(i32), else None
        if val % 2 == 0 {
            Some(val)
        } else {
            None
        }
    }
}
```

You can see that under ```impl Iterator for Alternate``` it says ```type Item = i32```. This is the associated type. Our iterator will be for our list of books, which is a ```Vec<String>>```. When we call next, it will give us a ```String```. So we will write ```type Item = String;```. That is the associated item.

To implement ```Iterator```, you need to write the ```fn next()``` function. This is where you decide what the iterator should do. For our ```Library```, we want it to give us the last books first. So we will ```match``` with ```.pop()``` which takes the last item off it it is ```Some```. We also want to print " is found!" for each item. Now it looks like this:

```rust
#[derive(Debug)]
struct Library {
    library_type: LibraryType,
    books: Vec<String>,
}

#[derive(Debug)]
enum LibraryType {
    City,
    Country,
}

impl Library {
    fn add_book(&mut self, book: &str) {
        self.books.push(book.to_string());
    }

    fn new() -> Self {
        Self {
            library_type: LibraryType::City,
            // most of the time
            books: Vec::new(),
        }
    }
}

impl Iterator for Library {
    type Item = String;

    fn next(&mut self) -> Option<String> {
        match self.books.pop() {
            Some(book) => Some(book + " is found!"), // Rust allows String + &str
            None => None,
        }
    }
}

fn main() {
    let mut my_library = Library::new();
    my_library.add_book("The Doom of the Darksword");
    my_library.add_book("Demian - die Geschichte einer Jugend");
    my_library.add_book("구운몽");
    my_library.add_book("吾輩は猫である");

    for item in my_library { // we can use a for loop now
        println!("{}", item);
    }
}
```

This prints:

```
吾輩は猫である is found!
구운몽 is found!
Demian - die Geschichte einer Jugend is found!
The Doom of the Darksword is found!
```
