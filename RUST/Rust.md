# Rust Tutorial: Basic to Advanced

This tutorial will take you from writing your first Rust program to building command-line tools, REST APIs, concurrent applications, and production-ready projects. I'll explain the concepts step by step with runnable examples.

Rust is a systems programming language focused on memory safety, performance, and concurrency without requiring a garbage collector.
## 1. What you can build with Rust



CLI tools

Build fast terminal applications, file utilities, search tools, and developer tools.



Backend development

Build REST APIs with frameworks such as Axum or Actix Web, similar to Spring Boot applications.

Systems programming

Build operating-system components, networking tools, embedded software, and performance-critical applications.



Advanced applications

Work with WebAssembly, multithreading, asynchronous networking, and distributed systems.

## 2. Install Rust on Fedora Linux

Rust's recommended toolchain manager is `rustup`.

Open your terminal and run:

```
sudo dnf install gcc gcc-c++ make pkgconf-pkg-config
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Follow the installer instructions, then load the environment:

```
source "$HOME/.cargo/env"
```

Verify the installation:

```
rustc --version
cargo --version
rustup update
```

- `rustc` — Rust compiler.
    
- `cargo` — project creation, dependency management, building, testing, and running.
    
- `rustup` — manages Rust toolchains and targets.
    

## 3. Create your first Rust project

```
cargo new hello_rust
cd hello_rust
cargo run
```

Your project structure will look like this:

```
hello_rust/
├── Cargo.toml
└── src/
    └── main.rs
```

Open `src/main.rs`:

```rust
fn main() {
    println!("Hello, Rust!");
}
```

Run it:

```
cargo run
```

Output:

```
Hello, Rust!
```

Understanding the code

- `fn` declares a function.
    
- `main()` is the program's entry point.
    
- `println!` is a macro that prints text and a newline.
    
- `!` after `println` indicates a macro invocation.
    
- Rust statements usually end with `;`.
    

Useful Cargo commands:

|Command|Purpose|
|---|---|
|`cargo run`|Compile and run|
|`cargo check`|Check code without producing a final executable|
|`cargo build`|Compile the project|
|`cargo build --release`|Optimized production build|
|`cargo test`|Run tests|
|`cargo fmt`|Format code|
|`cargo clippy`|Run additional lint checks|

## 4. Variables and data types

### Variables and mutability

Rust variables are immutable by default.

```rust
fn main() {
    let age = 22;
    println!("Age: {age}");

    let mut score = 10;
    score = 20;

    println!("Score: {score}");
}
```

- `let age = 22;` creates an immutable variable.
    
- `let mut score = 10;` creates a mutable variable.
    
- `{age}` is captured directly inside the format string.
    
- Rust checks types at compile time.
    

Unlike Java, Rust does not use `int`, `String`, or `boolean` as its type names.

### Common data types

|Rust type|Meaning|Example|
|---|---|---|
|`i32`|Signed 32-bit integer|`let x: i32 = -10;`|
|`u32`|Unsigned 32-bit integer|`let x: u32 = 10;`|
|`i64`|Signed 64-bit integer|`let x: i64 = 100;`|
|`f64`|64-bit floating point|`let x: f64 = 3.14;`|
|`bool`|Boolean|`let active: bool = true;`|
|`char`|Unicode scalar value|`let letter: char = 'R';`|
|`String`|Owned, growable text|`String::from("Rust")`|
|`&str`|String slice|`"Rust"`|

Example:

```rust
fn main() {
    let age: u32 = 22;
    let price: f64 = 99.50;
    let active: bool = true;
    let letter: char = 'R';
    let language: &str = "Rust";

    println!("{age}, {price}, {active}, {letter}, {language}");
}
```

### Constants and shadowing

```rust
const MAX_USERS: u32 = 100;

