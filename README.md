# rust-notes

Some tips / things of notes for myself while I'm learning rust

## What is Rust

Rust is a systems programming language that pursues the trifecta of safety (guaranteed at compile time), fearless concurrency (easier when things are safe), and blazingly fast speed (due to zero-cost abstractions). Unlike high-level languages that offer safety but lack speed and concurrency, or systems languages like C/C++ that offer speed and concurrency but lack safety, Rust provides all three benefits simultaneously.

## Rust History

Rust was started in 2006 as a personal project by Graydon Hoare, a Mozilla employee. Mozilla began sponsoring Rust in 2009, and version 1.0 was released in 2015 after a 9-year incubation period. Mozilla sponsored Rust because they were tired of C++ and wanted a better language for Firefox development. The Firefox Quantum update in 2017, which rewrote the core in Rust, made Firefox over twice as fast and less buggy, proving Rust's effectiveness in production.

## Learning Rust

Rust has a steep learning curve at the beginning, making fundamentals crucial since without them your code won't compile. Effective learning requires understanding core concepts before writing complex programs, as the compiler enforces strict rules that must be followed for successful compilation.

## Cargo Package Manager

Cargo is the first tool you'll use and keep using for everything in Rust development. It combines the best parts of npm, pip, bundler, and make into a single tool that handles package management, building, testing, and documentation generation. Systems programmers can finally have nice things too.

```bash
cargo new hello
```

The `cargo new` command creates a complete project structure with a `cargo.toml` configuration file and a `src/main.rs` source file containing a hello world program. The `cargo.toml` file is the authoritative source of project information, containing the project name, version, authors, and edition.

```toml
[package]
name = "hello"
version = "0.1.0"
authors = ["Your Name <email@example.com>"]
edition = "2018"
```

## Building and Running

Cargo provides simple commands to build and run your projects. The `cargo run` command builds and runs your project in one step, while `cargo run --release` creates an optimized version for production use.

```bash
cargo run              # Build and run in one step
cargo run --release    # Build optimized version
```

Cargo compiles projects in debug mode by default, which includes debug symbols for faster compilation but slower execution. Release mode removes debug symbols, resulting in slower compilation but significantly faster execution. Cargo also implements smart caching and won't recompile if nothing has changed in your source code.

## Variables

Rust uses the `let` statement to declare variables, with type annotations optional when the compiler can infer the type. Variables are immutable by default, promoting safety, concurrency, and speed by preventing accidental changes and enabling compiler optimizations.

```rust
let bunnies = 2;                    // Immutable variable
let mut bunnies = 32;               // Mutable variable
let bunnies: i32 = 2;               // Explicit type annotation
let (x, y) = (1, 2);                // Destructuring assignment
```

The `mut` keyword makes variables mutable, allowing value changes after initialization. Constants use `const` with required type annotations and screaming snake case naming convention, providing compile-time inlined values that are faster than regular variables.

```rust
let mut missiles = 8;                // Mutable variable
missiles = missiles - 2;             // Can be reassigned

const STARTING_MISSILES: i32 = 8;    // Constant (screaming snake case)
const READY_AMOUNT: i32 = 2;         // Type annotation required
```

Common variable mistakes include forgetting semicolons, omitting the `let` keyword for declarations, and attempting to reassign immutable variables. Constants should be placed at module scope (outside functions) to make them available throughout the module.

## Scope

Variable scope begins where the variable is created and extends to the end of its block, with values immediately dropped when they go out of scope. Variables are accessible from nested blocks but not from outer scopes, and Rust's compiler catches scope violations at compile time.

```rust
let x = 5;
{
    let y = 10;
    println!("{} {}", x, y);        // Works: x accessible from nested block
}
// println!("{}", y);               // Error: y not in scope
```

Variable shadowing allows redefining variables in the same or nested scopes, enabling type changes and mutability modifications within the same scope. Shadowed variables are completely separate from their original namesakes.

## Memory Safety

Rust guarantees memory safety at compile time by requiring all variables to be initialized before use. The compiler analyzes all code paths to ensure variables are guaranteed to be initialized, preventing undefined behavior that occurs in languages like C.

```rust
let enigma;                          // Error: uninitialized variable
// println!("{}", enigma);          // Won't compile

let enigma = 42;                     // Works: properly initialized
println!("{}", enigma);              // Safe to use
```

The compiler cannot reason about conditional initialization at compile time, so variables must be guaranteed to be initialized through all possible code paths. This prevents the undefined behavior that occurs when using uninitialized variables in other systems languages.

## Functions

Functions are defined using the `fn` keyword with snake_case naming convention, and function declarations can appear anywhere in the file regardless of call order. Parameters require explicit type annotations, and return types are specified with an arrow syntax.

```rust
fn add_numbers(x: i32, y: i32) -> i32 {
    x + y                            // Tail expression (no semicolon)
}

fn multiply_numbers(x: i32, y: i32) -> i32 {
    return x * y;                    // Explicit return
}

// Practical examples from exercise
fn area_of(x: i32, y: i32) -> i32 {
    x * y                            // Calculate rectangle area
}

fn volume(width: i32, height: i32, depth: i32) -> i32 {
    width * height * depth           // Calculate volume
}
```

Tail expressions automatically return values when the semicolon is omitted from the last expression in a function block. Function calls require arguments in the correct order since Rust doesn't support named parameters, and macros are distinguished by their exclamation mark suffix.

Common function mistakes include scope issues where variables defined in inner blocks are not accessible outside, and forgetting to use tail expressions instead of explicit return statements. The `cargo clippy` tool can identify non-idiomatic code patterns.

## Module System

Rust's module system organizes code into libraries and binaries, with `lib.rs` serving as the root library file and `main.rs` as the binary entry point. All items in libraries are private by default and must be marked with `pub` to be accessible from other modules.

```rust
// lib.rs
pub fn greet() -> String {
    "Hello, world!".to_string()
}

// main.rs
use hello::greet;                    // Import from library
fn main() {
    println!("{}", greet());         // Use imported function
}
```

The `use` statement brings items into scope, and absolute paths use double colons to access functions. Dependencies are added to `cargo.toml` and can be found on crates.io, Rust's package registry.

```rust
// lib.rs - Organize functions into library
pub fn print_difference(x: f32, y: f32) {
    println!("Difference between {} and {} is {}", x, y, x - y);
}

pub fn print_array(arr: [f32; 2]) {
    println!("Coordinates are {} and {}", arr[0], arr[1]);
}

pub fn ding(value: i32) {
    if value == 13 {
        println!("Ding, you found 13");
    }
}

// main.rs - Use library functions
use ding_machine::{print_difference, print_array, ding};

fn main() {
    let coords = (6.3, 15.0);
    print_difference(coords.0, coords.1);

    let coords_array = [coords.0, coords.1];
    print_array(coords_array);

    let series = [1, 2, 3, 4, 5, 6, 13, 8, 9, 10];
    ding(series[6]);
}
```

Library names are defined in `cargo.toml` and must match the project name. Functions moved to libraries must be marked `pub` to be accessible from the main binary. The `use` statement can import multiple items from the same library.

## Scalar Types

Rust provides four scalar types: integers, floats, booleans, and characters. Integer types include signed (i8, i16, i32, i64, i128, isize) and unsigned (u8, u16, u32, u64, u128, usize) variants, with i32 being the default for unannotated literals.

```rust
let decimal = 98_222;                // Decimal with underscores
let hex = 0xff;                      // Hexadecimal
let octal = 0o77;                    // Octal
let binary = 0b1111_0000;            // Binary
let byte = b'A';                     // Byte literal
let float = 2.0;                     // f64 by default
let boolean = true;                  // bool type
let character = 'z';                 // Unicode scalar value
```

Floating point types include f32 and f64, with f64 being the default. Boolean values are true and false, and characters represent Unicode scalar values using single quotes. Integer literals can include underscores for readability, and type suffixes can be added to specify exact types.

## Compound Types

Compound types group multiple values into single types, with tuples storing values of different types and arrays storing values of the same type. Tuples use parentheses and dot notation for access, while arrays use square brackets and are limited to 32 elements.

```rust
let tuple: (i32, f64, u8) = (500, 6.4, 1);
let first = tuple.0;                 // Access by index
let (x, y, z) = tuple;               // Destructuring

let array: [i32; 5] = [1, 2, 3, 4, 5];
let first = array[0];                // Index access
let repeated = [3; 5];               // [3, 3, 3, 3, 3]

// Practical examples from exercise
let coords: (f32, f32) = (6.3, 15.0);
let x = coords.0;                    // Tuple indexing
let y = coords.1;

let coords_array: [f32; 2] = [coords.0, coords.1];  // Convert tuple to array
let series = [1, 2, 3, 4, 5, 6, 13, 8, 9, 10];
let value = series[6];               // Array indexing (gets 13)

// Nested data structures
let mess = (1, (2, [true, false]), 3);
let boolean = mess.1.1[0];           // Access nested: tuple -> tuple -> array
```

Tuples have a maximum **arity** of 12 elements and use dot syntax to emphasize that members can be different types. Arrays are fixed-size, stack-allocated, and typically replaced with vectors for dynamic collections. Both tuples and arrays can be converted between each other when the types are compatible.

## Collections

Collections are data structures that hold multiple values. Rust's standard library provides several collection types, each optimized for different use cases.

### Vectors (`Vec<T>`)

Vectors are the most commonly used collection, similar to arrays or lists in other languages. They store values of the same type in contiguous memory.

```rust
// Creating vectors
let mut numbers: Vec<i32> = Vec::new();
numbers.push(1);
numbers.push(2);
numbers.push(3);

// Using the vec! macro for initialization
let numbers = vec![1, 2, 3, 4, 5];

// Accessing elements
let first = numbers[0];               // Indexing (panics if out of bounds)
let first = numbers.get(0);           // Returns Option<&i32>

// Stack operations
numbers.push(6);                      // Add to end
let last = numbers.pop();             // Remove and return last element

// Common methods
numbers.insert(1, 10);                // Insert at index
numbers.remove(1);                    // Remove at index
numbers.sort();                       // Sort in place
```

### HashMaps (`HashMap<K, V>`)

HashMaps store key-value pairs with constant-time lookup, similar to dictionaries in other languages.

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert("Alice", 100);
scores.insert("Bob", 85);

// Accessing values
let alice_score = scores.get("Alice"); // Returns Option<&i32>
let alice_score = scores["Alice"];     // Panics if key doesn't exist

// Updating values
scores.insert("Alice", 95);            // Overwrites existing value
scores.entry("Charlie").or_insert(0);  // Insert if not exists

