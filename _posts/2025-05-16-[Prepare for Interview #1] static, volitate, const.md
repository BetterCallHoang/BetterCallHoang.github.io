---
layout: post
title: "[Note C] static, volitate, const keyword"
date: 2025-05-16
author: "Hoang Nguyen"
categories: [Tech]
tags: [Interview]
---
Table of Contents:
* Table of Contents:
{:toc}
---

# 1. What is static? static use cases.

- The `static` keyword has different meanings depends on its use cases. Normally there are three distinct uses of `static` in C:
1. When a static variable is declared in a function, its value will remain throughout the program.

```c
#include <stdio.h>

int increse() {
    static int count = 0;
    count++;
    return count;
}

int main() {
    printf("%d\n", increse());
    printf("%d\n", increse());
    printf("%d\n", increse());
    return 0;
    // The "count" variable increase from 1 to 3 
}
```

1. When a static variable is declared in a module (or a file) but in outside of all the functions, all the functions of that module will be able to access that variable. But other modules cannot access that variable:

```c
//main.c
#include <stdio.h>

static int count = 0;

int increse() {
    count++;
    return count;
}
int decrease() {
    count--;
    return count;
}

int main() {
    printf("%d\n", increse());
    printf("%d\n", increse());
    printf("%d\n", increse());
    printf("%d\n", decrease());
    printf("%d\n", decrease());
    printf("%d\n", decrease()); 
    //All the functions in main.c are able to access count variable
    //Therefore in the end count value = 0
    return 0;
}
```

1. Function decleared static can only be called by other function within that modules.

→ Private function.

```c
// static_ex1.c
#include "static_ex1.h"

int increse(int count) {
    count++;
    decrese(count);
    return count;
}
static int decrese(int count) {
    count--;
    return count;
}
// staic_ex1.h
#ifndef STATIC_EX1_H
#define STATIC_EX1_H
#include <stdio.h>
int increse(int count);
static int decrese(int count);

#endif
// main.c
#include "static_ex1.h"

int main() {
    printf("%d\n", increse(10)); // Success compile
    // printf("%d\n", decrese(10)); // When added this command, compile will fail
    return 0
}
```

# 2. What is volatile? volatile use cases

- A `volatile` variable is one that can change unexpectedly. Consequently, the compiler can make no assumptions about the value of the variable. In particular, the optimizer must be careful to reload the variable each time it is used instead of holding its copy in a register.
- `volatile` use cases:
1. Hardware register in peripherals (e.g. status register)

```c
#define STATUS_REGISTER  (*(volatile unsigned int *)0x40001000)

void wait_for_ready() {
    // Wait until the hardware sets the READY bit
    while ((STATUS_REGISTER & 0x01) == 0) {
        // Spin until ready
    }
}
```

1. Non-stack variables referenced within an interrupt service routine.

```c
volatile int data_ready = 0;

void ISR_Handler(void) {
    data_ready = 1;  // Set by the interrupt
}

void main_loop(void) {
    while (!data_ready) {
        // Wait for interrupt
    }
    // Now process the data
}

```

1. Variables shared by multiple tasks in a multi-threaded application

```c
#include <pthread.h>
#include <stdio.h>

volatile int stop = 0;

void* worker(void* arg) {
    while (!stop) {
        // Perform work
    }
    printf("Worker stopped.\n");
    return NULL;
}

int main() {
    pthread_t thread;
    pthread_create(&thread, NULL, worker, NULL);

    // Simulate some processing
    sleep(1);

    stop = 1;  // Signal the worker to stop
    pthread_join(thread, NULL);
    return 0;
}

```

# 3. What is `const` ? `const` use cases

- The  `const` keyword provides the “read-only” access to the memory location that the symbol represents [3].

```c
const int x = 10;
int x = 11; // warning 
int *y = &x; 
*y = 12; // okay

const int *z = &x; 
*z = 20; // Warning
*(int *)z = 20; //oke
```

- One of the advantages of using const in an embedded system is that in many cases declaring a symbol as const will force that symbol to be stored in ROM.  When the symbol is stored in ROM, the indirect methods of trying to change the value of the symbol will not work. The danger is that the behavior is not specified in the C standard!  A developer has to take care to understand what the compiler is doing and where it is storing the read-only values. Its also make the code more readable and maintainable.

# Resources

[1] [A ‘C’ Test: The 0x10 Best Questions for Would-be Embedded Programmers](https://rmbconsulting.us/publications/a-c-test-the-0x10-best-questions-for-would-be-embedded-programmers/#footnote1)

[2] [Circuits & Code: Mastering Embedded Co-op Interviews](https://circuits-and-code.github.io/)

[3] [Embedded Basics – Peculiarities of the keyword const](https://www.beningo.com/embedded-basics-peculiarities-of-the-keyword-const/)