fn main() {
    let number = 10;
    let number = number + 5;

    println!("{number}");
    println!("{MAX_USERS}");
}
```

A `const` must have an explicit type. Shadowing lets you declare a new variable with the same name, even with a different type.

## 5. Operators and control flow

### Conditions

```rust
fn main() {
    let age = 22;

    if age >= 18 {
        println!("Adult");
    } else {
        println!("Minor");
    }
}
```

Rust uses `if` as an expression, too:

```
let age = 22;
let category = if age >= 18 { "Adult" } else { "Minor" };
```

Both branches must produce compatible types.

### Loops

```rust
fn main() {
    for i in 1..=5 {
        println!("{i}");
    }

    let mut count = 3;

    while count > 0 {
        println!("{count}");
        count -= 1;
    }

    loop {
        println!("Runs until break");
        break;
    }
}
```

- `1..5` produces 1, 2, 3, 4.
    
- `1..=5` includes 5.
    
- `loop` repeats indefinitely until stopped or returned from.
    

### `match`

Rust's `match` is similar to Java's `switch`, but it also supports exhaustive pattern matching.

```rust
fn main() {
    let status = 200;

    match status {
        200 => println!("Success"),
        404 => println!("Not found"),
        500 => println!("Server error"),
        _ => println!("Other status"),
    }
}
```

The `_` pattern handles any remaining values.

## 6. Functions

```rust
fn add(a: i32, b: i32) -> i32 {
    a + b
}

fn main() {
    let result = add(10, 20);
    println!("Result: {result}");
}
```

Important details:

- Parameters need types.
    
- `-> i32` declares the return type.
    
- `a + b` has no semicolon because it is the function's return expression.
    
- Adding a semicolon turns it into a statement and would make this function return `()` instead.
    

You can also return early:

```rust
fn divide(a: f64, b: f64) -> f64 {
    if b == 0.0 {
        return 0.0;
    }

    a / b
}
```

This example demonstrates syntax, but a real division API should distinguish division by zero from a valid result of zero. We'll use `Result` later.

## 7. Collections: arrays, vectors, and hash maps

### Arrays

An array has a fixed length, and all elements have the same type.

```rust
fn main() {
    let numbers: [i32; 3] = [10, 20, 30];

    println!("{}", numbers[0]);
    println!("Length: {}", numbers.len());
}
```

`[i32; 3]` means three `i32` elements.

### Vectors

A `Vec<T>` is a growable collection, similar in broad purpose to Java's `ArrayList<T>`.

```rust
fn main() {
    let mut numbers: Vec<i32> = vec![10, 20, 30];

    numbers.push(40);
    numbers.remove(0);

    for number in &numbers {
        println!("{number}");
    }
}
```

Notice `&numbers`: we borrow the vector rather than consume it.

### Hash maps

```rust
use std::collections::HashMap;

fn main() {
    let mut scores = HashMap::new();

    scores.insert("Java", 90);
    scores.insert("Rust", 95);

    if let Some(score) = scores.get("Rust") {
        println!("Rust score: {score}");
    }
}
```

`HashMap<K, V>` stores key-value pairs. Its `get()` method returns an `Option`, which represents either a value or no value.

## 8. Strings: `String` vs `&str`

This is an important distinction in Rust.

```rust
fn main() {
    let name: &str = "Rust";
    let mut message = String::from("Hello");

    message.push_str(" ");
    message.push_str(name);

    println!("{message}");
}
```

- `&str` is a borrowed view of text.
    
- `String` owns its text and can grow.
    
- `push_str()` appends text to a `String`.
    
- `String` is not simply a fixed-size array of characters.
    

Rust strings are UTF-8 encoded. Indexing a string by an integer is not supported because a byte index may not correspond to a valid character boundary.

For Unicode text, use `.chars()` when iterating over Unicode scalar values:

```rust
fn main() {
    let word = "Rust 🦀";

    for ch in word.chars() {
        println!("{ch}");
    }
}
```

## 9. Ownership — the most important Rust concept

Rust's ownership system manages memory without a traditional garbage collector.

Learn these three rules:

1. Every value has an owner.
    
2. There can only be one owner at a time.
    
3. When the owner goes out of scope, the value is dropped.
    

### Example: moving ownership

```rust
fn main() {
    let first = String::from("Hello");
    let second = first;

    println!("{second}");
    // println!("{first}"); // Compile error
}
```

Why does `first` stop working? Assigning an owned `String` to `second` moves ownership. Rust prevents both variables from independently freeing the same allocation.

Compare this with a simple integer:

```rust
fn main() {
    let first = 10;
    let second = first;

    println!("{first} {second}");
}
```

Integers like `i32` implement the `Copy` trait, so assigning them copies the value instead of moving it.

### Cloning

If you need a separate owned copy of a `String`, use `.clone()`:

```rust
fn main() {
    let first = String::from("Hello");
    let second = first.clone();

    println!("{first} {second}");
}
```

Cloning a `String` generally allocates and copies its contents, so use it when you need independent ownership.

## 10. Borrowing and references

Borrowing allows a function to use a value without taking ownership of it.

```rust
fn print_message(message: &String) {
    println!("{message}");
}

