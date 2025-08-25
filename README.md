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
