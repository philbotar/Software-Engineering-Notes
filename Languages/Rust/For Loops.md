# For Loops in Rust

Whats the difference between a Rust for loop and a Java For Loop? Don't they just do the same thing?

Essentially yes, but the Syntax is different enough to warrant a page on it.

## Lets go through each value in an Array

This for loop is probably the simplest, and easiest to grasp. Its utilising a python-like approach and leverages the iterator so that you dont need to worry about indexing.

```
let fruits = ["Apple", "Banana", "Orange"];

for fruit in fruits {
    println!("I like to eat {}s!", fruit);
}
```

## What about if we have a range of Numbers?

This is what i think is the biggest difference. We usually do a for loop like: for(int i = 0; i < range; i++), the equivalent in Rust is like so

```
for number in 1..4 { // This range includes 1, 2, and 3
    println!("Launch sequence: {}", number);
}
println!("LIFTOFF! 🚀");
```

This though, still doesn't give us an index for an array that is created during execution.

## Lets find the index for our array

We end up utilising enumerate to be able to get our indexes.

You first ensure that you apply .iter(), which is an iterate helper, and then you apply .enumerate() to get the index of the value retrieved. It ends up looking like so:

```
let fruits = ["Apple", "Banana", "Orange"];

// .enumerate() wraps the iterator and adds the index.
// The parentheses (index, fruit) unpack the pair into two variables.
for (index, fruit) in fruits.iter().enumerate() {
    println!("At index {}, we have a(n) {}.", index, fruit);
}
```