fn main() {
    let text = String::from("Hello Rust");

    print_message(&text);
    println!("{text}");
}
```

The `&String` parameter borrows the string. The caller remains its owner.

For a read-only string parameter, `&str` is usually more flexible:

```rust
fn greet(name: &str) {
    println!("Hello, {name}");
}

fn main() {
    let owned = String::from("Developer");

    greet(&owned);
    greet("Rust");
}
```

### Mutable borrowing

```rust
fn add_exclamation(message: &mut String) {
    message.push('!');
}

fn main() {
    let mut text = String::from("Hello");

    add_exclamation(&mut text);

    println!("{text}");
}
```

Rust enforces borrowing rules:

- You can have multiple immutable references at the same time.
    
- You can have one mutable reference at a time.
    
- You cannot simultaneously use ordinary immutable and mutable references to the same data in conflicting ways.
    

These rules prevent many data races and invalid-memory-access bugs at compile time.

## 11. Slices

A slice is a borrowed view of part of a collection.

```rust
fn main() {
    let numbers = [10, 20, 30, 40, 50];
    let part = &numbers[1..4];

    println!("{part:?}");
}
```

Output:

```
[20, 30, 40]
```

String slices work similarly:

```rust
fn first_word(text: &str) -> &str {
    match text.find(' ') {
        Some(index) => &text[..index],
        None => text,
    }
}

fn main() {
    println!("{}", first_word("Hello Rust"));
}
```

Slices avoid copying the entire collection when only a portion is needed.

## 12. Structs — creating your own data types

A Rust `struct` is broadly similar to a Java class used to hold data, but a struct does not itself define inheritance or methods.

```rust
struct User {
    id: u32,
    name: String,
    active: bool,
}

impl User {
    fn new(id: u32, name: String) -> Self {
        Self {
            id,
            name,
            active: true,
        }
    }

    fn display(&self) {
        println!("{}: {}", self.id, self.name);
    }

    fn deactivate(&mut self) {
        self.active = false;
    }
}

fn main() {
    let mut user = User::new(1, String::from("Alex"));

    user.display();
    user.deactivate();

    println!("Active: {}", user.active);
}
```

Understand the syntax:

- `struct User` defines a type.
    
- `impl User` defines methods associated with that type.
    
- `Self` refers to the implementing type.
    
- `&self` borrows the instance immutably.
    
- `&mut self` allows a method to mutate the instance.
    

Rust methods can also be constructors, utility functions, and other associated functions. `User::new()` is a conventional constructor pattern, not a special language feature.

## 13. Enums and pattern matching

Enums represent one of several possible variants.

```rust
enum PaymentStatus {
    Pending,
    Completed,
    Failed(String),
}

fn print_status(status: PaymentStatus) {
    match status {
        PaymentStatus::Pending => println!("Waiting"),
        PaymentStatus::Completed => println!("Paid"),
        PaymentStatus::Failed(reason) => println!("Failed: {reason}"),
    }
}

fn main() {
    print_status(PaymentStatus::Failed(
        String::from("Insufficient funds"),
    ));
}
```

Unlike a basic Java enum, a Rust enum variant can carry data.

This makes enums useful for representing application states, API responses, events, and error types.

### `Option<T>`: values that may be absent

Rust doesn't use `null` as a universal value for ordinary references. Instead, `Option<T>` represents optional values.

```
fn find_user(id: u32) -> Option<String> {
    if id == 1 {
        Some(String::from("Alex"))
    } else {
        None
    }
}

fn main() {
    match find_user(1) {
        Some(name) => println!("Found: {name}"),
        None => println!("User not found"),
    }
}
```

Useful methods include:

```
let value = Some(10);

println!("{:?}", value.unwrap_or(0));
println!("{:?}", value.map(|n| n * 2));
println!("{:?}", value.is_some());
```

Avoid calling `.unwrap()` on values that might be absent in normal application flow, because it panics when the value is `None`.

## 14. Error handling with `Result<T, E>`

Rust distinguishes success from failure with `Result`.

```
use std::fs;

