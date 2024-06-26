---
title: "Yasi (Yet another scheme implementation)"
draft: false
---

[Yasi](https://github.com/mrunix00/Yasi) is my own implementation of the 
[Scheme](https://en.wikipedia.org/wiki/Scheme_(programming_language)) dialect of
the [Lisp programming language](https://en.wikipedia.org/wiki/Lisp_(programming_language)),
written from scratch in C++.

{{< rawhtml >}}
<img src="yasi-screenshot.png" alt="Yasi screenshot" width="100%">
{{< /rawhtml >}}

## Roadmap

- [x] REPL Loop
- [x] Load and execute files
- [x] Basic arithmetic operations (+-*/)
- [x] Print statement
- [x] Input statement
- [ ] File I/O operations
- [x] Strings
- [x] Comments
- [x] Variables
- [x] Conditional statements
- [x] Functions
- [x] Lambda functions
- [ ] Pairs
- [ ] Lists
- [ ] Macros
- [ ] Load and execute libraries
- [ ] Mathematical functions (sin, cos, tan, etc.)
- [x] Compilation to bytecode
- [x] Bytecode execution
- [ ] Optimization
    - [x] Constants substitution
    - [ ] Compile-time evaluation
    - [ ] Functions inlining [(WIP)](https://github.com/mrunix00/Yasi/commit/89e010e5623f7aabdba738418d38414f132de3e2)
    - [x] Tail call optimization
    - [ ] JIT Compilation
- [x] Error handling
- [ ] Garbage collection
- [ ] Windows Support
