# Rust Experiences (---- MERGED WITH REDMINE. DO NOT EDIT ANYMORE. ----)

## Introduction

Lessons learnt when using Rust for a short while during MCPTT project are described in this document. 

## Rust Pros

- **Good package manager**. ![Cargo](https://doc.rust-lang.org/cargo/), the Rust's package manager, completely handles dependencies,
building them, and publishing the package to the web.
- **Memory safety**. Due to a stricter type-system than C/C++, Rust programs are less vulnerable to memory bugs.
- **No garbage collector**. For applications that performance predictability is a requirement, such as IMS core or RCS services,
languages with no garbage collector are better choices as the operation of garbage collector imposes a non-deterministic high-load on
the system.

## Rust Cons

- **Poor ecosystem**. Rust lacks support of many popular third-party libraries. For example, Apache Ignite has no official
support for Rust.
