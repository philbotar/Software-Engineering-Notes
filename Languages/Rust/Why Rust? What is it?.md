# What is Rust?

Rust is a systems programming language with a focus on performance, safety and concurrency.

## Why do we have Rust as a Language?

- Rust was created in 2006 with the goal of Replacing C++ for building large, complex systems without the issues that C++ introduces.

## How did it improved Concurrency and Memory Safety?

It did this through a strict set of rules at compile time. The set of rules being called **Ownership**.

## Ownership

**Ownership** works like so: If i create a value, and then i move that value to another variable, in other languages that value would either be duplicated or the variable would be a reference to the same piece of memory, in rust we are only allowed one variable to have ownership.

```
// Create a string. s1 is the owner.
let s1 = String::from("hello");

// Give the leash to s2. Ownership is MOVED to s2.
let s2 = s1;
```

What if we just want to read the piece of information?

## Borrowing

**Immutable Borrowing** is the concept of providing a reference to the variable you own. Instead of "moving" it somewhere, you just provide a reference to it. Then anyone with that reference can read that variable without changing it. The variable is denoted with a & prepending it. You can have as many of these as you want.

---

**Mutable Borrowing** is the concept of providing a mutable reference to a variable. You provide a reference that can change the data. To stop race conditions, Rust enforces a strict rule of only allowing one mutable borrow of a piece of information in a specific scope.yes

Think of this as lending your book to an editor. While they have it to make changes, nobody else—not even you, the owner—can access it. This ensures they can work without their changes conflicting with someone else's.

## What about Concurrency ?

_What is Concurrency?_

- Concurrency is when a program does multiple things simultaneously. In order to do this, we need to create multiple _Threads_.

_What are Threads?_

- A thread is a sequence of instructions that a program can execute. Multi-threading can be thought of multiple things being done at the same time.

_How does Rust approach Threading?_

- From what we just learnt about borrowing and Ownership, this ties into threading where many threads can read data, but only one thread can change data at a time. This is checked at compile rather than execution and is why Rust can handle concurrency better than other languages.

## Answering the Why

Why we use Rust is because it gives us the performance of C++ without the issues around

1. Memory Bugs (e.g. Dangling Points), as theyre solved by the **Ownership** system.
2. Concurrency Bugs (e.g. Race Conditions), as theyre solved by the **Borrowing** rules.
