# Chapter 19
# Structs

With structs, you can create your own type. Structs are created with the keyword ```struct```. The name of a struct should be in UpperCamelCase (capital letter for each word, no spaces).

There are three types of structs. One is a "unit struct". Unit means "doesn't have anything".

```rust
struct FileDirectory;
```

The next is a ```tuple struct```, or an ```unnamed struct```. It is "unnamed" because you only need to write the types, not the variable names. Tuple structs are good when you need a simple struct and don't need to remember names.

```rust
struct Colour(u8, u8, u8);

fn main() {
    let my_colour = Colour(50, 0, 50); // Make a colour out of RGB (red, green, blue)
    println!("The second part of the colour is: {}", my_colour.1);
}
```

The third type is the named struct. This is probably the most common struct. In this struct you declare variable names and types inside a ```{}```code block.

```rust
struct Colour(u8, u8, u8); // Declare the same Colour tuple struct

struct SizeAndColour {
    size: u32,
    colour: Colour, // And we put it in our new named struct
}

fn main() {
    let my_colour = Colour(50, 0, 50);
    
    let size_and_colour = SizeAndColour {
        size: 150,
        colour: my_colour
    };
}
```

Let's create a ```Country``` struct to give an example. The ```Country``` struct has the fields ```population```, ```capital```, and ```leader_name```.

```rust
struct Country {
    population: u32,
    capital: String,
    leader_name: String
}

fn main() {
    let population = 500_000;
    let capital = String::from("Elist");
    let leader_name = String::from("Batu Khasikov");

    let kalmykia = Country {
        population: population,
        capital: capital,
        leader_name: leader_name,
    };

}
```

Did you notice that we wrote the same thing twice? Actually, you don't need to do that. If the field name and variable name are the same, you don't have to write it twice.

```rust
struct Country {
    population: u32,
    capital: String,
    leader_name: String
}

fn main() {
    let population = 500_000;
    let capital = String::from("Elist");
    let leader_name = String::from("Batu Khasikov");

    let kalmykia = Country {
        population,
        capital,
        leader_name,
    };

}
```
