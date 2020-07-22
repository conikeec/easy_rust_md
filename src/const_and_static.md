# Chapter 10
# const and static
There are two types that don't use ```let``` to declare: ```const``` and ```static```. Also, you need to write the type for them. These are for variables that don't change (```const``` means constant). The difference is that:

* ```const``` is a value that does not change,
* ```static``` is a value that does not change and has a fixed memory location.

So they are almost the same. Rust programmers almost always use ```const```.

You write them with ALL CAPITAL LETTERS, and usually outside of the ```main``` function. 

Two examples are: ```const NUMBER_OF_MONTHS: u32 = 12;``` and ````const SEASONS: [&str; 4] = ["Spring", "Summer", "Fall", "Winter"];```
