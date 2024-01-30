+++
title = "Understanding rust ownership and borrowing."
date = 2024-01-23
updated = 2024-01-23
description = "In this blog post I will try to explain the concepts of ownership and borrowing in Rust."

[taxonomies]
tags = ["rust", "learning"]

[extra]
footnote_backlinks = true
quick_navigation_buttons = true
+++


<details>
    <summary><b>Table of Contents</b></summary>
    <!-- toc -->
</details>

# Understanding Rust's Ownership and Borrowing: Building Robust and Memory-Safe Code

## Introduction

Rust's ownership and borrowing system is a distinctive feature that sets it apart from other programming languages. This system is designed to ensure memory safety without the need for a garbage collector. In this blog post, we will delve into the core concepts of ownership and borrowing in Rust, exploring how they contribute to writing robust, high-performance code with minimal runtime overhead.

## 1. The Ownership Model

### 1.1 What is Ownership? 

When you assign a value to a variable, Rust allocates memory to store that value. This memory is automatically freed when the variable (the owner of the value) goes out of scope. This automatic cleanup of memory is done by a piece of code called the "drop" function which is called automatically at the closing brace of the scope.
By enforcing strict ownership rules, Rust prevents common programming errors like null or dangling pointers, double free, and data races. For example, since there can only be one owner at a time, you can't accidentally modify data that's simultaneously being used elsewhere. And because values are automatically cleaned up when their owners go out of scope, you don't have to worry about manually freeing memory and potentially causing double free errors.

### 1.2 Ownership Rules

Ownership is a fundamental concept in Rust that ensures memory safety without the need for a garbage collector. It revolves around three main rules:

1. Each value in Rust has a variable that's called its owner.
2. There can only be one owner at a time.
3. When the owner goes out of scope, the value will be dropped.

### 1.3 Move Semantics

In Rust, the concept of "moving" ownership is a key part of how the language ensures memory safety. When you assign a variable to another variable, Rust by default "moves" the ownership, rather than copying the data. This means that the original variable can no longer be used.

Here's an example:
```
let s1 = String::from("hello");
let s2 = s1;
```

In this code, s1 is a string that owns its data. When we assign s1 to s2, Rust moves the ownership of the data from s1 to s2. Now s2 is the owner of the data, and s1 can no longer be used. If you try to use s1 after this, you'll get a compile-time error.

This behavior prevents multiple variables from pointing to the same memory location, which can lead to data races and other memory-related issues. Since there's always exactly one owner, you don't have to worry about another part of your code unexpectedly changing the data you're working with.

Moreover, this move semantics is also beneficial in terms of performance. Moving ownership often involves simply changing a pointer, which is much faster than copying a large amount of data. And because Rust ensures that the old variable can't be used after the move, it's safe to free the memory as soon as the new owner goes out of scope.

## 2. Borrowing

### 2.1 What is Borrowing?

Borrowing is a key feature in Rust that complements the ownership system. It allows a piece of data to be accessed by different parts of your program without transferring ownership. This is useful when you want to use a value but still want the owner to retain its ownership.

There are two types of borrowing in Rust: immutable and mutable.

### 2.1 Imutable Borrowing
This allows a value to be read from multiple places without changing it. You can have multiple immutable references to the same data. Here's an example:
```
let s = String::from("hello");
let r1 = &s;
let r2 = &s;
println!("{} and {}", r1, r2);
```
In this code, r1 and r2 are both references to s. They "borrow" s without taking ownership, so s is still valid after this code.

### 2.2 Mutable Borrowing

This allows a value to be changed. You can only have one mutable reference to a particular piece of data in a particular scope. This restriction prevents data races at compile time. Here's an example:
```
let mut s = String::from("hello");
let r1 = &mut s;
*r1 += ", world";
println!("{}", r1);
```
In this code, r1 is a mutable reference to s. It can modify s, but no other references to s can exist while r1 is in scope.

By enforcing these rules, Rust's borrowing system allows your program to access data in a controlled and safe manner, preventing a whole class of bugs related to ownership and concurrency.

## 3. Lifetimes

### 3.1 Understanding Lifetimes

In Rust, memory safety is a core principle. One of the ways Rust achieves this is through a feature called 'lifetimes'. Lifetimes are Rust's way of ensuring that all references in your code are valid for as long as you're using them.