// Iterating
for (name, score) in &scores {
    println!("{}: {}", name, score);
}
```

### Other Collections

- **`VecDeque<T>`** - Double-ended queue (ring buffer)
- **`LinkedList<T>`** - Linked list (slow for most operations)
- **`HashSet<T>`** - Set with hash-based lookup
- **`BinaryHeap<T>`** - Priority queue (max-heap)
- **`BTreeMap<K, V>`** - Sorted map using B-tree
- **`BTreeSet<T>`** - Sorted set using B-tree

## Strings

Rust provides two main string types: `&str` (borrowed string slice) and `String` (owned string), both guaranteed to be valid UTF-8. String slices are immutable references to string data, while owned strings can be modified and contain additional capacity for growth.

```rust
let slice: &str = "Hello";           // String slice (immutable)
let owned = String::from("World");   // Owned string (mutable)
let converted = slice.to_string();   // Convert slice to owned

for byte in slice.bytes() {          // Iterate over bytes
    println!("{}", byte);
}

for char in slice.chars() {          // Iterate over Unicode scalars
    println!("{}", char);
}
```

Strings cannot be indexed by character position due to UTF-8's variable byte encoding. Instead, use iterator methods like `bytes()`, `chars()`, or third-party crates for grapheme-level operations. String literals are always borrowed string slices.

### UTF-8 vs UTF-4 Encoding

Rust's `char` type is always four bytes, making arrays of characters effectively UTF-32 strings, while Rust strings use UTF-8 encoding internally. This creates a mismatch where characters represent Unicode scalar values but strings store UTF-8 bytes, requiring different iteration methods for each encoding.

```rust
let thai_word = "สวัสดี";              // Thai word "sawatdi"
// thai_word[3]                      // Error: cannot index strings

for byte in thai_word.bytes() {      // 18 bytes total
    println!("{}", byte);
}

for scalar in thai_word.chars() {    // 6 Unicode scalars
    println!("{}", scalar);
}
```

### Why you can't index Strings

Because English isn't the only language, Rust prevents you from indexing what you want. The Thai word "`สวัสดี`" (sawatdi) perfectly illustrates Rust's string indexing challenges through three representation levels. At the byte level, it's 18 UTF-8 bytes where each Thai character requires 3 bytes. At the scalar level, it's 6 Unicode scalar values. At the grapheme level, it's 6 visual characters that users actually see.

```rust
let thai = "สวัสดี";
// Byte level: 18 bytes (each Thai char = 3 bytes)
// Scalar level: 6 Unicode scalars
// Grapheme level: 6 visual characters
```

Consider attempting to access the character at index 3 in "`สวัสดี`". This string is stored as a vector of 18 bytes. If you indexed by bytes, you would not obtain the expected result. Unicode scalars in UTF-8 can be represented by 1, 2, 3, or 4 bytes, requiring traversal of the bytes to determine where one scalar ends and the next begins. In this case, every three bytes represents a Unicode scalar.

```rust
// Cannot index directly - would need O(n) traversal
// thai[3]  // Error: cannot index strings
```

Even if scalar indexing were available, the result would still not match expectations. Diacritics are Unicode scalars that combine with other Unicode scalars to produce different graphemes, which are typically what users intend to access. This demonstrates how graphemes decompose into variable amounts of scalars, which in turn decompose into variable amounts of bytes.

Instead, use iterators for different levels:

```rust
// Access bytes (UTF-8 encoding)
for (i, byte) in thai.bytes().enumerate() {
    println!("Byte {}: {}", i, byte);
}

// Access Unicode scalars
for (i, scalar) in thai.chars().enumerate() {
    println!("Scalar {}: {}", i, scalar);
}
```

As part of Rust's emphasis on performance, indexing operations on standard library collections are guaranteed to be constant time operations. This guarantee cannot be provided for strings because the bytes, which are indexable, do not correspond to what users expect when indexing into a string. The graphemes, which users typically want, can only be retrieved after examining a sequence of bytes sequentially.

For grapheme-level access, use external crates:

```rust
// For grapheme-level access, use external crates:
use unicode_segmentation::UnicodeSegmentation;
for (i, grapheme) in thai.graphemes(true).enumerate() {
    println!("Grapheme {}: {}", i, grapheme);
}
```

When working with strings, you have several options: use the `bytes()` method to access the vector of UTF-8 bytes, use the `chars()` method to retrieve an iterator for Unicode scalars, or use a package like `unicode-segmentation` which provides functions that handle graphemes of various types.

Diacritics and combining characters further complicate indexing. A single visual character (grapheme) can be composed of multiple Unicode scalars, and each scalar can be 1-4 bytes in UTF-8. This three-level hierarchy (bytes → scalars → graphemes) makes constant-time indexing impossible, which is why Rust requires explicit iteration methods for string access.

## Control Flow

Rust's control flow structures are expressions that return values, with if statements requiring explicit boolean conditions and mandatory braces. If expressions can be used for conditional assignment, with all branches returning the same type and no semicolons on the returned values.

```rust
let number = 7;
let message = if number < 5 {
    "small"
} else if number < 10 {
    "medium"
} else {
    "large"
};
```

If expressions are expressions, not statements, meaning they return values. Four key points:

1. No semicolons after branch values (tail expressions)
2. Return cannot be used for this purpose
3. All blocks must return the same type
4. A semicolon is required at the end of the if expression when used in a statement context

Braces are mandatory, preventing the common C programming error where additional lines end up outside the intended branch body.

```rust
// Rust's if expression replaces C's ternary operator
let result = if condition { "yes" } else { "no" };

// Nested if expressions remain readable
let nested = if x > 0 {
    if y > 0 { "both positive" } else { "x positive" }
} else {
    if y > 0 { "y positive" } else { "both negative" }
};
```

The unconditional `loop` allows the compiler to perform optimizations when it knows a loop is unconditional. Use `break` to terminate loops, and labeled breaks to exit nested loops.

```rust
loop {
    if condition { break; }          // Unconditional loop
}

'outer: loop {                       // Labeled loop (tick identifier)
    loop {
        break 'outer;                // Break outer loop
    }
}

'outer: loop {
    loop {
        continue 'outer;             // Continue outer loop
    }
}
```

While loops have the same behaviors as unconditional loops but terminate when their condition evaluates to false. Rust refuses to coerce expressions to booleans, requiring explicit boolean values. While loops are essentially syntactic sugar for putting a negated break condition at the top of an unconditional loop.

```rust
while condition {
    // Loop body
}

// Do-while equivalent
loop {
    // Loop body
    if !condition { break; }
}
```

For loops iterate over any iterable value, with compound and collection types providing multiple ways to get iterators. The `iter` method is common for getting iterators, which determine item order and selection.

```rust
for i in 0..50 {                     // Range (exclusive)
    println!("{}", i);
}

for i in 0..=50 {                    // Range (inclusive)
    println!("{}", i);
}

// Practical examples from exercise
let mut sum = 0;
for i in 7..=23 {                    // Inclusive range: 7 to 23
    sum += i;                        // Add each number to sum
}
println!("The sum is {}", sum);      // Output: 255

// Command line argument handling
use std::env;

fn main() {
    let args: Vec<String> = env::args().collect();

    for arg in &args[1..] {          // Skip program name
        if arg == "sum" {
            sum_function();
        } else if arg == "double" {
            double_function();
        } else {
            count_function(arg);
        }
    }
}

// While loop example
let mut x = 1;
let mut count = 0;
while x < 500 {
    count += 1;                      // Increment counter
    x *= 2;                          // Double x
}
println!("You can double x {} times before it is larger than 500", count);
```

Ranges use `..` for exclusive bounds and `..=` for inclusive bounds, with the start being inclusive and end being exclusive by default. For loops can destructure items and bind parts to variables, similar to let statements but with variables local to the loop body. Command line arguments are accessed through `std::env::args()` and can be processed with control flow structures.

## Enums

Enums in Rust are algebraic data types that can hold different variants, each potentially containing different data. They're more powerful than C-style enums and can represent complex data structures.

### Basic Enum Syntax

```rust
enum DispenserItem {
    Empty,
    Ammo(u8),
    Things(String, i32),
    Place { x: i32, y: i32 },
}

// Using enum variants
let item = DispenserItem::Ammo(5);
let place = DispenserItem::Place { x: 10, y: 20 };
```

### Pattern Matching

Enums require pattern matching to access their data safely.

```rust
// if let - for single variant matching
if let DispenserItem::Ammo(count) = item {
    println!("Got {} ammo", count);
}

// match - for exhaustive pattern matching
match item {
    DispenserItem::Empty => println!("Empty"),
    DispenserItem::Ammo(count) => println!("Got {} ammo", count),
    DispenserItem::Things(name, count) => println!("Got {} {}", count, name),
    DispenserItem::Place { x, y } => println!("At position ({}, {})", x, y),
}
```

### Option Enum

`Option<T>` represents a value that might be present or absent, replacing null/nil from other languages.

```rust
// Option is in the prelude (no need to import)
let some_number: Option<i32> = Some(5);
let no_number: Option<i32> = None;

// Pattern matching with Option
match some_number {
    Some(value) => println!("Got: {}", value),
    None => println!("No value"),
}

// Helper methods
if some_number.is_some() {
    println!("Has value: {}", some_number.unwrap());
}

// Option implements IntoIterator
for value in some_number {
    println!("Value: {}", value);
}
```

### Result Enum

`Result<T, E>` represents success or failure, commonly used for error handling.

```rust
use std::fs::File;

// File operations return Result
let file_result = File::open("example.txt");

// Must handle Result (compiler warning if ignored)
match file_result {
    Ok(file) => println!("File opened successfully"),
    Err(error) => println!("Error: {}", error),
}

// Common methods
let file = file_result.unwrap();      // Panics on error
let file = file_result.expect("Failed to open file");  // Panics with message

// Helper methods
if file_result.is_ok() {
    let file = file_result.unwrap();
}
```

**Key Features:** Enums are algebraic data types that are more powerful than C-style enums, supporting pattern matching that is exhaustive and safe. They provide generic support to hold any type and can implement methods like structs. Enums eliminate null values by using `Option` to represent presence or absence, and `Result` for error handling with success or failure outcomes.

```rust
// Practical example combining enums and collections
enum Shot {
    Bullseye,
    Hit(f64),    // Distance from center
    Miss,
}

impl Shot {
    fn points(&self) -> i32 {
        match self {
            Shot::Bullseye => 5,
            Shot::Hit(distance) if *distance < 3.0 => 2,
            Shot::Hit(_) => 1,
            Shot::Miss => 0,
        }
    }
}

// Using with collections
let mut shots = Vec::new();
shots.push(Shot::Hit(1.2));          // 2 points
shots.push(Shot::Hit(4.3));          // 1 point
shots.push(Shot::Bullseye);          // 5 points

let total: i32 = shots.iter().map(|shot| shot.points()).sum();
```

## Ownership

Ownership is Rust's core memory management system with three fundamental rules: each value has exactly one owner, ownership can be transferred through moves, and values are automatically dropped when their owner goes out of scope. This system prevents memory leaks and data races without garbage collection.

```rust
let s1 = String::from("hello");
let s2 = s1;                         // s1 is moved to s2
// println!("{}", s1);              // Error: s1 no longer valid

