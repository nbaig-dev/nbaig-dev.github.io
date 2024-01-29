+++
title = "My journey learning Rust"
date = 2024-01-23
updated = 2024-01-23
description = "In this blog post I will outline my journey learning The RUst Programming Language."

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

