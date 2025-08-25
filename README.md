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
```

Tail expressions automatically return values when the semicolon is omitted from the last expression in a function block. Function calls require arguments in the correct order since Rust doesn't support named parameters, and macros are distinguished by their exclamation mark suffix.

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
```

Tuples have a maximum **arity** of 12 elements and use dot syntax to emphasize that members can be different types. Arrays are fixed-size, stack-allocated, and typically replaced with vectors for dynamic collections.

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

// Functional programming style with lazy evaluation
let result: Vec<i32> = (0..10)
    .filter(|&x| x % 2 == 0)
    .map(|x| x * 2)
    .collect();

// Destructuring in for loops
let pairs = vec![(1, 2), (3, 4), (5, 6)];
for (x, y) in pairs {
    println!("({}, {})", x, y);
}
```

Ranges use `..` for exclusive bounds and `..=` for inclusive bounds, with the start being inclusive and end being exclusive by default. For loops can destructure items and bind parts to variables, similar to let statements but with variables local to the loop body.

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

## Ownership

Ownership is Rust's core memory management system with three fundamental rules: each value has exactly one owner, ownership can be transferred through moves, and values are automatically dropped when their owner goes out of scope. This system prevents memory leaks and data races without garbage collection.

```rust
let s1 = String::from("hello");
let s2 = s1;                         // s1 is moved to s2
// println!("{}", s1);              // Error: s1 no longer valid

let s3 = s2.clone();                 // Deep copy (expensive)
let s4 = s2;                         // s2 is moved to s4
```

When values are moved, the original variable becomes invalid and cannot be used. The `clone` method creates deep copies for heap-allocated data, while simple types like integers implement `Copy` and are automatically duplicated. Function parameters take ownership unless references are used.

## References and Borrowing

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

## Structs

Structs are Rust's primary way to create custom data types, combining data fields with associated methods and functions. Struct definitions use the `struct` keyword with capital camel case naming, and all fields must be specified when creating instances unless default constructors are provided.

```rust
struct RedFox {
    energy: u32,
    name: String,
}

impl RedFox {
    fn new() -> Self {
        RedFox {
            energy: 70,
            name: String::from("Red Fox"),
        }
    }

    fn run(&mut self) {
        self.energy -= 1;
    }
}

let mut fox = RedFox::new();
fox.run();
```

The `impl` block defines all behavior for a struct, separating data definition from implementation. Methods take `self` as their first parameter and are called on instances, while associated functions (like constructors) don't take `self` and are called on the type itself. The `Self` type alias refers to the struct being implemented, and multiple `impl` blocks can exist for the same struct to organize related functionality. This pattern of defining data with `struct` and behavior with `impl` is the standard way to create custom types in Rust.

## Traits

Traits define shared behavior that can be implemented by multiple types, serving as Rust's alternative to inheritance. Traits specify required methods that implementing types must provide, enabling generic functions that work with any type implementing a specific trait.

```rust
trait Noisy {
    fn get_noise(&self) -> &str;
}

impl Noisy for RedFox {
    fn get_noise(&self) -> &str {
        "meow"
    }
}

fn print_noise<T: Noisy>(item: T) {
    println!("{}", item.get_noise());
}
```

Traits can have default implementations, inherit from other traits, and be implemented for any type as long as either the trait or type is defined in your crate. The `Copy` trait allows types to be copied instead of moved.