let s3 = s2.clone();                 // Deep copy (expensive)
let s4 = s2;                         // s2 is moved to s4
```

The three ownership rules are: each value has exactly one owner, there can only be one owner at a time, and when the owner goes out of scope, the value is dropped immediately. When a value is moved, the stack data (pointer, length, capacity) is copied to the new owner, and the original variable becomes uninitialized.

```rust
// Move vs Clone example
let s1 = String::from("hello");
let s2 = s1;                         // Move: s1 becomes invalid
// println!("{}", s1);              // Error: borrow of moved value

let s3 = s2.clone();                 // Clone: creates deep copy
println!("{} {}", s2, s3);           // Both valid

// Function ownership transfer
fn do_stuff(s: String) -> String {   // Takes ownership
    println!("{}", s);
    s                                // Returns ownership
}

let mut s1 = String::from("hello");
s1 = do_stuff(s1);                   // s1 moved in and back
```

Rust reserves the term "copy" for stack-only data, while "clone" is used when heap data is involved. When a value is dropped, the destructor runs, heap memory is freed, and stack memory is popped, ensuring no memory leaks or dangling pointers.

```rust
// Practical ownership patterns from exercise
fn inspect(s: &String) {             // Borrows string (immutable reference)
    if s.ends_with("s") {
        println!("{} is plural", s);
    } else {
        println!("{} is singular", s);
    }
}

fn change(s: &mut String) {          // Borrows string (mutable reference)
    if !s.ends_with("s") {
        s.push_str("s");             // Modifies original string
    }
}

fn eat(s: String) -> bool {          // Takes ownership (consumes string)
    s.starts_with("b") && s.contains("a")  // String consumed, not usable after
}

// Usage examples
let mut word = String::from("apple");
inspect(&word);                      // word still usable
change(&mut word);                   // word modified
println!("{}", word);                // "apples"

let word2 = String::from("boat");
if eat(word2) {                      // word2 consumed
    println!("might be bananas");
}
// println!("{}", word2);            // Error: word2 moved
```

References and Borrowing

References allow you to refer to a value without taking ownership, using the ampersand operator to create pointers that are automatically managed by the compiler. References are immutable by default and must follow the borrowing rules: **Variables can have either one mutable reference or any number of immutable references at a time.**

```rust
fn calculate_length(s: &String) -> usize {
    s.len()                          // No ownership transfer
}

let mut s1 = String::from("hello");
let r1 = &s1;                        // Immutable reference
let r2 = &s1;                        // Another immutable reference
let r3 = &mut s1;                    // Mutable reference (only one allowed)

*r3 = String::from("world");         // Dereference to modify
```

The dot operator automatically dereferences references when accessing methods or fields. References are implemented as pointers but are guaranteed to be valid through Rust's **lifetime** system, preventing dangling pointers and null references.

```rust
// Function borrowing example
fn do_stuff(s: &String) {            // Takes reference, not ownership
    println!("{}", s);               // Can use s normally
}

let s1 = String::from("hello");
do_stuff(&s1);                       // Pass reference to s1
println!("{}", s1);                  // s1 still valid after function call

// Mutable references
fn do_stuff_mut(s: &mut String) {
    s.push_str(" world");            // Auto-dereferencing with dot operator
}

let mut s1 = String::from("hello");
do_stuff_mut(&mut s1);               // Pass mutable reference
println!("{}", s1);                  // "hello world"

// Manual dereferencing
let mut x = 5;
let r = &mut x;
*r = 10;                             // Manual dereference with *
println!("{}", x);                   // 10
```

References default to immutable even if the referenced value is mutable. The borrowing rule (one mutable reference OR any number of immutable references) applies across all threads and prevents data races. The compiler enforces these rules with informative error messages, ensuring memory safety without runtime overhead.

## Structs

Structs are Rust's primary way to create custom data types, combining data fields with associated methods and functions. Struct definitions use the `struct` keyword with capital camel case naming, and all fields must be specified when creating instances unless default constructors are provided.

```rust
struct RedFox {
    energy: u32,
    position: u32,
}

// Instantiation requires all fields (verbose but explicit)
let fox = RedFox {
    energy: 100,
    position: 0,
};

impl RedFox {
    // Associated function (like constructor) - no self parameter
    fn new() -> Self {
        RedFox {
            energy: 100,
            position: 0,
        }
    }

    // Method - takes some form of self as first parameter
    fn run(&mut self) {
        self.energy -= 1;
        self.position += 1;
    }
}

// Using associated function as constructor
let mut fox = RedFox::new();
fox.run();                           // Method call with dot syntax
```

**Key Features:** Structs use capital camel case naming and require all fields to be specified during instantiation unless default constructors are provided. The `impl` block separates data definition from implementation, with associated functions (like `new()`) not taking `self` and methods always taking some form of `self` as their first parameter. The `Self` type alias can be used instead of the struct name, and derive macros like `#[derive(Debug)]` provide common trait implementations. Rust uses composition over inheritance, with traits serving as the primary mechanism for shared behavior.

```rust
// Practical example from exercise
#[derive(Debug)]
struct Grapes {
    amount_left: i32,
}

trait Bite {
    fn bite(&mut self);
}

impl Bite for Grapes {
    fn bite(&mut self) {
        self.amount_left -= 1;
    }
}

let mut grapes = Grapes { amount_left: 100 };
grapes.bite();                       // Now 99 grapes left
println!("{:?}", grapes);            // Grapes { amount_left: 99 }
```

The `impl` block defines all behavior for a struct, separating data definition from implementation. This pattern of defining data with `struct` and behavior with `impl` is the standard way to create custom types in Rust.

## Traits

Traits define shared behavior that can be implemented by multiple types, serving as Rust's alternative to inheritance. They represent the "composition over inheritance" approach, defining required methods that implementing types must provide.

```rust
trait Noisy {
    fn get_noise(&self) -> &str;
}

impl Noisy for RedFox {
    fn get_noise(&self) -> &str {
        "meow"
    }
}

// Generic function that works with any type implementing Noisy
fn print_noise<T: Noisy>(item: T) {
    println!("{}", item.get_noise());
}

// Can implement traits for built-in types
impl Noisy for u8 {
    fn get_noise(&self) -> &str {
        "beep"
    }
}

// Trait inheritance
trait Movement {
    fn move_forward(&self);
}

trait Run: Movement {
    fn run(&self) {
        self.move_forward();
        println!("I'm running");
    }
}

// Default implementations
trait Animal {
    fn make_sound(&self) -> &str {
        "unknown sound"  // Default behavior
    }
}

impl Animal for Robot {
    // Uses default implementation - no need to override
}
```

**Key Features:** Traits represent composition over inheritance, allowing you to mix and match behaviors across different types. Generic functions can work with any type implementing a specific trait, and the orphan rule permits implementing traits if either the trait or type is defined in your crate. Traits can inherit from other traits and provide default implementations for common behavior. Unlike structs, traits can only define methods, not data fields. The `Copy` trait is a special case that allows types to be copied instead of moved.

**Special Traits:** The `Copy` trait is implemented for stack-only types like integers, floats, and booleans, allowing them to be copied instead of moved. Heap types cannot implement `Copy` and must be moved when transferred between variables.

### Common Standard Library Traits

**Derivable Traits:** Many traits can be automatically implemented using the `#[derive()]` attribute when all fields implement the trait.

```rust
#[derive(Debug, Clone, PartialEq, Eq)]
struct Puzzle {
    num_pieces: u32,
    name: String,
}

// Debug formatting
println!("{:?}", puzzle);        // One-line debug
println!("{:#?}", puzzle);       // Pretty debug with indentation

// Clone implementation
let puzzle2 = puzzle.clone();    // Deep copy

// Equality comparison
if puzzle == puzzle2 {
    println!("Puzzles are equal");
}
```

**Copy Trait:** A marker trait for types that can be copied instead of moved. Only stack-only types can implement `Copy`.

```rust
#[derive(Debug, Clone, Copy)]
enum PuzzleType {
    Jigsaw,
    Crossword,
    Sudoku,
}

// Copy types are copied automatically
let puzzle1 = PuzzleType::Jigsaw;
let puzzle2 = puzzle1;  // puzzle1 is copied, not moved
println!("{:?}", puzzle1);  // Still available
```

### Manual Trait Implementation

**Three Steps to Implement Traits:**

1. **Import the trait** with `use` statement (if not in prelude)
2. **Generate boilerplate** using IDE or documentation
3. **Implement required methods**

**Default Trait:** Provides a default value for types.

```rust
impl Default for Puzzle {
    fn default() -> Self {
        Puzzle {
            num_pieces: 1000,
            name: String::from("Forest Lake"),
        }
    }
}

// Usage with struct update syntax
let custom_puzzle = Puzzle {
    num_pieces: 500,
    ..Default::default()  // Fill remaining fields with defaults
};
```

**PartialEq and Eq Traits:** Enable equality comparisons.

```rust
impl PartialEq for Puzzle {
    fn eq(&self, other: &Self) -> bool {
        self.num_pieces == other.num_pieces &&
        self.name == other.name
    }
}

impl Eq for Puzzle {}  // Marker trait - no methods to implement

// Usage
if puzzle1 == puzzle2 {
    println!("Puzzles are equal");
}
```

**From and Into Traits:** Enable type conversions. Implement `From` to automatically get `Into`.

```rust
// Implement From to get Into automatically
impl From<Puzzle> for String {
    fn from(puzzle: Puzzle) -> Self {
        puzzle.name
    }
}

// More efficient: implement From<&Puzzle> for String
impl From<&Puzzle> for String {
    fn from(puzzle: &Puzzle) -> Self {
        puzzle.name.clone()  // Clone only the string, not entire struct
    }
}

// Usage
let puzzle = Puzzle::default();
let name: String = puzzle.into();  // Uses Into trait
let name = String::from(&puzzle);  // Uses From trait

// Generic function using Into trait
fn show<T: Into<String>>(item: T) {
    let string = item.into();
    println!("{}", string);
}

show(&puzzle);  // Works with reference, doesn't consume puzzle
```

### Practical Exercise Examples

The exercise demonstrates comprehensive trait implementation patterns:

