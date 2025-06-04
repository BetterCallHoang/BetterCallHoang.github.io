---
layout: post
title: "[C Note] extern \"C\""
date: 2025-04-13
author: "Hoang Nguyen"
categories: [Tech]
tags: [C notebook]
---



This series is intended to be my notebook about the C language concepts that I misunderstand.

---

I usually meet this pattern defined in C header files: 

```c
#ifdef __cplusplus
extern "C" {
#endif

// Your C declarations (functions, variables, etc.)

#ifdef __cplusplus
}
#endif

```

So what actually is the usage when we insert that `#ifdef __cplusplus`  condition?

The main reason stems from the suitability of C libraries when we want to use them in C++.

In C++, we has a feature that called function overloading which supports functions could use the same name, as long as the parameters are different:

```cpp
#include <iostream>

void greet() {
    std::cout << "Hello, world!" << std::endl;
}

void greet(const char* name) {
    std::cout << "Hello, " << name << "!" << std::endl;
}

int main() {
    greet();           // Calls greet()
    greet("Hoang");    // Calls greet(const char*)
    return 0;
}

```

That feature create a mangled name for each functions, which use to distinguishes the same-name functions, for example:

| Function | Mangled Name (example) |
| --- | --- |
| `void greet()` | `_Z5greetv` |
| `void greet(const char*)` | `_Z5greetPKc` |

Because of that, the `#ifdef __cplusplus` is used in the case where a C library is used in C++, and it tells the C++ compiler to insert the `extern "C"` keyword, which is used to keep the functions in C libraries in their original names; otherwise, the linker won’t find these functions due to the name mismatch, for example:

- C library:

```c
// lib.c
#include <stdio.h>

void say_hello() {
    printf("Hello from C!\n");
}

```

- C++ main:

```c
// main.cpp
// Missing extern "C" here!
void say_hello();

int main() {
    say_hello();  // C++ expects a mangled version of the symbol
    return 0;
}

```

- The compile commands:

```c
gcc -c lib.c        # compiles lib.c to lib.o
g++ -c main.cpp     # compiles main.cpp to main.o (with mangled name)
g++ main.o lib.o -o app  # ⛔ linker will fail here
```

- The link error occur:

```c
undefined reference to `say_hello()`
```

Why?

- `lib.c` defines symbol: `say_hello` (plain C)
- `main.cpp` expects: `_Z9say_hellov` (mangled C++ version)

Question: Why we don’t use directly `gcc -c lib.c` so it gets mangled too, then we won’t have the mismatch?
Answer:  It's not a good idea:

1. **Subtle bugs**:
    - C and C++ treat things differently (e.g., implicit casts, `malloc` return types, `struct` keyword).
    - Code that compiles in C may break or behave differently in C++.
2. **You lose true C compatibility**:
    - If you’re using a real C library (like `math.h`, POSIX APIs, embedded drivers), they expect **true C linkage**.
3. **Defeats the purpose** of writing in C:
    - Using `g++` changes the **language mode**, not just the compiler.

Fix: 

```c
extern "C" void say_hello();  // <--- fixes it!

int main() {
    say_hello();
    return 0;
}
```
