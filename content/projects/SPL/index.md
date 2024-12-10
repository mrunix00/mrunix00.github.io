---
title: "SPL (Simple Programming Language)"
draft: false
---

[SPL](https://github.com/mrunix00/spl) is a simple interpreted, garbage collected and statically typed programming language.

## Syntax
### Variables declaration

```
define x : int = 42;
define y = 42;
```

### Arrays
```
define arr : int[] = [1, 2, 3, 4, 5];
define element = arr[0];
```

## Function declarations

```
define add : function(x : int, y : int) -> int = {
    return x + y;
};
```

## Conditions

```
if x == 42 {
    // do something
} else {
    // do something else
};
```

## Loops

```
while x < 42 {
    // do something
};

for define i = 0; i < 20; i++ {
    // do something
};
```

## Roadmap
- [x] Lexer
- [x] Parser
- [x] REPL shell
- [x] Variables
- [x] Functions
- [x] Conditions
- [x] While loops
- [x] For loops
- [x] Arrays
- [x] Strings
- [ ] Hashmaps
- [ ] Error handling
- [ ] Structs
- [ ] I/O operations
- [x] Bytecode VM
- [ ] Tail call optimization
- [x] Garbage collection
- [x] Native API calls
- [x] Importing libraries
- [ ] Standard library
- [ ] JIT compiler
