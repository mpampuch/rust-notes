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