```rust
// 1. Deriving Debug trait
#[derive(Debug)]
enum Cake {
    Chocolate,
    Spice,
    MapleBacon,
}

fn admire_cake(cake: Cake) {
    println!("What a nice {:?} cake!", cake);
}

// 2. Deriving Copy and Clone traits
#[derive(Debug, Clone, Copy)]
enum Cake {
    Chocolate,
    Spice,
    MapleBacon,
}

// Now cake is copied instead of moved
let cake = Cake::Spice;
admire_cake(cake);  // cake is copied
match cake {
    Cake::Spice => println!("Great, let's spice it up!"),
    _ => println!("Other cake"),
}

// 3. Manual Default implementation
#[derive(Debug)]
struct Party {
    restaurant: bool,
    attendees: u8,
    cake: Cake,
}

impl Default for Party {
    fn default() -> Self {
        Party {
            restaurant: true,
            attendees: 8,
            cake: Cake::Chocolate,
        }
    }
}

// 4. Struct update syntax with Default
let my_party = Party {
    cake: Cake::MapleBacon,
    ..Default::default()  // Fill remaining fields with defaults
};

// 5. Manual PartialEq implementation
impl PartialEq for Party {
    fn eq(&self, other: &Self) -> bool {
        self.cake == other.cake  // Only compare cake field
    }
}

// Cakes need PartialEq for comparison
#[derive(Debug, Clone, Copy, PartialEq)]
enum Cake {
    Chocolate,
    Spice,
    MapleBacon,
}

// Usage
let party1 = Party { cake: Cake::MapleBacon, ..Default::default() };
let party2 = Party { cake: Cake::MapleBacon, restaurant: false, attendees: 235, ..Default::default() };
if party1 == party2 {
    println!("Your party is just like mine!");
}
```

## Closures

Closures are anonymous functions that can capture values from their enclosing scope. They're commonly used with iterators, threading, and functional programming patterns.

### Basic Closure Syntax

```rust
// Simple closure with parameters
let add = |x, y| x + y;
let result = add(1, 2);               // 3

// Closure with no parameters
let get_five = || 5;
let five = get_five();                // 5

// Closure capturing from scope
let message = String::from("Hello");
let print_message = || println!("{}", message);
print_message();                      // Prints "Hello"
```

### Move Closures

Closures can take ownership of captured values using the `move` keyword.

```rust
let data = vec![1, 2, 3, 4, 5];

// Borrowing closure (captures reference)
let sum_ref = || {
    let total: i32 = data.iter().sum();
    println!("Sum: {}", total);
};

// Moving closure (takes ownership)
let sum_move = move || {
    let total: i32 = data.iter().sum();
    println!("Sum: {}", total);
};

// Can be sent to threads or returned from functions
```

### Functional Programming with Closures

Closures are essential for functional programming patterns with iterators.

```rust
let numbers = vec![1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

let result: i32 = numbers
    .iter()
    .map(|x| x * 3)                   // Multiply each by 3
    .filter(|x| x > &10)              // Keep values > 10
    .fold(0, |acc, x| acc + x);       // Sum remaining values

println!("Result: {}", result);       // 45 (15 + 18 + 21 + 24 + 27 + 30)
```

**Key Features:** Closures are anonymous functions that eliminate the need to define separate functions, with type inference automatically determining parameter and return types. They can capture variables from their enclosing scope and support move semantics to take ownership of captured values. Closures work seamlessly with iterator methods like `map`, `filter`, and `fold`, and move closures can be safely sent between threads.

### Advanced Closure Concepts

**Type Inference:** The compiler automatically determines argument and return types based on how the closure is used, eliminating the need for explicit type annotations in most cases.

**Borrowing vs Moving:** Closures automatically borrow references to values in their enclosing scope by default. Use the `move` keyword to take ownership of captured values, which is essential for sending closures to threads or returning them from functions.

```rust
// Automatic borrowing (default behavior)
let s = String::from("strawberry");
let f = || println!("{}", s);  // Borrows reference to s
f();  // Prints "strawberry"
println!("{}", s);  // Still accessible

// Move semantics
let s = String::from("strawberry");
let f = move || println!("{}", s);  // Takes ownership of s
f();  // Prints "strawberry"
// println!("{}", s);  // Error: s moved into closure

// Clone for shared access
let s = String::from("strawberry");
let s_clone = s.clone();
let f = move || println!("{}", s_clone);  // Uses clone
f();  // Prints "strawberry"
println!("{}", s);  // Original still accessible
```

**Function Types:** When passing closures to or from functions, you may need to specify closure types using traits from `std::ops`: `Fn`, `FnMut`, and `FnOnce`. These are advanced traits that determine how the closure captures and uses its environment.

## Iterators

Iterators are the foundation of Rust's functional programming capabilities, providing lazy evaluation and efficient data processing. They work seamlessly with closures to create powerful data transformation pipelines.

### Basic Iterator Usage

```rust
let numbers = vec![1, 2, 3, 4, 5];

// Basic iteration
for num in &numbers {
    println!("{}", num);
}

// Functional style with iterators
numbers.iter()
    .map(|x| x * 2)
    .filter(|x| x > &5)
    .for_each(|x| println!("{}", x));
```

### Iterator Types

There are three main types of iterators depending on how they access collection data:

```rust
let mut numbers = vec![1, 2, 3, 4, 5];

// into_iter() - Takes ownership (consumes collection)
for num in numbers.into_iter() {
    println!("{}", num);
}
// numbers is no longer available

// iter() - Borrows immutably (preserves collection)
let numbers = vec![1, 2, 3, 4, 5];
for num in numbers.iter() {
    println!("{}", num);  // num is &i32
}
// numbers is still available

// iter_mut() - Borrows mutably (allows modification)
let mut numbers = vec![1, 2, 3, 4, 5];
for num in numbers.iter_mut() {
    *num *= 2;  // num is &mut i32
}
// numbers is now [2, 4, 6, 8, 10]
```

### Syntactic Sugar

For loops automatically use the appropriate iterator method:

```rust
let numbers = vec![1, 2, 3, 4, 5];

// These are equivalent:
for num in &numbers { /* ... */ }        // iter()
for num in &mut numbers { /* ... */ }    // iter_mut()
for num in numbers { /* ... */ }         // into_iter()
```

### Iterator Adapters vs Consumers

Iterator adapters transform iterators without consuming them, while consumers actually process the data.

```rust
let numbers = vec![1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

// Iterator adapters (lazy - no processing yet)
let transformed = numbers
    .into_iter()
    .map(|x| x * 3)           // Transform each item
    .filter(|x| x % 2 == 0);  // Keep only even numbers

// Iterator consumer (triggers processing)
let result: i32 = transformed.sum();  // Actually processes the data
println!("{}", result);  // 90 (sum of 6, 12, 18, 24, 30)
```

### The Turbofish (`::<>`)

Used to specify generic types when the compiler can't infer them:

```rust
let numbers = vec![1, 2, 3, 4, 5];

// Without turbofish (compiler error)
// let sum = numbers.iter().sum();

// With turbofish (explicit type)
let sum: i32 = numbers.iter().sum();
let sum = numbers.iter().sum::<i32>();

// With collect (needs type annotation)
let doubled: Vec<i32> = numbers.iter().map(|x| x * 2).collect();
let doubled = numbers.iter().map(|x| x * 2).collect::<Vec<_>>();
```

### Drain Method

Removes items from a collection without consuming the collection itself:

```rust
let mut numbers = vec![1, 2, 3, 4, 5];

// Drain all items
for num in numbers.drain(..) {
    println!("Drained: {}", num);
}
// numbers is now empty but still usable

use std::collections::HashMap;
let mut map = HashMap::new();
map.insert("a", 1);
map.insert("b", 2);

// Drain all key-value pairs
for (key, value) in map.drain() {
    println!("Drained: {} = {}", key, value);
}
// map is now empty but still usable
```

**Key Features:** Iterators are lazy and only process items when consumed. They provide a functional programming approach to data processing with methods like `map`, `filter`, `fold`, and `sum`. Iterators can be chained together to create complex data transformations, and they're often more efficient than traditional loops due to compiler optimizations.

### Practical Exercise Examples

The exercise demonstrates comprehensive closure and iterator usage:

```rust
// 1. Basic closure creation
let square = |x| x * x;
println!("5 squared is {}", square(5));  // 25

// 2. Iterator with tuple destructuring
let tuples = vec![(0, 1), (2, 3), (4, 5)];
let result: Vec<_> = tuples
    .into_iter()
    .map(|(x, y)| (x + 1, y))  // Destructure tuple in parameter
    .collect();
// Result: [(1, 1), (3, 3), (5, 5)]

// 3. Mutable iteration with syntactic sugar
let mut numbers = vec![1, 2, 3, 4];
for x in &mut numbers {
    *x *= 3;  // Equivalent to: x *= 3;
}
// numbers is now [3, 6, 9, 12]

// 4. Complex iterator chain
let words = vec!["hello", "world", "rust", "programming"];
let filtered_words: Vec<_> = words
    .into_iter()                    // Convert to iterator
    .filter(|s| !s.contains('h'))   // Remove words with 'h'
    .map(|w| w.to_uppercase())      // Convert to uppercase
    .collect();                     // Collect into vector
// Result: ["WORLD", "RUST", "PROGRAMMING"]
```

## Error Handling

Rust's error handling system is built around the `Result<T, E>` type and custom error types. Creating well-designed error types is crucial for library development and professional Rust code.

### Creating Custom Error Types

**Five Key Rules for Error Types:**