fn read_file() -> Result<String, std::io::Error> {
    let content = fs::read_to_string("data.txt")?;
    Ok(content)
}

fn main() {
    match read_file() {
        Ok(content) => println!("{content}"),
        Err(error) => eprintln!("Could not read file: {error}"),
    }
}
```

Important concepts:

- `Ok(value)` represents success.
    
- `Err(error)` represents failure.
    
- `?` propagates an error to the calling function.
    
- `Result<T, E>` makes error handling explicit in the type system.
    

The `?` operator can return early from a function whose return type supports the propagated error.

For example, to parse a number:

```
use std::num::ParseIntError;

fn parse_age(input: &str) -> Result<u32, ParseIntError> {
    input.parse::<u32>()
}

fn main() {
    match parse_age("22") {
        Ok(age) => println!("Age: {age}"),
        Err(error) => println!("Invalid age: {error}"),
    }
}
```

Practical rule: use `Option` when a value may be absent; use `Result` when an operation may fail and the failure deserves an explanation.

## 15. Traits — Rust's approach to shared behavior

A trait defines behavior that types can implement. It is similar in purpose to a Java interface.

```
trait Printable {
    fn print(&self);

    fn description(&self) -> &str {
        "Printable object"
    }
}

struct Product {
    name: String,
}

impl Printable for Product {
    fn print(&self) {
        println!("Product: {}", self.name);
    }

    fn description(&self) -> &str {
        &self.name
    }
}

fn main() {
    let product = Product {
        name: String::from("Keyboard"),
    };

    product.print();
    println!("{}", product.description());
}
```

Traits are a foundation for reusable libraries and generic code.

Common standard-library traits include:

- `Debug` — debug formatting with `{:?}`.
    
- `Display` — user-facing formatting with `{}`.
    
- `Clone` — explicit duplication.
    
- `Copy` — inexpensive bitwise copying for eligible types.
    
- `Default` — a default value.
    
- `PartialEq` — equality comparisons.
    
- `Iterator` — iteration behavior.
    
- `From` and `Into` — conversions between types.
    

For example:

```
#[derive(Debug, Clone, PartialEq)]
struct Product {
    id: u32,
    price: f64,
}

fn main() {
    let first = Product { id: 1, price: 99.0 };
    let second = first.clone();

    println!("{first:?}");
    println!("Equal: {}", first == second);
}
```

`derive` automatically implements supported traits when the fields permit it.

## 16. Generics and trait bounds

Generics let you write code that works with multiple types.

```
fn first_element<T>(items: &[T]) -> Option<&T> {
    items.first()
}

fn main() {
    let numbers = [10, 20, 30];
    let names = ["Alex", "Sam"];

    println!("{:?}", first_element(&numbers));
    println!("{:?}", first_element(&names));
}
```

`T` is a generic type parameter. The function can operate on slices of different element types without knowing their concrete type in advance.

### Trait bounds

Sometimes a generic type needs to support a particular operation.

```
use std::fmt::Display;

fn show<T: Display>(value: T) {
    println!("{value}");
}

fn main() {
    show(100);
    show("Hello");
}
```

`T: Display` means `T` must implement the `Display` trait.

For multiple requirements:

```
use std::fmt::{Debug, Display};

fn inspect<T: Debug + Display>(value: T) {
    println!("Display: {value}");
    println!("Debug: {value:?}");
}
```

Advanced Rust uses traits and generics extensively in reusable libraries, data processing, and framework design.

## 17. Closures and iterators

Closures are anonymous functions that can capture variables from their environment.

```
fn main() {
    let multiplier = 3;

    let multiply = |n: i32| n * multiplier;

    println!("{}", multiply(10));
}
```

### Iterator operations

```
fn main() {
    let numbers = vec![1, 2, 3, 4, 5, 6];

    let result: Vec<i32> = numbers
        .iter()
        .filter(|&&n| n % 2 == 0)
        .map(|&n| n * n)
        .collect();

    println!("{result:?}");
}
```

Output:

```
[4, 16, 36]
```

Here:

- `iter()` iterates over borrowed elements.
    
- `filter()` keeps matching elements.
    
- `map()` transforms each element.
    
- `collect()` gathers the results into a collection.
    

More useful iterator methods include `fold`, `find`, `any`, `all`, `enumerate`, `zip`, and `flat_map`.

## 18. Modules and project organization

As projects grow, split code into modules instead of putting everything in `main.rs`.

A simple structure:

```
src/
├── main.rs
├── models.rs
└── services.rs
```

`src/models.rs`:

```
pub struct User {
    pub name: String,
}
```

`src/services.rs`:

```
use crate::models::User;

