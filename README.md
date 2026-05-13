# Rust Programming Comprehensive Cheatsheet
> **Complete Rust reference covering syntax, ownership, lifetimes, generics, traits, collections, error handling, concurrency, smart pointers, modules, testing, and Cargo commands. Includes 22 sections with code examples—from basics to advanced features like async, macros, and unsafe Rust. Perfect for quick lookup.**

## Table of Contents

1. [Basic Syntax](#1-basic-syntax)
2. [Variables and Mutability](#2-variables-and-mutability)
3. [Data Types](#3-data-types)
4. [Functions](#4-functions)
5. [Control Flow](#5-control-flow)
6. [Ownership and Borrowing](#6-ownership-and-borrowing)
7. [Structs](#7-structs)
8. [Enums and Pattern Matching](#8-enums-and-pattern-matching)
9. [Collections](#9-collections)
10. [Error Handling](#10-error-handling)
11. [Generics](#11-generics)
12. [Traits](#12-traits)
13. [Lifetimes](#13-lifetimes)
14. [Closures](#14-closures)
15. [Iterators](#15-iterators)
16. [Smart Pointers](#16-smart-pointers)
17. [Concurrency](#17-concurrency)
18. [Modules and Crates](#18-modules-and-crates)
19. [Testing](#19-testing)
20. [Advanced Features](#20-advanced-features)
21. [Common Macros](#21-common-macros)
22. [Cargo Commands](#22-cargo-commands)

---

## 1. Basic Syntax

### Hello World
```rust
fn main() {
    println!("Hello, World!");
}
```

### Comments
```rust
// Line comment

/* Block comment
   Multi-line */

/// Documentation comment (outer)
//! Inner documentation comment
```

### Printing
```rust
println!("Hello");                    // with newline
print!("Hello");                      // without newline
println!("Number: {}", 42);           // placeholder
println!("{0} {1} {0}", 1, 2);        // positional
println!("{name}: {age}", name="Bob", age=30); // named
println!("{:b}", 10);                 // binary: 1010
println!("{:x}", 255);                // hex: ff
println!("{:.2}", 3.14159);           // 2 decimal places
```

---

## 2. Variables and Mutability

### Variable Declaration
```rust
let x = 5;           // immutable (default)
let mut y = 5;       // mutable
y = 10;              // OK

const MAX_POINTS: u32 = 100_000;  // constant (always immutable)
static HELLO: &str = "Hello";      // static variable
```

### Shadowing
```rust
let x = 5;
let x = x + 1;        // shadowing (new variable)
let x = "hello";      // type can change
```

### Destructuring
```rust
let (a, b) = (1, 2);
let [x, y] = [1, 2];
let Point { x, y } = point;
```

---

## 3. Data Types

### Scalar Types
```rust
// Integers
let a: i8 = -128;      // -128 to 127
let b: u8 = 255;       // 0 to 255
let c: i16 = -32768;   // i32, i64, i128, isize, usize
let d: u32 = 42;       // u16, u64, u128

// Floats
let e: f32 = 3.14;      // f64 (double) default
let f: f64 = 2.71828;

// Boolean
let g: bool = true;

// Character
let h: char = '😀';     // 4 bytes Unicode
```

### Compound Types
```rust
// Tuple
let tup: (i32, f64, char) = (500, 6.4, 'A');
let (a, b, c) = tup;   // destructuring
let first = tup.0;      // index access

// Array (fixed length, same type)
let arr: [i32; 5] = [1, 2, 3, 4, 5];
let zeros = [0; 10];    // array of 10 zeros
let first = arr[0];

// Slice (reference to contiguous sequence)
let slice: &[i32] = &arr[1..4];  // [2, 3, 4]
let slice_full = &arr[..];        // whole array
```

### Type Aliases
```rust
type Kilometers = i32;
let distance: Kilometers = 100;
```

---

## 4. Functions

### Basic Function
```rust
fn add(x: i32, y: i32) -> i32 {
    x + y    // no semicolon = expression (return value)
}

fn print_message() {
    println!("Hello");  // no return value -> ()
}
```

### Function with Return
```rust
fn divide(x: f64, y: f64) -> Result<f64, String> {
    if y == 0.0 {
        Err(String::from("Division by zero"))
    } else {
        Ok(x / y)
    }
}
```

### Methods
```rust
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
    
    fn new(width: u32, height: u32) -> Self {  // associated function
        Self { width, height }
    }
}
```

---

## 5. Control Flow

### if-else
```rust
let number = 6;
if number % 4 == 0 {
    println!("divisible by 4");
} else if number % 3 == 0 {
    println!("divisible by 3");
} else {
    println!("not divisible");
}

// if as expression
let condition = true;
let number = if condition { 5 } else { 6 };
```

### Loop
```rust
// Infinite loop
loop {
    println!("forever");
    break;  // exit loop
}

// while loop
let mut n = 0;
while n < 5 {
    println!("{}", n);
    n += 1;
}

// for loop (range)
for i in 0..5 {      // 0 to 4
    println!("{}", i);
}

for i in 0..=5 {     // 0 to 5 (inclusive)
    println!("{}", i);
}

// Iterate over collection
let fruits = vec!["apple", "banana", "cherry"];
for fruit in &fruits {
    println!("{}", fruit);
}

// Loop with index
for (i, fruit) in fruits.iter().enumerate() {
    println!("{}: {}", i, fruit);
}
```

### Match
```rust
let x = 1;
match x {
    1 => println!("one"),
    2 => println!("two"),
    3 | 4 => println!("three or four"),
    5..=10 => println!("five to ten"),
    _ => println!("other"),  // catch-all
}

// match as expression
let result = match x {
    1 => "one",
    2 => "two",
    _ => "other",
};
```

### if let
```rust
let some_option = Some(5);
if let Some(x) = some_option {
    println!("{}", x);
}

let optional = Some(7);
if let Some(7) = optional {
    println!("seven");
}
```

### while let
```rust
let mut stack = Vec::new();
stack.push(1);
stack.push(2);

while let Some(top) = stack.pop() {
    println!("{}", top);
}
```

---

## 6. Ownership and Borrowing

### Ownership Rules
1. Each value has a variable (owner)
2. Only one owner at a time
3. When owner goes out of scope, value dropped

### Move Semantics
```rust
let s1 = String::from("hello");
let s2 = s1;           // s1 moved to s2 (s1 invalid)
// println!("{}", s1); // ERROR: s1 moved

let x = 5;
let y = x;             // Copy (integers are Copy trait)
println!("{}", x);     // OK
```

### Clone (Deep Copy)
```rust
let s1 = String::from("hello");
let s2 = s1.clone();   // Deep copy
println!("{} {}", s1, s2); // OK
```

### Borrowing (References)
```rust
// Immutable reference (multiple allowed)
let s1 = String::from("hello");
let len = calculate_length(&s1);  // borrow s1

fn calculate_length(s: &String) -> usize {
    s.len()  // s is dropped, but not the value
}

// Mutable reference (only one allowed)
let mut s = String::from("hello");
change(&mut s);

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

### Borrowing Rules
- One mutable OR any number of immutable references
- References must always be valid (no dangling)

### Dereferencing
```rust
let mut x = 5;
let y = &mut x;
*y += 1;  // dereference mutable reference
```

---

## 7. Structs

### Defining Structs
```rust
// Classic struct
struct Person {
    name: String,
    age: u32,
    email: String,
}

// Tuple struct
struct Color(u8, u8, u8);
let black = Color(0, 0, 0);

// Unit struct (no fields)
struct AlwaysEqual;
let subject = AlwaysEqual;
```

### Creating and Using
```rust
let person = Person {
    name: String::from("Alice"),
    age: 30,
    email: String::from("alice@example.com"),
};

// Access fields
println!("{}", person.name);

// Update syntax
let person2 = Person {
    name: String::from("Bob"),
    ..person  // remaining fields from person
};

// Tuple struct access
let Color(r, g, b) = black;
println!("{} {} {}", r, g, b);
println!("{}", black.0);
```

### Methods and Associated Functions
```rust
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    // Method (takes self)
    fn area(&self) -> u32 {
        self.width * self.height
    }
    
    // Method with parameters
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
    
    // Associated function (like static method)
    fn square(size: u32) -> Self {
        Self { width: size, height: size }
    }
}

let rect = Rectangle { width: 30, height: 50 };
println!("Area: {}", rect.area());
let square = Rectangle::square(10);
```

### Derivable Traits
```rust
#[derive(Debug, Clone, Copy, PartialEq, Eq, Hash)]
struct Point {
    x: i32,
    y: i32,
}
```

---

## 8. Enums and Pattern Matching

### Defining Enums
```rust
enum IpAddr {
    V4(String),
    V6(String),
}

enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(u8, u8, u8),
}

// With methods
impl Message {
    fn call(&self) {
        // method body
    }
}
```

### Option Enum
```rust
enum Option<T> {
    Some(T),
    None,
}

let some_number = Some(5);
let some_string = Some("hello");
let absent_number: Option<i32> = None;

// Using Option
match some_number {
    Some(i) => println!("{}", i),
    None => println!("none"),
}
```

### Result Enum
```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}

let result = std::fs::read_to_string("file.txt");
match result {
    Ok(content) => println!("{}", content),
    Err(error) => eprintln!("Error: {}", error),
}
```

### Methods on Option/Result
```rust
// Option methods
Some(5).unwrap();           // returns value or panics
Some(5).unwrap_or(0);       // returns value or default
Some(5).expect("message");  // panic with custom message
Some(5).is_some();          // true if Some
None.is_none();             // true if None

// Result methods
Ok(5).unwrap();             // returns value or panics
Ok(5).unwrap_or(0);         // returns value or default
Ok(5).is_ok();              // true if Ok
Err(5).is_err();            // true if Err
Ok(5).ok();                 // convert to Option
```

### Pattern Matching
```rust
let x = Some(5);
match x {
    Some(y) if y < 5 => println!("less than 5"),
    Some(y) => println!("{}", y),
    None => println!("none"),
}

// Match guards
match value {
    Some(x) if x < 5 => println!("less"),
    Some(x) => println!("{}", x),
    None => (),
}

// @ bindings
match value {
    Some(x @ 1..=5) => println!("{} in range", x),
    Some(x) => println!("{} outside range", x),
    None => (),
}
```

---

## 9. Collections

### Vector (Vec<T>)
```rust
// Creation
let mut v: Vec<i32> = Vec::new();
let v = vec![1, 2, 3];

// Adding
v.push(4);

// Access
let third = &v[2];          // panics if out of bounds
let third = v.get(2);       // returns Option

// Iteration
for i in &v {
    println!("{}", i);
}

for i in &mut v {
    *i += 10;  // mutate values
}

// Removing
v.pop();                    // removes last element
v.remove(1);                // removes element at index

// Utilities
v.len();                    // length
v.is_empty();               // check empty
v.contains(&2);             // check if contains
v.sort();                   // sort
v.reverse();                // reverse
v.dedup();                  // remove consecutive duplicates
```

### HashMap<K, V>
```rust
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);

// Access
let blue_score = scores.get("Blue");  // Option<&V>
let blue_score = scores["Blue"];      // panics if not found

// Iterate
for (key, value) in &scores {
    println!("{}: {}", key, value);
}

// Update
scores.entry(String::from("Blue")).or_insert(25);
scores.entry(String::from("Red")).or_insert(100);

// Remove
scores.remove("Blue");
```

### HashSet<T>
```rust
use std::collections::HashSet;

let mut set = HashSet::new();
set.insert(1);
set.insert(2);

set.contains(&1);  // check membership
set.remove(&1);    // remove
```

### String
```rust
let mut s = String::new();
let s = "hello".to_string();
let s = String::from("hello");

// Concatenation
let s1 = String::from("Hello");
let s2 = String::from("World");
let s3 = s1 + &s2;  // s1 moved, can't use further

// Format macro
let s = format!("{}-{}-{}", a, b, c);

// Manipulation
s.push('c');        // add char
s.push_str("har");  // add string
s.pop();            // remove last char

// Slicing (can panic on char boundaries)
let hello = "hello";
let slice = &hello[0..4];  // "hell"

// Iteration
for c in "नमस्ते".chars() {  // characters
    println!("{}", c);
}
for b in "नमस्ते".bytes() {   // raw bytes
    println!("{}", b);
}
```

---

## 10. Error Handling

### Panic (Unrecoverable)
```rust
panic!("crash and burn");

// Debug assertions
debug_assert!(condition);
assert_eq!(a, b);
assert_ne!(a, b);
```

### Result (Recoverable)
```rust
use std::fs::File;
use std::io::ErrorKind;

let file = File::open("hello.txt");
let file = match file {
    Ok(file) => file,
    Err(error) => match error.kind() {
        ErrorKind::NotFound => match File::create("hello.txt") {
            Ok(fc) => fc,
            Err(e) => panic!("Problem creating file: {:?}", e),
        },
        other_error => panic!("Problem opening file: {:?}", other_error),
    },
};
```

### Shortcuts
```rust
// unwrap - panics if Err
let file = File::open("hello.txt").unwrap();

// expect - panics with custom message
let file = File::open("hello.txt").expect("Failed to open file");

// ? operator - propagate error
fn read_file() -> Result<String, io::Error> {
    let mut s = String::new();
    File::open("hello.txt")?.read_to_string(&mut s)?;
    Ok(s)
}
```

### Custom Error Types
```rust
use std::fmt;

#[derive(Debug)]
struct AppError {
    message: String,
}

impl fmt::Display for AppError {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "{}", self.message)
    }
}

impl std::error::Error for AppError {}

fn do_something() -> Result<(), AppError> {
    Err(AppError { message: "Something went wrong".to_string() })
}
```

---

## 11. Generics

### Generic Functions
```rust
fn largest<T: PartialOrd>(list: &[T]) -> &T {
    let mut largest = &list[0];
    for item in list {
        if item > largest {
            largest = item;
        }
    }
    largest
}
```

### Generic Structs
```rust
struct Point<T, U> {
    x: T,
    y: U,
}

let integer = Point { x: 5, y: 10 };
let float = Point { x: 1.0, y: 4.0 };
let mixed = Point { x: 5, y: 4.0 };
```

### Generic Enums
```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

### Generic Methods
```rust
impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}

// Specific implementation
impl Point<f32> {
    fn distance_from_origin(&self) -> f32 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}
```

### Multiple Generics
```rust
fn add<T: std::ops::Add<Output = T>>(a: T, b: T) -> T {
    a + b
}
```

---

## 12. Traits

### Defining and Implementing Traits
```rust
trait Summary {
    fn summarize(&self) -> String;
    
    // Default implementation
    fn summarize_author(&self) -> String {
        String::from("Unknown")
    }
}

struct NewsArticle {
    headline: String,
    location: String,
}

impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}, by {}", self.headline, self.location)
    }
}
```

### Trait Bounds
```rust
// Syntax 1
fn notify(item: &impl Summary) {
    println!("{}", item.summarize());
}

// Syntax 2 (trait bound)
fn notify<T: Summary>(item: &T) {
    println!("{}", item.summarize());
}

// Multiple traits
fn notify(item: &(impl Summary + Display)) {}

fn notify<T: Summary + Display>(item: &T) {}

// where clause
fn some_function<T, U>(t: &T, u: &U) -> i32
where
    T: Display + Clone,
    U: Clone + Debug,
{
    42
}
```

### Returning Trait Types
```rust
fn returns_summarizable() -> impl Summary {
    NewsArticle {
        headline: String::from("News"),
        location: String::from("NYC"),
    }
}
```

### Derivable Traits
```rust
#[derive(Debug, Clone, Copy, PartialEq, Eq, PartialOrd, Ord, Hash)]
struct MyStruct {
    value: i32,
}
```

### Common Traits

| Trait | Purpose |
|-------|---------|
| `Debug` | Formatting with `{:?}` |
| `Clone` | Explicit deep copy |
| `Copy` | Implicit shallow copy |
| `PartialEq` | Equality comparison (`==`, `!=`) |
| `Eq` | Full equivalence |
| `PartialOrd` | Partial ordering |
| `Ord` | Total ordering |
| `Hash` | Hashing for HashMap |
| `Default` | Default values |
| `Display` | Formatted output (`{}`) |
| `From/Into` | Type conversions |

---

## 13. Lifetimes

### Lifetime Annotations
```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}
```

### Struct with Lifetime
```rust
struct ImportantExcerpt<'a> {
    part: &'a str,
}

impl<'a> ImportantExcerpt<'a> {
    fn level(&self) -> i32 {
        3
    }
    
    fn announce_and_return_part(&self, announcement: &str) -> &str {
        println!("{}", announcement);
        self.part
    }
}
```

### Lifetime Elision Rules
```rust
// Single input -> output gets same lifetime
fn first_word(s: &str) -> &str {}

// Multiple inputs, one is self or &self
fn method(&self, s: &str) -> &str {}
```

### Static Lifetime
```rust
let s: &'static str = "I live forever";
```

---

## 14. Closures

### Defining Closures
```rust
// Full syntax
let add_one = |x: i32| -> i32 { x + 1 };

// Inferred types
let add_one = |x| x + 1;

// Multiple parameters
let add = |x, y| x + y;

// No parameters
let random = || 42;
```

### Capture Environment
```rust
let x = 4;
let equal_to_x = |z| z == x;

// Move ownership
let y = vec![1, 2, 3];
let move_closure = move |z| z == y;
```

### Closures as Parameters
```rust
fn call_with_one<F>(some_closure: F) -> i32
where
    F: Fn(i32) -> i32,
{
    some_closure(1)
}

// Different traits
// Fn: borrows immutably
// FnMut: borrows mutably
// FnOnce: takes ownership
```

### Closure Examples
```rust
let numbers = vec![1, 2, 3];
let doubled: Vec<_> = numbers.iter().map(|x| x * 2).collect();

let mut counter = 0;
let mut increment = || counter += 1;
increment();
```

---

## 15. Iterators

### Creating Iterators
```rust
let v = vec![1, 2, 3];
let iter = v.iter();        // Iterator over &T
let iter_mut = v.iter_mut(); // Iterator over &mut T
let into_iter = v.into_iter(); // Iterator over T (consumes)
```

### Iterator Adaptors (Lazy)
```rust
// map - transform each element
let doubled: Vec<_> = v.iter().map(|x| x * 2).collect();

// filter - keep elements satisfying condition
let evens: Vec<_> = v.iter().filter(|&&x| x % 2 == 0).collect();

// enumerate - add index
for (i, val) in v.iter().enumerate() {
    println!("{}: {}", i, val);
}

// zip - combine two iterators
let sum_pairs: Vec<_> = v1.iter().zip(v2.iter()).collect();

// take - first n elements
let first_two: Vec<_> = v.iter().take(2).collect();

// skip - skip first n elements
let rest: Vec<_> = v.iter().skip(2).collect();

// rev - reverse
let reversed: Vec<_> = v.iter().rev().collect();

// chain - concatenate iterators
let chain = a.iter().chain(b.iter());
```

### Consumer Methods
```rust
let v = vec![1, 2, 3];

let sum: i32 = v.iter().sum();
let product: i32 = v.iter().product();

let min = v.iter().min();
let max = v.iter().max();

let any_even = v.iter().any(|&x| x % 2 == 0);
let all_positive = v.iter().all(|&x| x > 0);

let first = v.iter().find(|&&x| x > 1);
let position = v.iter().position(|&x| x == 2);

let count = v.iter().count();
let folded = v.iter().fold(0, |acc, &x| acc + x);
```

### Custom Iterator
```rust
struct Counter {
    count: u32,
}

impl Counter {
    fn new() -> Counter {
        Counter { count: 0 }
    }
}

impl Iterator for Counter {
    type Item = u32;
    
    fn next(&mut self) -> Option<Self::Item> {
        self.count += 1;
        if self.count < 6 {
            Some(self.count)
        } else {
            None
        }
    }
}
```

---

## 16. Smart Pointers

### Box<T> (Heap allocation)
```rust
let b = Box::new(5);
println!("b = {}", b);

// Recursive type (cons list)
enum List {
    Cons(i32, Box<List>),
    Nil,
}
use List::{Cons, Nil};
let list = Cons(1, Box::new(Cons(2, Box::new(Cons(3, Box::new(Nil))))));
```

### Rc<T> (Reference counted)
```rust
use std::rc::Rc;

let a = Rc::new(String::from("hello"));
let b = Rc::clone(&a);  // increase ref count
let c = Rc::clone(&a);

println!("count: {}", Rc::strong_count(&a));
```

### RefCell<T> (Interior mutability)
```rust
use std::cell::RefCell;

let data = RefCell::new(5);
*data.borrow_mut() = 10;  // mutate through immutable reference
println!("{}", data.borrow());
```

### Combined Patterns
```rust
use std::rc::Rc;
use std::cell::RefCell;

// Multiple ownership of mutable data
let shared = Rc::new(RefCell::new(5));
let a = Rc::clone(&shared);
let b = Rc::clone(&shared);

*a.borrow_mut() += 1;
println!("{}", b.borrow());
```

### Weak<T> (Prevent cycles)
```rust
use std::rc::{Rc, Weak};

struct Node {
    value: i32,
    parent: RefCell<Weak<Node>>,
    children: RefCell<Vec<Rc<Node>>>,
}
```

### Deref and Drop Traits
```rust
use std::ops::{Deref, Drop};

impl<T> Deref for MyBox<T> {
    type Target = T;
    
    fn deref(&self) -> &Self::Target {
        &self.0
    }
}

impl<T> Drop for MyBox<T> {
    fn drop(&mut self) {
        println!("Dropping!");
    }
}
```

---

## 17. Concurrency

### Threads
```rust
use std::thread;
use std::time::Duration;

// Spawn thread
let handle = thread::spawn(|| {
    for i in 1..10 {
        println!("number {} from spawned thread", i);
        thread::sleep(Duration::from_millis(1));
    }
});

for i in 1..5 {
    println!("number {} from main thread", i);
    thread::sleep(Duration::from_millis(1));
}

handle.join().unwrap();  // wait for thread
```

### Move Closures into Threads
```rust
let v = vec![1, 2, 3];

let handle = thread::spawn(move || {
    println!("{:?}", v);
});

handle.join().unwrap();
// v no longer accessible
```

### Message Passing (mpsc)
```rust
use std::sync::mpsc;

let (tx, rx) = mpsc::channel();

thread::spawn(move || {
    tx.send(String::from("hello")).unwrap();
});

let received = rx.recv().unwrap();  // blocking
println!("Got: {}", received);

// Multiple producers
let tx1 = tx.clone();
```

### Shared State (Mutex)
```rust
use std::sync::{Arc, Mutex};

let counter = Arc::new(Mutex::new(0));
let mut handles = vec![];

for _ in 0..10 {
    let counter = Arc::clone(&counter);
    let handle = thread::spawn(move || {
        let mut num = counter.lock().unwrap();
        *num += 1;
    });
    handles.push(handle);
}

for handle in handles {
    handle.join().unwrap();
}

println!("Result: {}", *counter.lock().unwrap());
```

### Atomic Types
```rust
use std::sync::atomic::{AtomicUsize, Ordering};

let atomic = AtomicUsize::new(0);
atomic.fetch_add(1, Ordering::SeqCst);
atomic.store(42, Ordering::SeqCst);
println!("{}", atomic.load(Ordering::SeqCst));
```

### Send and Sync Traits
- `Send`: Types that can be transferred between threads
- `Sync`: Types that can be shared between threads

---

## 18. Modules and Crates

### Module System
```rust
// lib.rs or main.rs
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}
        fn seat_at_table() {}
    }
    
    mod serving {
        fn take_order() {}
        fn serve_order() {}
    }
}
```

### Paths
```rust
// Absolute path
crate::front_of_house::hosting::add_to_waitlist();

// Relative path
front_of_house::hosting::add_to_waitlist();

// super (parent module)
fn serve_order() {}
mod back_of_house {
    fn fix_incorrect_order() {
        super::serve_order();
    }
}
```

### Visibility
```rust
pub fn public_function() {}      // public
fn private_function() {}         // private

pub mod module {                  // public module
    pub fn public() {}            // public function
    fn private() {}               // private function
}

pub struct PublicStruct {
    pub public_field: i32,        // public field
    private_field: i32,           // private field
}

pub enum PublicEnum {
    Variant1,                     // all variants public
    Variant2,
}
```

### Using Keyword
```rust
use std::collections::HashMap;

// Multiple imports
use std::fmt::{self, Display, Debug};

// Nested paths
use std::{
    collections::HashMap,
    fs::File,
    io::{self, Read},
};

// Glob import
use std::collections::*;

// Renaming (as)
use std::fmt::Result as FmtResult;
```

### External Crates (Cargo.toml)
```toml
[dependencies]
serde = "1.0"
rand = "0.8"
tokio = { version = "1.0", features = ["full"] }
```

### Re-exporting
```rust
pub use self::hosting::add_to_waitlist;  // re-export
```

---

## 19. Testing

### Unit Tests
```rust
#[cfg(test)]
mod tests {
    use super::*;
    
    #[test]
    fn it_works() {
        assert_eq!(2 + 2, 4);
    }
    
    #[test]
    fn another_test() {
        panic!("This test fails!");
    }
}
```

### Assertions
```rust
assert!(condition);
assert_eq!(a, b);
assert_ne!(a, b);

// With custom messages
assert!(condition, "Custom message: {}", value);
assert_eq!(a, b, "Expected {} but got {}", b, a);
```

### Test Attributes
```rust
#[test]
#[ignore]  // ignore this test
fn expensive_test() {
    // code
}

#[test]
#[should_panic(expected = "specific error message")]
fn test_panic() {
    panic!("specific error message");
}

#[test]
#[should_panic]
fn test_generic_panic() {
    panic!("any error");
}
```

### Result-based Tests
```rust
#[test]
fn test_result() -> Result<(), String> {
    if 2 + 2 == 4 {
        Ok(())
    } else {
        Err(String::from("two plus two does not equal four"))
    }
}
```

### Integration Tests (tests directory)
```rust
// tests/integration_test.rs
use my_crate;

#[test]
fn integration_test() {
    assert_eq!(my_crate::add(2, 2), 4);
}
```

### Running Tests
```bash
cargo test                    # run all tests
cargo test -- --nocapture    # show println! output
cargo test test_name         # run specific test
cargo test -- --ignored      # run ignored tests
cargo test --test integration_test  # specific integration test
```

---

## 20. Advanced Features

### Unsafe Rust
```rust
// Dereference raw pointers
let mut num = 5;
let r1 = &num as *const i32;
let r2 = &mut num as *mut i32;
unsafe {
    println!("r1 is: {}", *r1);
    println!("r2 is: {}", *r2);
}

// Call unsafe functions
unsafe fn dangerous() {}
unsafe { dangerous(); }

// Access/modify mutable static variables
static mut COUNTER: u32 = 0;
unsafe { COUNTER += 1; }

// Implement unsafe trait
unsafe trait Foo {}
unsafe impl Foo for i32 {}
```

### Macros (Declarative)
```rust
macro_rules! vec {
    ( $( $x:expr ),* ) => {
        {
            let mut temp_vec = Vec::new();
            $(
                temp_vec.push($x);
            )*
            temp_vec
        }
    };
}

// Usage
let v = vec![1, 2, 3];
```

### Procedural Macros (derive)
```rust
// In a separate crate
use proc_macro::TokenStream;

#[proc_macro_derive(HelloMacro)]
pub fn hello_macro_derive(input: TokenStream) -> TokenStream {
    // implementation
}
```

### Associated Types
```rust
trait Iterator {
    type Item;  // associated type
    
    fn next(&mut self) -> Option<Self::Item>;
}
```

### Operator Overloading
```rust
use std::ops::Add;

#[derive(Debug, PartialEq)]
struct Point {
    x: i32,
    y: i32,
}

impl Add for Point {
    type Output = Point;
    
    fn add(self, other: Point) -> Point {
        Point {
            x: self.x + other.x,
            y: self.y + other.y,
        }
    }
}
```

### Type Conversions (From/Into)
```rust
impl From<String> for MyType {
    fn from(s: String) -> Self {
        MyType { value: s }
    }
}

let my = MyType::from(String::from("hello"));

// Into is automatic when From is implemented
let my: MyType = String::from("hello").into();
```

---
## 21. Common Macros

| Macro | Purpose |
|-------|---------|
| `println!()` | Print with newline |
| `print!()` | Print without newline |
| `format!()` | Format string |
| `vec![]` | Create vector |
| `assert!()` | Assert condition |
| `assert_eq!()` | Assert equality |
| `unreachable!()` | Mark unreachable code |
| `todo!()` | Mark incomplete code |
| `unimplemented!()` | Mark unimplemented code |
| `panic!()` | Panic with message |
| `include_str!()` | Include file as string |
| `include_bytes!()` | Include file as bytes |
| `concat!()` | Concatenate literals |
| `stringify!()` | Stringify expression |
| `cfg!()` | Compile-time configuration |
| `env!()` | Environment variable |
| `file!()` | Current file |
| `line!()` | Current line |
| `column!()` | Current column |
| `module_path!()` | Current module path |

---

## 22. Cargo Commands

### Basic Commands
```bash
cargo new project_name     # create new binary project
cargo new --lib project_name  # create new library
cargo build                # build project
cargo build --release      # build with optimizations
cargo run                  # build and run
cargo check                # check without building
cargo test                 # run tests
cargo doc                  # generate documentation
cargo doc --open           # open docs in browser
cargo clean                # remove target directory
cargo update               # update dependencies
```

### Advanced Commands
```bash
cargo add crate_name       # add dependency
cargo remove crate_name    # remove dependency
cargo tree                 # show dependency tree
cargo fmt                  # format code
cargo clippy               # lint code
cargo bench                # run benchmarks
cargo install crate_name   # install binary crate
cargo uninstall crate_name # uninstall
cargo publish              # publish to crates.io
cargo login                # login to crates.io
```

### Workspace Commands
```bash
cargo new my-workspace --bin
# Create Cargo.toml workspace
[workspace]
members = ["adder", "subtractor"]
```

### Configuration (config.toml)
```toml
[build]
rustflags = ["-C", "target-cpu=native"]

[target.x86_64-unknown-linux-gnu]
linker = "clang"

[alias]
a = "run --release"
t = "test"
```

---

## Quick Reference

### Common Patterns

**Read file contents:**
```rust
use std::fs;
let contents = fs::read_to_string("file.txt")?;
```

**Write to file:**
```rust
use std::fs::File;
use std::io::Write;
let mut file = File::create("file.txt")?;
file.write_all(b"Hello")?;
```

**Parse string to number:**
```rust
let num: i32 = "42".parse()?;
```

**Match multiple patterns:**
```rust
match value {
    1 | 2 | 3 => println!("1-3"),
    4..=10 => println!("4-10"),
    _ => (),
}
```

**String to Vec<char>:**
```rust
let chars: Vec<char> = "hello".chars().collect();
```

**Vec to String:**
```rust
let s: String = vec!['h', 'e', 'l', 'l', 'o'].into_iter().collect();
```

**Sleep/delay:**
```rust
use std::thread;
use std::time::Duration;
thread::sleep(Duration::from_secs(1));
thread::sleep(Duration::from_millis(100));
```

**Time measurement:**
```rust
use std::time::Instant;
let start = Instant::now();
// code
println!("{:?}", start.elapsed());
```

---

*This cheatsheet covers the most essential Rust programming concepts. For more details, refer to [The Rust Book](https://doc.rust-lang.org/book/) and [Rust by Example](https://doc.rust-lang.org/rust-by-example/).*