1. **Errors should be enums** - Never use structs for errors
2. **Group related errors** into as few enums as makes sense
3. **Only return your own errors** from public APIs (don't expose dependency errors)
4. **Make enums non-exhaustive** with `#[non_exhaustive]` attribute
5. **Implement Debug, Display, and Error traits** (in that order)

### Manual Error Implementation

```rust
use std::fmt::{Display, Formatter};
use std::error::Error;

#[non_exhaustive]
#[derive(Debug)]
pub enum PuzzleError {
    InvalidSize(u32),
    MissingImage(String),
}

impl Display for PuzzleError {
    fn fmt(&self, f: &mut Formatter<'_>) -> std::fmt::Result {
        use PuzzleError::*;
        match self {
            InvalidSize(size) => write!(f, "Invalid puzzle size: {}", size),
            MissingImage(path) => write!(f, "Missing image file: {}", path),
        }
    }
}

impl Error for PuzzleError {}
```

### Using thiserror Crate

The `thiserror` crate simplifies error creation significantly:

```toml
[dependencies]
thiserror = "1.0"
```

```rust
use thiserror::Error;

#[non_exhaustive]
#[derive(Debug, Error)]
pub enum PuzzleError {
    #[error("Invalid puzzle size: {0}")]
    InvalidSize(u32),

    #[error("Missing image file: {path}")]
    MissingImage { path: String },

    #[error("IO error: {0}")]
    Io(#[from] std::io::Error),
}
```

**Key Features of thiserror:**

- **Automatic trait implementation** for `Display` and `Error`
- **Error message templates** with field interpolation
- **Error conversion** with `#[from]` attribute
- **Much less boilerplate** than manual implementation

### Error Conversion Patterns

```rust
// Converting from standard library errors
impl From<std::io::Error> for PuzzleError {
    fn from(err: std::io::Error) -> Self {
        PuzzleError::Io(err)
    }
}

// Converting between your own error types
impl From<PuzzleError> for AppError {
    fn from(err: PuzzleError) -> Self {
        AppError::Puzzle(err)
    }
}

// Usage with ? operator
fn load_puzzle(path: &str) -> Result<Puzzle, PuzzleError> {
    let file = std::fs::File::open(path)?;  // Converts io::Error to PuzzleError
    // ... rest of implementation
}
```

### Best Practices

**Error Grouping:** Group related errors together rather than creating many small error types.

**Non-exhaustive Enums:** Always use `#[non_exhaustive]` to allow adding new error variants without breaking user code.

**Error Messages:** Provide clear, actionable error messages that help users understand and fix the problem.

**Dependency Errors:** Convert external library errors into your own error types to maintain API stability.

**Standard Library Errors:** Consider whether to wrap or pass through standard library errors based on your API design.

### Error Handling Patterns

**Non-recoverable Errors:** Use `panic!` for errors that make it impossible for the program to continue.

```rust
// Only use panic for truly unrecoverable situations
if critical_condition {
    panic!("Critical system failure: {}", error_message);
}

// Library authors should avoid panic, expect, and unwrap
// Applications can use them as a last resort
```

**Recoverable Errors:** Handle errors gracefully or return them to the caller.

```rust
// Using if let for error-focused handling
if let Err(e) = load_config() {
    eprintln!("Warning: Could not load config: {}", e);
    // Continue with default configuration
}

// Using match for both success and error cases
let score = match load_saved_score() {
    Ok(score) => score,
    Err(_) => 0,  // Default value on error
};

// Using Result helper methods
let score = load_saved_score().unwrap_or(0);
let config = load_config().unwrap_or_else(|_| Config::default());
```

### The `?` Operator

The `?` operator simplifies error propagation by automatically unwrapping `Ok` values and returning `Err` values.

```rust
// Without ? operator (verbose)
fn load_puzzle(path: &str) -> Result<Puzzle, std::io::Error> {
    let file = match std::fs::File::open(path) {
        Ok(file) => file,
        Err(e) => return Err(e),
    };
    // ... rest of implementation
}

// With ? operator (concise)
fn load_puzzle(path: &str) -> Result<Puzzle, std::io::Error> {
    let file = std::fs::File::open(path)?;
    // ... rest of implementation
}

// Chaining multiple operations
fn process_data() -> Result<Data, AppError> {
    let file = std::fs::File::open("data.txt")?;
    let data = parse_data(file)?;
    let processed = transform_data(data)?;
    Ok(processed)
}
```

### Application-Level Error Handling with anyhow

For applications dealing with multiple error types, use the `anyhow` crate for simplified error handling.

```toml
[dependencies]
anyhow = "1.0"
```

```rust
use anyhow::{Context, Result};

fn get_puzzle(filename: &str) -> Result<Puzzle> {
    let file = std::fs::File::open(filename)
        .with_context(|| format!("Couldn't open puzzle file: {}", filename))?;

    let puzzle = Puzzle::from_file(file)
        .context("Couldn't convert data into puzzle")?;

    Ok(puzzle)
}

fn main() -> Result<()> {
    let puzzle = get_puzzle("puzzle.dat")
        .context("Couldn't get the first puzzle")?;

    println!("Loaded puzzle: {}", puzzle.name);
    Ok(())
}
```

**Key Features of anyhow:**

- **Generic error type** that works with any error implementing `std::error::Error`
- **Context methods** for adding helpful error information
- **Automatic error chaining** with detailed error reports
- **Simplified main function** that can return `Result<()>`

### Error Context and Debugging

```rust
// Adding context to errors
let file = std::fs::File::open(path)
    .with_context(|| format!("Failed to open {}", path))?;

// Using closures for expensive context
let data = process_file(file)
    .with_context(|| {
        let expensive_info = generate_debug_info();
        format!("Processing failed: {}", expensive_info)
    })?;

// Error output with anyhow
// Error: Couldn't get the first puzzle
//
// Caused by:
//   0: Couldn't open puzzle file: puzzle.dat
//   1: No such file or directory (os error 2)
```

### Best Practices

**Library Development:**

- Convert external errors to your own error types
- Use `?` operator for your own error types
- Never use `panic!`, `expect()`, or `unwrap()` in libraries

**Application Development:**

- Use `anyhow` or similar crates for simplified error handling
- Add context to errors for better debugging
- Consider using `panic!` only for truly unrecoverable situations

**Error Propagation:**

- Use `?` operator for automatic error propagation
- Add context with `.context()` or `.with_context()`
- Return errors up the call stack when you can't handle them locally

### Practical Exercise Examples

The exercise demonstrates comprehensive error handling patterns:

```rust
// 1. Creating custom error types with thiserror
use thiserror::Error;

#[non_exhaustive]
#[derive(Debug, Error)]
pub enum DolphinError {
    #[error("The dolphin is hungry")]
    Hungry,

    #[error("The dolphin is too young")]
    TooYoung,

    #[error("The dolphin's name is too long and annoying to say")]
    LongName,
}

// 2. Using anyhow for application-level error handling
use anyhow::Result;

fn playtime(dolphin: &Dolphin) -> Result<Vec<String>> {
    let mut responses = Vec::new();

    // Two-line approach with intermediate variable
    let response = dolphin.say_your_name()?;
    responses.push(response);

    // One-line approach with direct unwrapping
    responses.push(dolphin.flip()?);
    responses.push(dolphin.shake_hands()?);

    Ok(responses)
}

// 3. Error handling in main function
fn main() -> Result<()> {
    let dolphins = vec![
        Dolphin::new("Devin", 15, false),
        Dolphin::new("VeryLongNameThatIsTooLong", 5, true),
        Dolphin::new("Baby", 2, false),
    ];

    for dolphin in &dolphins {
        match playtime(dolphin) {
            Ok(tricks) => println!("{} performed: {:?}", dolphin.name, tricks),
            Err(e) => println!("{} couldn't perform: {}", dolphin.name, e),
        }
    }

    Ok(())
}
```

## Threads

Rust provides portable threading that works across different platforms (macOS, Linux, Windows). Threads allow concurrent execution and can run simultaneously on multiple CPU cores.

### Thread Fundamentals

Every process starts with a single **main thread**. You can spawn additional threads to perform parallel processing, which is especially beneficial on multi-core systems.

**Why Use Threads:**

Threads provide **parallel processing** by utilizing multiple CPU cores simultaneously, **shared memory** since threads in the same process can share memory, **cost efficiency** as threads are cheaper than processes, and **performance** benefits by potentially running twice as fast on dual-core systems (minus communication overhead).

### Basic Threading

```rust
use std::thread;

fn main() {
    // Spawn a new thread
    let handle = thread::spawn(|| {
        println!("Hello from thread!");
        42  // Return value from thread
    });

    // Do work in main thread
    println!("Hello from main thread!");

    // Wait for spawned thread to complete
    let result = handle.join().unwrap();
    println!("Thread returned: {}", result);
}
```

### Thread Handles and Joining

**Key Concepts:**

The **`thread::spawn()`** function creates a new thread and returns a `JoinHandle`, which allows you to wait for thread completion and retrieve results. The **`join()`** method blocks until the thread completes and returns a `Result<T, E>`, and **thread cleanup** requires always joining threads to ensure clean termination.

```rust
use std::thread;

fn main() {
    let handle = thread::spawn(|| {
        // Simulate some work
        thread::sleep(std::time::Duration::from_secs(2));
        "Thread completed successfully"
    });

    // Main thread continues while child thread runs
    println!("Main thread doing other work...");

    // Wait for child thread to complete
    match handle.join() {
        Ok(result) => println!("Thread result: {}", result),
        Err(e) => println!("Thread panicked: {:?}", e),
    }
}
```

### Thread Return Values

Threads can return any type that implements the `Send` trait:

```rust
use std::thread;

fn main() {
    let handle = thread::spawn(|| {
        // Complex computation
        let mut sum = 0;
        for i in 1..=1000 {
            sum += i;
        }
        sum
    });

    let result = handle.join().unwrap();
    println!("Sum: {}", result);
}
```

### Practical Example: Parallel Cooking

```rust
use std::thread;
use std::time::Duration;

mod dad {
    use super::*;

    pub fn cook_spaghetti() -> bool {
        println!("Dad: Starting to cook spaghetti...");
        thread::sleep(Duration::from_secs(4));
        println!("Dad: Spaghetti is ready!");
        true
    }
}

mod mom {
    use super::*;

    pub fn cook_sauce_and_set_table() {
        println!("Mom: Starting to cook sauce...");
        thread::sleep(Duration::from_secs(2));
        println!("Mom: Sauce is ready!");

        println!("Mom: Setting the table...");
        thread::sleep(Duration::from_secs(1));
        println!("Mom: Table is set!");
    }
}

fn main() {
    // Spawn thread for dad's task
    let spaghetti_handle = thread::spawn(dad::cook_spaghetti);

    // Mom's task runs on main thread
    mom::cook_sauce_and_set_table();

    // Wait for dad to finish
    let spaghetti_success = spaghetti_handle.join().unwrap();

    if spaghetti_success {
        println!("Dinner is ready!");
    } else {
        println!("Something went wrong with the spaghetti!");
    }
}
```

### Threads with Move Closures

Threads often use move closures to take ownership of data.

```rust
use std::thread;

fn main() {
    let data = vec![1, 2, 3, 4, 5];

    let handle = thread::spawn(move || {
        // data is moved into this thread
        let sum: i32 = data.iter().sum();
        println!("Sum in thread: {}", sum);
        sum
    });

    // data is no longer available in main thread
    let result = handle.join().unwrap();
    println!("Thread result: {}", result);
}
```

### Thread Safety Considerations

```rust
use std::thread;
use std::sync::{Arc, Mutex};

fn main() {
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

    println!("Final count: {}", *counter.lock().unwrap());
}
```

### Thread Performance Considerations

**Thread Overhead:**

Threads have **memory allocation** costs as each thread allocates ~2MB for its stack, **context switching** overhead as the CPU must perform expensive context switches, and **scheduling overhead** that increases when more threads share a core.

**When to Use Threads:**

Threads are ideal for **CPU-intensive tasks** that benefit from parallel processing on multiple cores, **I/O operations** while waiting for disk/network (though async/await is more efficient), and **independent computations** where tasks can run simultaneously.

**Best Practices:**

Thread management requires **always joining threads** to ensure clean termination, **limiting thread count** by not creating more threads than CPU cores for CPU-bound tasks, **using async/await** for I/O-bound concurrent operations, and **considering thread pools** for managing many short-lived tasks.

**Key Features:** Rust threads are portable across Mac, Linux, Windows, and other platforms, with join handles that can wait for thread completion and retrieve return values. The `join()` method returns `Result<T, E>` for proper panic handling, and threads can use move closures to take ownership of data. Threads are heavyweight, allocating several MB of stack memory each, with expensive context switching when many threads share CPU cores. They are best suited for CPU-bound computational tasks, while I/O-bound operations should consider async/await for better efficiency.

```rust
// Practical example combining closures and threads
use std::thread;
use std::time::Duration;

fn expensive_sum(numbers: Vec<i32>) -> i32 {
    thread::sleep(Duration::from_millis(500));  // Simulate work
    numbers
        .iter()
        .filter(|&x| x % 2 != 0)                // Remove even numbers
        .map(|&x| x * x)                        // Square remaining numbers
        .sum()
}

fn main() {
    let numbers = vec![1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

    // Spawn thread with move closure
    let handle = thread::spawn(move || {
        expensive_sum(numbers)
    });

    // Main thread continues working
    for letter in ['A', 'B', 'C', 'D', 'E', 'F'] {
        println!("{}", letter);
        thread::sleep(Duration::from_millis(200));
    }

    // Wait for thread completion and get result
    let result = handle.join().unwrap();
    println!("Thread result: {}", result);
}
```

## Channels

Channels provide a way for threads to communicate by sending messages. While the standard library has a channel implementation, the **crossbeam** crate provides a more efficient and feature-rich alternative.

### Channel Fundamentals

A channel is a one-way queue that threads use to send values to other threads. Values sent through channels must implement the `Send` trait, which the compiler automatically implements for most types.

**Channel Types:**

- **Bounded channels** have a fixed capacity and block when full, providing backpressure
- **Unbounded channels** grow indefinitely until memory is exhausted

### Crossbeam Channels

```rust
use crossbeam_channel::{unbounded, bounded, Sender, Receiver};
use std::thread;

fn main() {
    // Create an unbounded channel
    let (tx, rx) = unbounded::<String>();

    // Spawn a thread to send messages
    let sender = thread::spawn(move || {
        tx.send("Hello from thread!".to_string()).unwrap();
        tx.send("Another message".to_string()).unwrap();
    });

    // Receive messages in main thread
    while let Ok(msg) = rx.recv() {
        println!("Received: {}", msg);
    }

    sender.join().unwrap();
}
```

### Bounded vs Unbounded Channels

**Bounded Channels:**

```rust
// Channel with capacity of 5
let (tx, rx) = bounded::<i32>(5);

// Sender will block when channel is full
tx.send(1).unwrap();
tx.send(2).unwrap();
// ... blocks when full
```

**Unbounded Channels:**

```rust
// Channel that can hold unlimited messages
let (tx, rx) = unbounded::<i32>();

// Sender never blocks
for i in 0..1000 {
    tx.send(i).unwrap(); // Never blocks
}
```

### Multiple Senders and Receivers

Channels support multiple senders and receivers:

```rust
use crossbeam_channel::{unbounded, Sender, Receiver};
use std::thread;

fn main() {
    let (tx, rx) = unbounded::<String>();

    // Multiple senders
    let tx1 = tx.clone();
    let tx2 = tx.clone();

    // Multiple receivers
    let rx1 = rx.clone();
    let rx2 = rx.clone();

    // Spawn sender threads
    let sender1 = thread::spawn(move || {
        tx1.send("From sender 1".to_string()).unwrap();
    });

    let sender2 = thread::spawn(move || {
        tx2.send("From sender 2".to_string()).unwrap();
    });

    // Spawn receiver threads
    let receiver1 = thread::spawn(move || {
        while let Ok(msg) = rx1.recv() {
            println!("Receiver 1 got: {}", msg);
        }
    });

    let receiver2 = thread::spawn(move || {
        while let Ok(msg) = rx2.recv() {
            println!("Receiver 2 got: {}", msg);
        }
    });

    // Drop original sender to close channel
    drop(tx);

    sender1.join().unwrap();
    sender2.join().unwrap();
    receiver1.join().unwrap();
    receiver2.join().unwrap();
}
```

### Practical Example: Cafeteria Workers

```rust
use crossbeam_channel::{unbounded, Sender, Receiver};
use std::thread;
use std::time::Duration;

#[derive(Debug)]
enum Lunch {
    Soup,
    Salad,
    Sandwich,
    Hotdog,
}

fn cafeteria_worker(
    name: &str,
    orders_rx: Receiver<&str>,
    lunches_tx: Sender<Lunch>,
) {
    for order in orders_rx {
        println!("{} received order: {}", name, order);

        // Convert order to lunch
        let lunch = match order {
            x if x.contains("soup") => Lunch::Soup,
            x if x.contains("salad") => Lunch::Salad,
            x if x.contains("sandwich") => Lunch::Sandwich,
            _ => Lunch::Hotdog, // Default
        };

        // Simulate preparation time
        thread::sleep(Duration::from_millis(100 * order.len() as u64));

        println!("{} sending: {:?}", name, lunch);
        if lunches_tx.send(lunch).is_err() {
            break; // Channel closed
        }
    }
}

fn main() {
    // Create channels
    let (orders_tx, orders_rx) = unbounded::<&str>();
    let (lunches_tx, lunches_rx) = unbounded::<Lunch>();

    // Clone for multiple workers
    let orders_rx1 = orders_rx.clone();
    let lunches_tx1 = lunches_tx.clone();

    // Spawn worker threads
    let worker1 = thread::spawn(move || {
        cafeteria_worker("Alice", orders_rx, lunches_tx);
    });

    let worker2 = thread::spawn(move || {
        cafeteria_worker("Bob", orders_rx1, lunches_tx1);
    });

    // Send orders
    let orders = vec!["caesar salad", "tomato soup", "turkey sandwich"];
    for order in orders {
        println!("Sending order: {}", order);
        orders_tx.send(order).unwrap();
    }

    // Close orders channel
    drop(orders_tx);

    // Receive lunches
    for lunch in lunches_rx {
        println!("Received lunch: {:?}", lunch);
    }

    worker1.join().unwrap();
    worker2.join().unwrap();
}
```

### Channel Patterns

**Iterating Over Messages:**

```rust
// Receiver implements IntoIterator
for message in rx {
    println!("Received: {}", message);
}
```

**Non-blocking Receives:**

```rust
use crossbeam_channel::TryRecvError;

match rx.try_recv() {
    Ok(msg) => println!("Got message: {}", msg),
    Err(TryRecvError::Empty) => println!("No messages"),
    Err(TryRecvError::Disconnected) => println!("Channel closed"),
}
```

**Selecting from Multiple Channels:**

```rust
use crossbeam_channel::{select, unbounded};

let (tx1, rx1) = unbounded::<i32>();
let (tx2, rx2) = unbounded::<String>();

select! {
    recv(rx1) -> msg => println!("Got number: {:?}", msg),
    recv(rx2) -> msg => println!("Got string: {:?}", msg),
}
```

### Best Practices

**Channel Design:**

- **Use bounded channels** for backpressure and memory safety
- **Avoid circular channel patterns** to prevent deadlocks
- **Design as directed acyclic graphs** for predictable flow
- **Close channels explicitly** to signal completion

**Performance Considerations:**

- **Crossbeam channels** are faster than standard library channels
- **Bounded channels** provide automatic backpressure
- **Multiple receivers** distribute work automatically
- **Channel cloning** is cheap and efficient

**Error Handling:**

- **Check send/recv results** for channel closure
- **Handle disconnection gracefully** in worker threads
- **Use try_recv()** for non-blocking operations
- **Implement proper cleanup** when channels close

### Practical Exercise Examples

The exercise demonstrates comprehensive thread and channel patterns:

```rust
use crossbeam_channel::{unbounded, Sender, Receiver};
use std::thread;
use std::time::Duration;

// Helper function for sleeping
fn sleep_ms(ms: f32) {
    thread::sleep(Duration::from_millis(ms as u64));
}

// Expensive computation to run in thread
fn expensive_sum(numbers: Vec<i32>) -> i32 {
    sleep_ms(500.0); // Simulate work
    numbers.iter().sum()
}

fn main() {
    let my_vector = vec![1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

    // 1. Spawn thread and get join handle
    let handle = thread::spawn(move || {
        expensive_sum(my_vector)
    });

    // Main thread continues working
    for letter in ['A', 'B', 'C', 'D', 'E', 'F'] {
        println!("Processing letter: {}", letter);
        sleep_ms(200.0);
    }

    // 2. Retrieve value from thread
    let result = handle.join().unwrap();
    let sum = result.unwrap();
    println!("Child thread's expensive sum is: {}", sum);

    // 3. Create channels for communication
    let (tx, rx) = unbounded::<&str>();
    let tx_clone = tx.clone();

    // 4. Spawn threads with channels
    let handle_a = thread::spawn(move || {
        sleep_ms(0.0); // No delay
        tx.send("Thread A1").unwrap();
        sleep_ms(200.0);
        tx.send("Thread A2").unwrap();
    });

    sleep_ms(100.0); // Main thread delay

    let handle_b = thread::spawn(move || {
        sleep_ms(0.0); // No delay
        tx_clone.send("Thread B1").unwrap();
        sleep_ms(200.0);
        tx_clone.send("Thread B2").unwrap();
    });

    // 5. Receive messages using iterator
    for message in rx {
        println!("Received: {}", message);
    }

    // 6. Join threads cleanly
    handle_a.join().unwrap();
    handle_b.join().unwrap();
}
```

**Execution Flow:**

1. **Main thread** processes letters with delays
2. **Child thread** performs expensive computation concurrently
3. **Thread A** sends messages immediately and after 200ms delay
4. **Thread B** starts 100ms later, sends messages with delays
5. **Main thread** receives all messages using channel iterator
6. **All threads** are joined cleanly before program exit

**Output Order Variations:**

- **Default timing:** Thread A1, Thread B1, Thread A2, Thread B2
- **With Thread A delay:** Thread B1, Thread B2, Thread A1, Thread A2
- **Concurrent execution** allows for different interleaving patterns

## Testing

Rust has first-class support for testing built into the language and Cargo. Testing is essential for ensuring code quality and preventing regressions.

### Unit Testing

**Test Organization:** Unit tests are typically placed in a `tests` module at the bottom of the same file as the code being tested.

```rust
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

#[cfg(test)]
mod tests {
    use super::*;  // Import everything from parent module

    #[test]
    fn test_add() {
        assert_eq!(add(2, 3), 5);
    }

    #[test]
    fn test_add_negative() {
        assert_eq!(add(-1, 1), 0);
    }

    #[test]
    #[should_panic]
    fn test_panic() {
        panic!("This test is expected to panic");
    }
}
```

**Test Attributes:**

- `#[test]` - Marks a function as a test
- `#[should_panic]` - Test passes if it panics
- `#[cfg(test)]` - Only compile test module when running tests

**Assert Macros:**

```rust
#[test]
fn test_assertions() {
    // Equality assertions
    assert_eq!(2 + 2, 4);
    assert_ne!(2 + 2, 5);

    // Boolean assertions
    assert!(true);
    assert!(2 > 1, "2 should be greater than 1");

    // Custom error messages
    assert_eq!(add(2, 3), 5, "Addition should work correctly");
}
```

### Test Functions Returning Result

Tests can return `Result` to use the `?` operator for error handling:

```rust
#[test]
fn test_parsing() -> Result<(), std::num::ParseIntError> {
    let number = "42".parse::<i32>()?;
    assert_eq!(number, 42);
    Ok(())
}

// With anyhow for generic error handling
#[test]
fn test_file_operations() -> anyhow::Result<()> {
    let content = std::fs::read_to_string("test.txt")?;
    assert!(!content.is_empty());
    Ok(())
}
```

### Documentation Tests

Rust automatically tests code examples in documentation comments:

````rust
/// Adds two numbers together.
///
/// # Examples
///
/// ```
/// # use my_crate::add;  // Hidden from docs but needed for test
/// let result = add(2, 3);
/// assert_eq!(result, 5);
/// ```
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}
````