pub fn greet(user: &User) {
    println!("Hello, {}", user.name);
}
```

`src/main.rs`:

```
mod models;
mod services;

use models::User;

fn main() {
    let user = User {
        name: String::from("Alex"),
    };

    services::greet(&user);
}
```

In Rust, items are private to their module by default. `pub` exposes an item to the appropriate external scope. Module organization and visibility are important when building libraries.

## 19. Cargo dependencies and external crates

Rust packages are commonly called crates. Cargo manages dependencies through `Cargo.toml`.

For example, add `serde` and `serde_json` to a project:

```
cargo add serde --features derive
cargo add serde_json
```

Example:

```
use serde::{Deserialize, Serialize};

#[derive(Debug, Serialize, Deserialize)]
struct User {
    id: u32,
    name: String,
}

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let user = User {
        id: 1,
        name: String::from("Alex"),
    };

    let json = serde_json::to_string(&user)?;
    println!("{json}");

    let decoded: User = serde_json::from_str(&json)?;
    println!("{decoded:?}");

    Ok(())
}
```

This is comparable to using Maven dependencies and a JSON library in Java.

Key Cargo files:

- `Cargo.toml` — package metadata, dependencies, and configuration.
    
- `Cargo.lock` — records resolved dependency versions for reproducible application builds.
    
- `src/lib.rs` — library crate entry point.
    
- `src/main.rs` — executable crate entry point.
    

For applications, commit `Cargo.lock` to version control. Library projects typically handle it differently depending on their build and distribution workflow.

## 20. Lifetimes — advanced borrowing

Lifetimes describe how long references must remain valid. Rust uses them to prevent references from outliving the data they refer to.

Consider a function that returns one of its input references:

```
fn longer<'a>(first: &'a str, second: &'a str) -> &'a str {
    if first.len() > second.len() {
        first
    } else {
        second
    }
}

fn main() {
    let a = "Rust";
    let b = "Programming";

    println!("{}", longer(a, b));
}
```

The lifetime `'a` expresses a relationship: the returned reference cannot be valid longer than the relevant borrowed inputs.

It does not mean that the strings must remain alive forever or that their lifetimes must have identical durations. The compiler checks the actual borrowing relationships.

### Lifetimes in structs

```
struct Book<'a> {
    title: &'a str,
}

fn main() {
    let title = String::from("Learning Rust");

    let book = Book {
        title: &title,
    };

    println!("{}", book.title);
}
```

`Book` borrows its title rather than owning it. The compiler ensures that the borrowed text remains valid while the `Book` uses it.

When should you learn lifetimes? After you understand ownership, references, structs, and functions. Many simple functions do not require you to write explicit lifetime annotations because Rust can infer them.

## 21. Smart pointers

Smart pointers manage ownership and provide additional behavior.

|Type|Purpose|
|---|---|
|`Box<T>`|Heap allocation with single ownership|
|`Rc<T>`|Shared ownership in single-threaded code|
|`Arc<T>`|Shared ownership across threads|
|`RefCell<T>`|Interior mutability checked at runtime|
|`Mutex<T>`|Mutual exclusion for shared access|
|`RwLock<T>`|Multiple readers or one writer|

### `Box<T>`

```
fn main() {
    let value = Box::new(100);
    println!("{value}");
}
```

`Box<T>` is useful for heap-allocated values, recursive data structures, and situations where you need an owned value behind a pointer.

### `Arc<T>` for shared ownership

```
use std::sync::Arc;
use std::thread;

fn main() {
    let message = Arc::new(String::from("Hello"));

    let shared = Arc::clone(&message);

    let handle = thread::spawn(move || {
        println!("{shared}");
    });

    handle.join().unwrap();

    println!("{message}");
}
```

`Arc` allows multiple owners to share a value safely across threads. It does not, by itself, make arbitrary mutation safe; use synchronization primitives when shared mutable state is needed.

## 22. Multithreading and concurrency

Rust supports operating-system threads through the standard library.

