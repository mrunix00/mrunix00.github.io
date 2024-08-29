---
title: "SPL (Simple Programming Language)"
draft: false
---

[SPL](https://github.com/mrunix00/spl) is a simple interpreted, garbage collected and statically typed programming language.

## Syntax
### Variables declaration

{{< rawhtml >}}
<table>
<td>
define x : int = 42;
<br>
define y = 42;
</td>
</table>
{{< /rawhtml >}}

### Arrays
{{< rawhtml >}}
<table>
<td>
define arr : int[] = [1, 2, 3, 4, 5];
<br>
define element = arr[0];
</td>
</table>
{{< /rawhtml >}}
## Function declarations
{{< rawhtml >}}
<table>
<td>
define add : function(x : int, y : int) -> int = {
<br>&nbsp&nbsp&nbsp&nbsp
    return x + y;
  <br>
};
</td>
</table>
{{< /rawhtml >}}

## Conditions
{{< rawhtml >}}
<table>
<td>
if x == 42 {
<br>
&nbsp&nbsp&nbsp&nbsp // do something
<br>
} else {
<br>
&nbsp&nbsp&nbsp&nbsp // do something else
<br>
};
</td>
</table>
{{< /rawhtml >}}

## Loops
{{< rawhtml >}}
<table>
<td>
while x < 42 {
<br>
&nbsp&nbsp&nbsp&nbsp // do something
<br>
};
<br>
for define i = 0; i < 20; i++ {
<br>
&nbsp&nbsp&nbsp&nbsp // do something
<br>
};
</td>
</table>
{{< /rawhtml >}}

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