**Key Features:**

- **Automatic testing** of documentation examples
- **Hidden code** with `#` prefix (not shown in docs but runs in tests)
- **Syntax highlighting** in generated documentation
- **Integration testing** of public API usage

### Running Tests

```bash
# Run all tests
cargo test

# Run specific test
cargo test test_add

# Run tests with output (even for passing tests)
cargo test -- --nocapture

# Run tests in parallel (default) or sequentially
cargo test -- --test-threads=1
```

### Test Output Structure

Cargo organizes test output by crate (library vs binary):

```
running 2 tests
test tests::test_add ... ok
test tests::test_add_negative ... ok

test result: ok. 2 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

running 1 test
test src/main.rs - main (line 1) ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

running 1 test
test src/lib.rs - add (line 1) ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

### Best Practices

**Test Organization:**

- Place tests in `#[cfg(test)]` modules
- Use `use super::*` to import parent module items
- Group related tests in the same module

**Test Naming:**

- Use descriptive test names that explain the scenario
- Follow the pattern `test_<function_name>_<scenario>`

**Test Coverage:**

- Test both success and failure cases
- Test edge cases and boundary conditions
- Use `#[should_panic]` for expected panics

**Documentation Tests:**

- Include practical examples in public API documentation
- Use `#` to hide setup code from documentation
- Ensure examples are complete and runnable