```
use std::thread;

fn main() {
    let handle = thread::spawn(|| {
        for i in 1..=5 {
            println!("Worker: {i}");
        }
    });

    println!("Main thread");

    handle.join().unwrap();
}
```

`thread::spawn` creates a worker thread, and `join()` waits for it to finish.

### Sharing mutable state with `Arc<Mutex<T>>`

```
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    let counter = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    for _ in 0..5 {
        let counter = Arc::clone(&counter);

        let handle = thread::spawn(move || {
            let mut value = counter.lock().unwrap();
            *value += 1;
        });

        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }

    println!("{}", *counter.lock().unwrap());
}
```

Output:

```
5
```

`Mutex` ensures that only one thread at a time holds the lock. `Arc` keeps the shared counter alive while threads use it.

Rust's type system also helps prevent data races by restricting which types and references can safely cross thread boundaries.

## 23. Async programming with Tokio

Threads are useful, but servers often need to manage many simultaneous network operations. Asynchronous programming allows tasks to wait for I/O without occupying a separate operating-system thread for each waiting operation.

Create a project and add Tokio:

```
cargo new async_demo
cd async_demo
cargo add tokio --features macros,rt-multi-thread,time
```

Replace `src/main.rs` with:

```
use tokio::time::{sleep, Duration};

async fn fetch_data() -> String {
    sleep(Duration::from_secs(1)).await;
    String::from("Data received")
}

#[tokio::main]
async fn main() {
    let first = fetch_data();
    let second = fetch_data();

    let (a, b) = tokio::join!(first, second);

    println!("{a}");
    println!("{b}");
}
```

Run:

```
cargo run
```

Both tasks can make progress concurrently while waiting. Their one-second sleeps do not require one dedicated thread per task.

Understand the important terms:

- `async fn` defines an asynchronous function.
    
- `.await` suspends the current async task until its operation can progress.
    
- A runtime such as Tokio schedules async tasks.
    
- `tokio::join!` waits for multiple futures together.
    

An `async` function does not automatically run in parallel. Async concurrency and parallel execution are related but distinct concepts.

## 24. File handling and command-line applications

Rust is excellent for Linux command-line tools.

```
use std::fs;
use std::io;

fn main() -> io::Result<()> {
    fs::write("hello.txt", "Hello from Rust")?;

    let contents = fs::read_to_string("hello.txt")?;

    println!("{contents}");

    Ok(())
}
```

This writes a file, reads it back, and propagates I/O errors.

### Read command-line arguments

```
use std::env;

fn main() {
    let args: Vec<String> = env::args().collect();

    for (index, arg) in args.iter().enumerate() {
        println!("{index}: {arg}");
    }
}
```

Run:

```
cargo run -- one two three
```

For advanced CLI projects, learn the `clap` crate for argument parsing, `anyhow` for application-level error handling, `thiserror` for defining custom error types, and `tracing` for structured logging.

## 25. Testing and debugging

Rust includes a built-in test framework.

```
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn adds_two_numbers() {
        assert_eq!(add(2, 3), 5);
    }

    #[test]
    fn adds_negative_numbers() {
        assert_eq!(add(-2, -3), -5);
    }
}
```

Run:

```
cargo test
```

Useful development commands:

```
cargo fmt --check
cargo clippy --all-targets --all-features
cargo test
cargo build --release
```

- `cargo fmt` keeps formatting consistent.
    
- `cargo clippy` catches common mistakes and suggests improvements.
    
- `cargo test` runs unit and integration tests.
    
- `cargo build --release` produces an optimized build.
    

For production projects, add integration tests, error-path tests, and tests for concurrency-sensitive behavior.

## 26. Build a REST API with Rust

Since you are learning backend development, this is a natural next step after the language fundamentals.

A common framework choice is Axum with Tokio.

### Rust backend stack

Client — browser, mobile app, or frontend

Axum — HTTP routes and handlers

Service layer — business logic

SQLx or Diesel — database access

PostgreSQL

### Build a minimal API

Create a project and add dependencies:

```
cargo new rust_api
cd rust_api

cargo add axum
cargo add tokio --features macros,rt-multi-thread
```

Replace `src/main.rs`:

