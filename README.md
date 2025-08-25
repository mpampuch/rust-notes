# rust-notes

Some tips / things of notes for myself while I'm learning rust

## Chapter 1: Introduction to Rust

### What is Rust?

Rust is a **systems programming language** that pursues the trifecta of:

- **Safety** - guaranteed at compile time
- **Fearless concurrency** - easier when things are safe
- **Blazingly fast speed** - due to zero-cost abstractions

### Rust vs Other Languages

- **High-level languages** (Ruby, Python): Safety ✅, Concurrency ❌, Speed ❌
- **Systems languages** (C, C++): Speed ✅, Concurrency ✅, Safety ❌ (shoot yourself in the foot by design)
- **Rust**: Safety ✅, Concurrency ✅, Speed ✅

### History and Origins

- **Started**: 2006 as personal project by Graydon Hoare (Mozilla employee)
- **Mozilla sponsorship**: 2009
- **Version 1.0**: 2015 (5 years old after 9-year incubation)
- **Comparison**: C++ is ~35 years old, C is ~45 years old

### Why Mozilla Sponsored Rust

- Mozilla was tired of C++ and wanted a better language for Firefox
- **Firefox Quantum** (2017): Core rewritten in Rust
- Result: Firefox became over twice as fast and less buggy
- Today: ~1.5 million lines of Rust in Firefox

### Learning Approach

- **Steep learning curve** at the beginning
- **Fundamentals are crucial** - without them, code won't compile
- **Learn by doing**: Do exercises, create projects, experiment
- **Anyone can learn Rust** and become a systems programmer
- **Have fun** and learn a little more each day

### Key Takeaways

- Rust combines the best of both worlds: safety of high-level languages with performance of systems languages
- The language is relatively young but has proven itself in production (Firefox)
- Focus on fundamentals first - they're essential for getting code to compile
- Practice is essential due to the steep learning curve