### Integration Testing

Integration tests test how multiple components work together. They're placed in a separate `tests` directory at the project root.

**Directory Structure:**

```
my_project/
├── src/
│   ├── lib.rs
│   └── main.rs
├── tests/
│   ├── integration_test.rs
│   └── another_test.rs
└── Cargo.toml
```

**Integration Test Example:**

```rust
// tests/integration_test.rs
use my_crate::{add, multiply, Calculator};

#[test]
fn test_calculator_integration() {
    let calc = Calculator::new();

    // Test multiple functions working together
    let result = calc.process_expression("2 + 3 * 4");
    assert_eq!(result, 14);
}

#[test]
fn test_multiple_functions() {
    let sum = add(2, 3);
    let product = multiply(sum, 4);
    assert_eq!(product, 20);
}
```

**Key Differences from Unit Tests:**

- **Separate directory** (`tests/` at project root)
- **Import library as external crate** (`use my_crate::...`)
- **Test component interactions** rather than individual functions
- **Appear in separate section** in test output

**Testing Binaries:**

```rust
// tests/binary_test.rs
use std::process::Command;

#[test]
fn test_binary_output() {
    let output = Command::new("cargo")
        .arg("run")
        .arg("--")
        .arg("test-input")
        .output()
        .expect("Failed to execute binary");

    assert!(output.status.success());
    assert_eq!(String::from_utf8_lossy(&output.stdout), "expected output\n");
}
```

**Best Practices:**

- **Keep binaries minimal** - put logic in library, test the library
- **Test public API** - integration tests should only test public interfaces
- **Test realistic scenarios** - simulate how users will actually use your code
- **Use descriptive file names** - each file should test a specific aspect

### Benchmarking

Rust's built-in benchmarking is only available in nightly builds, so most developers use the **Criterion** crate for performance measurement.

**Setup in `Cargo.toml`:**

```toml
[dev-dependencies]
criterion = { version = "0.5", features = ["html_reports"] }

[[bench]]
name = "my_benchmark"
harness = false
```

**Directory Structure:**

```
my_project/
├── src/
│   └── lib.rs
├── benches/
│   └── my_benchmark.rs
└── Cargo.toml
```

**Basic Benchmark Example:**

```rust
// benches/my_benchmark.rs
use criterion::{black_box, criterion_group, criterion_main, Criterion};
use my_crate::my_function;

fn benchmark_my_function(c: &mut Criterion) {
    c.bench_function("my_function", |b| {
        b.iter(|| my_function(black_box(42)))
    });
}

criterion_group!(benches, benchmark_my_function);
criterion_main!(benches);
```

**Key Components:**

- **`Criterion`** - Main benchmarking framework
- **`black_box()`** - Prevents compiler optimizations from eliminating function calls
- **`b.iter()`** - Measures the time for each iteration
- **`criterion_group!`** - Groups multiple benchmarks together
- **`criterion_main!`** - Generates the main function

**Running Benchmarks:**

```bash
# Run all benchmarks
cargo bench

# Run specific benchmark
cargo bench --bench my_benchmark
```

**Benchmark Output:**

```
running 1 test
test my_function ... bench:      2,123 ns/iter (+/- 234)

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

**HTML Reports:**
When enabled, Criterion generates detailed HTML reports in `target/criterion/report/index.html` with:

- Statistical analysis
- Performance graphs
- Comparison between runs
- Outlier detection

**Best Practices:**

- **Measure in production-like environments** - Same hardware, OS, and Rust version
- **Minimize background processes** - Close other applications during benchmarking
- **Avoid CI benchmarking** - Public CI servers are too noisy for reliable results
- **Start with idiomatic code** - Optimize only after identifying bottlenecks
- **Use dedicated hardware** - For CI benchmarking, use dedicated runners

**Performance Optimization Workflow:**

1. Write idiomatic, readable code
2. Identify performance bottlenecks
3. Write benchmarks to measure current performance
4. Implement optimizations
5. Measure the impact with benchmarks
6. Repeat if necessary

### Practical Exercise Examples

The exercise demonstrates comprehensive testing patterns:

```rust
// 1. Unit tests with #[cfg(test)] module
pub fn sploosh(x: i32, y: i32, z: i32) -> i32 {
    match (x, y, z) {
        (x, _, _) if x < 0 => 99,
        (1, 2, 3) => 4,
        (5, 6, 7) => 3,
        (x, y, z) => x + y - z,
    }
}

pub fn splish(a: i32, b: i32) -> i32 {
    -a + 3 * b
}

#[cfg(test)]
mod tests {
    use super::*;  // Import everything from parent module

    #[test]
    fn test_the_conditions() {
        // Test specific return values
        assert_eq!(sploosh(1, 2, 3), 4);
        assert_ne!(sploosh(5, 6, 7), 4);
        assert_eq!(sploosh(-1, 99, 1), 99);
    }

    #[test]
    fn test_splish_conditions() {
        // Test boolean conditions
        assert!(splish(100, 10) < 0);  // Negative
        assert!(splish(40, 20) > 0);   // Positive
        assert!(splish(9, 3) == 0);    // Zero
    }
}

// 2. Integration tests in tests/more_tests.rs
use testing::{sploosh, splish};

#[test]
fn integration_test() {
    assert_eq!(sploosh(1, 2, 3), 4);
    assert!(splish(100, 10) < 0);
}
```

## Logging

Rust's logging system is built around the `log` crate, which provides a common interface for logging across libraries and applications. The standard library doesn't include logging functionality to maintain stability.

### Log Levels

There are five log levels in descending order of priority:

```rust
use log::{error, warn, info, debug, trace};

// Error - highest priority, always shown
error!("Critical error: {}", error_message);

// Warning - important but not critical
warn!("Warning: {}", warning_message);

// Info - general information
info!("Application started successfully");

// Debug - detailed debugging information
debug!("Processing item: {:?}", item);

// Trace - very detailed debugging
trace!("Entering function: {}", function_name);
```

### Library Logging

Libraries should use only the `log` crate for simple logging:

```rust
// In library Cargo.toml
[dependencies]
log = "0.4"

// In library code
use log::{info, error, debug};

pub fn process_data(data: &str) -> Result<String, Error> {
    debug!("Processing data: {}", data);

    if data.is_empty() {
        error!("Empty data provided");
        return Err(Error::EmptyData);
    }

    info!("Data processed successfully");
    Ok(data.to_uppercase())
}
```

### Application Logging

Applications need both the `log` crate and a logging implementation:

```rust
// In application Cargo.toml
[dependencies]
log = "0.4"
env_logger = "0.10"  // Simple logger implementation

// In application code
use log::info;

fn main() {
    // Initialize the logger
    env_logger::init();

    info!("Application starting...");

    match process_data("hello") {
        Ok(result) => info!("Result: {}", result),
        Err(e) => error!("Error: {}", e),
    }
}
```

### Environment-Based Configuration

The `env_logger` crate uses environment variables for configuration:

```bash
# Set log level for all modules
export RUST_LOG=info

# Set different levels for different modules
export RUST_LOG=my_crate=debug,other_crate=warn

# Set level for specific module
export RUST_LOG=my_crate::module=trace

# Run with specific log level
RUST_LOG=debug cargo run
```

### Log Output Format

Typical log output includes timestamp, level, target (module), and message:

```
[2023-12-01T10:30:45Z INFO  my_crate] Application starting...
[2023-12-01T10:30:45Z DEBUG my_crate::processor] Processing item: Item { id: 1 }
[2023-12-01T10:30:45Z ERROR my_crate] Failed to process item: Invalid data
```

### Custom Targets

You can specify custom targets for log messages:

```rust
use log::{info, warn};

// Custom target for specific component
info!(target: "database", "Connected to database");
warn!(target: "cache", "Cache miss for key: {}", key);

// Default target is the current module
info!("This uses the current module as target");
```

### Advanced Logging

For more advanced features like structured logging, distributed tracing, and async context, consider the `tracing` framework:

```rust
use tracing::{info, error, instrument};

#[instrument]
async fn process_request(request: Request) -> Result<Response, Error> {
    info!("Processing request: {:?}", request);

    // ... processing logic

    info!("Request processed successfully");
    Ok(response)
}
```

### Best Practices

**Library Development:**

- Use only the `log` crate
- Choose appropriate log levels
- Include relevant context in messages
- Use structured data when helpful

**Application Development:**

- Initialize a logger implementation
- Configure log levels via environment variables
- Use appropriate log levels for different environments
- Consider structured logging for complex applications

**Log Level Guidelines:**

- **Error:** System errors that need immediate attention
- **Warn:** Issues that don't break functionality but should be investigated
- **Info:** General application flow and important events
- **Debug:** Detailed information useful for debugging
- **Trace:** Very detailed information for deep debugging

### Practical Exercise Examples

The logging exercise demonstrates comprehensive logging patterns:

```rust
// 1. Library logging with all log levels
use log::{error, warn, info, debug, trace};