```
use axum::{routing::get, Json, Router};
use serde::Serialize;

#[derive(Serialize)]
struct User {
    id: u32,
    name: String,
}

async fn home() -> &'static str {
    "Rust API is running"
}

async fn get_user() -> Json<User> {
    Json(User {
        id: 1,
        name: String::from("Alex"),
    })
}

#[tokio::main]
async fn main() {
    let app = Router::new()
        .route("/", get(home))
        .route("/api/users/1", get(get_user));

    let listener = tokio::net::TcpListener::bind("127.0.0.1:3000")
        .await
        .unwrap();

    println!("Server running on http://127.0.0.1:3000");

    axum::serve(listener, app).await.unwrap();
}
```

The example also requires Serde's derive feature. Add it before compiling:

```
cargo add serde --features derive
cargo run
```

Test the endpoints:

```
curl http://127.0.0.1:3000/
curl http://127.0.0.1:3000/api/users/1
```

The second endpoint returns JSON similar to:

```
{"id":1,"name":"Alex"}
```

This is a basic demonstration, not a production-ready API. A real backend should add configuration, validation, proper error responses, database integration, authentication and authorization, tests, and observability.

## 27. Advanced Rust concepts

Once you're comfortable with APIs, ownership, traits, and async programming, explore these topics.

A. Macros

Macros generate code at compile time. You've already used `println!`, `vec!`, and `assert_eq!`.

```
macro_rules! say_hello {
    ($name:expr) => {
        println!("Hello, {}!", $name);
    };
}

fn main() {
    say_hello!("Rust");
}
```

Learn declarative macros, macro repetition, and procedural macros.

B. Unsafe Rust

`unsafe` allows certain operations the compiler cannot fully verify, such as dereferencing raw pointers and calling unsafe functions.

```
fn main() {
    let mut value = 10;
    let ptr = &mut value as *mut i32;

    unsafe {
        *ptr += 1;
    }

    println!("{value}");
}
```

Use `unsafe` only when needed and uphold all safety requirements. It does not disable every Rust safety check.

C. Advanced traits and types

Learn associated types, trait objects (`dyn Trait`), static vs dynamic dispatch, blanket implementations, `Send`, `Sync`, and higher-ranked trait bounds.

D. Async internals

Learn `Future`, pinning (`Pin`), `Stream`, task scheduling, cancellation, timeouts, bounded concurrency, and backpressure.

E. FFI and WebAssembly

Learn how Rust interacts with C libraries, how to expose a C-compatible interface, and how to compile Rust for WebAssembly targets.

## 28. Database integration with PostgreSQL

For backend projects, you can use SQLx or Diesel to connect Rust to PostgreSQL.

For an asynchronous API, SQLx is a useful starting point.

```
cargo add sqlx --features runtime-tokio,postgres,derive
```

A typical database workflow looks like this:

1. Read database configuration from environment variables.
    
2. Create a PostgreSQL connection pool.
    
3. Execute parameterized queries.
    
4. Convert database rows into Rust structs.
    
5. Return results through HTTP handlers.
    
6. Handle database errors without exposing internal details to clients.
    

For example, the conceptual query using SQLx is:

```
let rows = sqlx::query_as::<_, User>(
    "SELECT id, name FROM users WHERE id = $1"
)
.bind(user_id)
.fetch_optional(&pool)
.await?;
```

Here, `User` must implement the required row-mapping trait, and `pool` must be a configured SQLx PostgreSQL pool. The example assumes the query and types are compatible with your database schema.

Use migrations to manage schema changes rather than relying on manually created production tables.

## 29. Rust compared with Java and Spring Boot

|Concept|Rust|Java / Spring|
|---|---|---|
|Compiler|`rustc`|`javac`|
|Project manager|Cargo|Maven / Gradle|
|Data structures|`struct`, `enum`|Classes, records, enums|
|Shared behavior|Traits|Interfaces|
|Optional values|`Option<T>`|`Optional<T>` or nullable values|
|Error handling|`Result<T, E>`|Exceptions and result types|
|Memory management|Ownership and borrowing|Garbage collection|
|Concurrency|Threads, channels, async runtimes|Threads, executors, virtual threads|
|Web APIs|Axum, Actix Web|Spring Boot Web|
|Database|SQLx, Diesel|JDBC, Spring Data JPA|
|Dependency injection|Often explicit composition|Spring IoC container|
|Build tool|Cargo|Maven / Gradle|

The biggest mindset shift is that Rust makes ownership and borrowing central to the language. Java usually lets the garbage collector manage object lifetimes, while Rust checks many memory-safety rules during compilation.