A lifetime is a construct in Rust that prevents dangling references by ensuring that any reference to an object will not outlive the object itself. In other words, it guarantees that the referenced object will not be destroyed while there are still references to it.

When you create a reference to an object in Rust, the compiler automatically assigns a lifetime to that reference. This lifetime determines the scope within which that reference is valid. Once you leave that scope, the reference is no longer valid and you can't use it anymore.
Here's a simple example:

```rust
{
    let x = 5; // x has a lifetime for this block
    let r = &x; // r has the same lifetime as x
} // x and r go out of scope here

In this example, x and r have the same lifetime, which is the scope of the block they're in. Once we leave that block, x and r go out of scope and are no longer valid.

### 3.2 Lifetimes Prevent Dangling References
One of the main benefits of lifetimes is that they prevent dangling references. A dangling reference is a reference that points to an object that has already been destroyed. This can lead to undefined behavior and serious bugs in your programs.

By ensuring that a reference cannot outlive the object it refers to, lifetimes prevent this kind of problem. If you try to use a reference after its object has been destroyed, the Rust compiler will give you an error.

### 3.3 Lifetime Annotations

Lifetime annotations in Rust are a way for explicitly specifying the lifetimes of references in function signatures. This is necessary when the lifetimes of arguments or return values depend on each other.

The syntax for lifetime annotations is an apostrophe followed by a name, like `'a`. This name is then used to refer to the lifetime in the rest of the function signature.

Here's an example of a function with lifetime annotations:

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

In this example, 'a is a lifetime annotation. The function signature tells Rust that for some lifetime 'a, the function takes two parameters, both of which are string references that live at least as long as 'a. The function returns a string reference that also lives at least as long as 'a.

This does not change the lifetimes of any values passed in or returned, but it does tell Rust that any values that do not adhere to this contract should be rejected by the compiler. This is the core of Rust's ability to make memory safety guarantees without needing a garbage collector.

## 4. Ownership and Concurrency

### 4.1 Ownership in Concurrent Programming

Discuss how Rust's ownership model facilitates safe concurrent programming. Explain how ownership rules help prevent data races and other common concurrency issues.

## 5. Common Patterns and Best Practices

### 5.1 Structuring Code with Ownership

Provide best practices for structuring code to maximize the benefits of ownership and borrowing. Illustrate common patterns and idioms used in Rust code.

### 5.2 Avoiding Common Pitfalls

Highlight common pitfalls when working with ownership and borrowing. Provide guidance on how to recognize and address these issues.

## 6. Conclusion

Summarize the key takeaways from the article, emphasizing the significance of ownership and borrowing in Rust for achieving memory safety and concurrency without sacrificing performance. Encourage readers to embrace these concepts and explore further by building real-world projects in Rust, leveraging the language's unique features for robust and efficient code.



=====================================================================



# Understanding Ownership and Borrowing in Rust

Rust is a unique programming language with a focus on performance, safety, and especially concurrency. One of the key features that enables Rust to achieve these goals is its system of ownership with a feature called borrowing. Let's dive into these concepts.

## Ownership

In Rust, every value has a variable that's called its _owner_. There can only be one owner at a time, and when the owner goes out of scope, the value will be dropped.

```rust
fn main() {
    let s = String::from("hello");  // s is now the owner of the value
}  // s goes out of scope and the value is dropped
```

This ownership system allows Rust to make memory safety guarantees without needing a garbage collector.

## Borrowing

While ownership is a powerful concept, it would be very limiting if we could never have multiple variables interact with the same piece of data. This is where borrowing comes in.

Borrowing is accomplished with the `&` symbol and allows you to have a reference to a value without taking ownership of it.

```rust
fn main() {
    let s = String::from("hello");
    let len = calculate_length(&s);
    println!("The length of '{}' is {}.", s, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```

In this example, `calculate_length` borrows `s` but does not take ownership, so `s` is still valid after the function call.

## Mutable Borrowing

Rust also allows mutable borrowing, which lets a function modify a value. This is done with the `&mut` symbol.

```rust
fn main() {
    let mut s = String::from("hello");
    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

However, Rust enforces a rule that you can have either one mutable reference or any number of immutable references, but not both. This prevents data races at compile time.

## Conclusion

Ownership and borrowing are fundamental concepts in Rust that enable it to ensure memory safety and prevent data races. By understanding these concepts, you can write efficient and safe Rust code.