#[derive(Debug)]
pub struct Frog {
    energy: i32,
}

impl Frog {
    pub fn new() -> Self {
        let frog = Frog { energy: 1 };
        debug!("A new frog has been created");
        frog
    }

    pub fn hop(&mut self) {
        if self.energy > 0 {
            self.energy -= 1;
            info!("A frog hopped. Energy left: {}", self.energy);
        } else {
            warn!("The frog will go to sleep since he ran out of energy");
        }
    }

    pub fn sleep(&mut self) {
        if self.energy == 0 {
            error!("The frog is already asleep");
        }
    }
}

impl Default for Frog {
    fn default() -> Self {
        let frog = Frog { energy: 5 };
        trace!("A default frog value was generated: {:?}", frog);
        frog
    }
}

// 2. Application with logger initialization
use log::info;

fn main() {
    // Initialize the logger
    env_logger::init();

    info!("Starting frog simulation...");

    let mut frog = Frog::new();
    frog.hop();
    frog.hop();
    frog.sleep();

    let default_frog = Frog::default();
    info!("Created default frog with energy: {}", default_frog.energy);
}
```

**Key Patterns Demonstrated:**

The exercise demonstrates **log level usage** with appropriate levels for different types of information, **debug formatting** using `{:?}` for structured data in logs, **context inclusion** by including relevant data like energy levels, **logger initialization** by setting up `env_logger` in applications, and **environment configuration** using `RUST_LOG` to control output.

**Running with Different Log Levels:**

```bash
# Default (error only)
cargo run

# Info and above
RUST_LOG=info cargo run

# All levels including debug and trace
RUST_LOG=trace cargo run
```

## Idiomatic Code

Idiomatic Rust means writing code like a native Rust developer who has used the language daily for years. The Rust community has distilled idiomatic practices into two essential tools that automate most style and quality concerns.

### Rust Format (`rustfmt`)

Rust Format automatically formats your code according to the de facto Rust style guide, eliminating the need to memorize formatting rules.

To do this, run `cargo fmt`.

```rust
// Before formatting (poorly formatted)
struct Person{name:String,age:u32}
enum Status{Active,Inactive,}

// After `cargo fmt`
struct Person {
    name: String,
    age: u32,
}

enum Status {
    Active,
    Inactive,
}
```

Rust Format handles whitespace, punctuation, and indentation automatically. It adds trailing commas to struct fields and enum variants, collapses multiple blank lines, but won't add extra blank lines or check compilation.

Configure your editor to run `cargo fmt` on save for automatic formatting.

### Clippy

Clippy is a powerful linter that checks for over 450 specific types of problems across four main categories.

```rust
// Style: Needless return (not idiomatic)
fn get_value() -> i32 {
    return 42;
}

// Corrected: Tail expression (idiomatic)
fn get_value() -> i32 {
    42
}

// Correctness: Useless code
let x = 5;
x; // Clippy warns: this does nothing

// Complexity: Too many arguments
#[allow(clippy::too_many_arguments)]
fn complex_function(a: i32, b: i32, c: i32, d: i32, e: i32, f: i32, g: i32, h: i32) {
    // Function implementation
}

// Performance: Inefficient iterator usage
let count = vec![1, 2, 3, 4, 5].iter().cloned().collect::<Vec<_>>().len();
// Clippy suggests: vec![1, 2, 3, 4, 5].len() or iterator.count()
```

**Warning Categories:**

- **Style:** More idiomatic ways to write code
- **Correctness:** Code that compiles but is wrong or useless
- **Complexity:** Overly complex code that can be simplified
- **Performance:** Inefficient code that can be optimized

**Suppressing Warnings:** Use `#[allow(clippy::warning_name)]` attributes to suppress specific warnings when they're false positives.

### Practical Exercise Examples

The exercise demonstrates iterative code improvement using both tools:

```rust
// Before: Poorly formatted and non-idiomatic code
const pi:f32=3.14159265359;fn calculate_area(radius:f32)->f32{let foo=radius;foo=foo+1;if foo>0{if foo<10{return 5;}}return 3.14159265359*radius*radius;}

// After cargo fmt: Properly formatted
const PI: f32 = 3.14159265359;

fn calculate_area(radius: f32) -> f32 {
    let number = radius;
    number += 1;
    if number > 0 && number < 10 {
        5
    } else {
        std::f32::consts::PI * radius * radius
    }
}
```

**Common Clippy Fixes:**

- **Naming:** Constants should be uppercase (`PI` not `pi`)
- **Assignment:** Use `+=` instead of `x = x + 1`
- **Variables:** Avoid placeholder names like `foo`, `bar`, `baz`
- **Returns:** Remove unnecessary `return` statements (use tail expressions)
- **Precision:** Use standard library constants (`std::f32::consts::PI`)
- **Logic:** Collapse nested `if` statements into single conditions

## Documentation

Rust provides powerful documentation tools that generate beautiful, searchable websites from your code comments. Documentation is only useful if it's readable, and Rust's documentation system makes it easy to create professional documentation.

### Generating Documentation

```bash
cargo doc --no-deps --open
```

The `--no-deps` flag generates documentation only for your library without including all dependencies, and `--open` automatically opens the generated documentation in your browser.

### Documentation Comments

Rust uses special documentation comments that begin with `///` for outer documentation and `//!` for inner documentation.

````rust
//! # Puzzle Library
//!
//! A library for managing puzzle pieces and their properties.
//! This documentation appears on the main page of your library.

/// The number of pieces in a standard puzzle.
///
/// # History
///
/// This constant represents the traditional 1000-piece puzzle size
/// that has been popular since the 1960s.
pub const PUZZLE_PIECES: u32 = 1000;

/// Represents a puzzle with its properties.
///
/// Puzzles can have different numbers of pieces and names.
/// Use [`PUZZLE_PIECES`] for standard puzzle sizes.
pub struct Puzzle {
    /// The number of pieces in this puzzle
    pub num_pieces: u32,
    /// The name or title of the puzzle
    pub name: String,
}

impl Puzzle {
    /// Creates a new puzzle with the specified number of pieces and name.
    ///
    /// # Examples
    ///
    /// ```
    /// use puzzle_lib::Puzzle;
    ///
    /// let puzzle = Puzzle::new(500, "Mountain Landscape".to_string());
    /// assert_eq!(puzzle.num_pieces, 500);
    /// ```
    pub fn new(num_pieces: u32, name: String) -> Self {
        Puzzle { num_pieces, name }
    }
}
````

### Documentation Features

**Markdown Support:** Documentation comments use CommonMark markdown syntax, the same as GitHub.

**Intradoc Links:** Create links to other parts of your documentation using `[`item_name`]` or `[link text](item_name)`.

**Code Examples:** Include runnable code examples in documentation that are automatically tested.

**Outer vs Inner Comments:**

- **Outer (`///`)**: Document items from outside (functions, structs, constants)
- **Inner (`//!`)**: Document the current module or library from inside

**Documentation Structure:**

- Start with a description paragraph
- Add sections with headers (`#`, `##`, etc.)
- Document structs at the top, fields separately
- Document each function and method individually

### Practical Exercise Examples

The documentation exercise demonstrates comprehensive documentation practices:

```rust
//! # Pumpkin Library
//!
//! A pumpkin is a cultivated winter squash in the genus Cucurbita.
//! The term is most commonly applied to round, orange-colored squash varieties.
//!
//! ![Pumpkin Image](https://upload.wikimedia.org/wikipedia/commons/thumb/5/5c/Pumpkins.jpg/800px-Pumpkins.jpg)

/// Represents a pumpkin with its properties.
///
/// # Recipes
///
/// Recipes will most assuredly be coming very, very soon.
pub struct Pumpkin {
    /// The roundness of the pumpkin as a percentage
    pub roundness: f32,
    /// The orangeness of the pumpkin (number from 8 to 27)
    pub orangeness: u8,
}

impl Pumpkin {
    /// Smashes the pumpkin, making it unusable for pie 😢
    ///
    /// If you smash the pumpkin, it will be gone. Then it can't be used for pie.
    pub fn smash(&mut self) {
        // Implementation details...
    }
}

/// Burnt orange color value for the [`orangeness`] field in the [`Pumpkin`] struct.
pub const BURNT_ORANGE: u8 = 18;
```

**Key Documentation Features Demonstrated:**

- **Module-level documentation** with `//!` (inner comments)
- **Images and markdown** support in documentation
- **Struct documentation** with description and sections
- **Field documentation** explaining semantics and valid ranges
- **Method documentation** with emojis and clear explanations
- **Intradoc links** using `[`item_name`]` syntax
- **Constants documentation** with links to related items

## Publishing

Publishing a crate to crates.io makes it available to the entire Rust community. The process involves careful preparation since published packages are permanent and cannot be removed.

### Important Considerations

**Permanence:** Once published, packages cannot be removed from crates.io. This means you should never publish private credentials, SSH keys, AWS tokens, or other sensitive information.

**Unique Names:** Package names must be unique across the entire crates.io registry since there's only one global namespace. Choose names carefully as they cannot be changed after publication.

### Setup Process

1. **GitHub Account:** Create a GitHub account if you don't have one (required for authentication)
2. **Login to crates.io:** Visit crates.io and log in with your GitHub account
3. **Create API Token:** Go to Account Settings → API Access → New Token
4. **Authenticate Locally:** Run `cargo login` and enter your access token

### Package Configuration

Configure your `cargo.toml` with essential metadata before publishing:

```toml
[package]
name = "your-package-name"
version = "0.1.0"
authors = ["Your Name <email@example.com>"]
edition = "2021"
description = "A brief description of what your package does"
license = "MIT OR Apache-2.0"
repository = "https://github.com/username/repo-name"
homepage = "https://github.com/username/repo-name"
readme = "README.md"
keywords = ["keyword1", "keyword2", "keyword3"]
categories = ["category1", "category2"]

[dependencies]
# Your dependencies here
```

**Key Configuration Fields:**

- **`description`**: Brief summary visible on crates.io
- **`repository`**: Link to your source code (GitHub, GitLab, etc.)
- **`homepage`**: Project website (often same as repository)
- **`readme`**: Path to README file (rendered on crates.io page)
- **`keywords`**: Up to 5 search terms to improve discoverability
- **`categories`**: Up to 5 categories from crates.io's predefined list
- **`license`**: License type(s) - use "OR" to separate multiple licenses

### Publishing Process

```bash
cargo publish
```

The publish command performs several checks:

- Ensures no uncommitted changes exist
- Recompiles the entire project
- Verifies the version hasn't been published already
- Uploads the package to crates.io

### Documentation Hosting

**Automatic Documentation:** All published crates automatically get documentation generated and hosted on docs.rs. This is often the first place users look to understand how to use your package.

**Benefits:** Reduces friction for developers who want to document their code, as documentation is automatically generated and publicly accessible.
