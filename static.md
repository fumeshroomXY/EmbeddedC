This article is about something worth to note when using the keyword `static` in C.
# Static Function
In C, a static function is a function with internal linkage, meaning it is **visible only within the .c file where it is defined**.  
```c
static void helper(void) {
    /* ... */
}
```
- The function can be called only inside this source file
- It is hidden from the linker
- Other .c files cannot call it, even with `extern`
static function = **private function**

## Why use static functions?
- Prevent name collisions
```c
// a.c
static void init(void) { }
// b.c
static void init(void) { }
```
No conflict — each init is private to its file.

- Encapsulation (important)
```c
// uart.c
static void config_hw(void) { }
void uart_init(void) {
    config_hw();
}

// uart.h
void uart_init(void);
```
`config_hw()` is an implementation detail. Users cannot depend on it accidentally.

- Better optimization  
Because the function is only used in one file, the compiler can inline it, remove unused ones and do more aggressive optimizations.

# Static Local Variable
In C, a static local variable is a variable declared inside a function that:
- Keeps its value between function calls
- Is initialized only **once**
- Has **block scope** (only visible inside that function)
- Has static storage duration (exists for the **whole program**)

**Basic example**
```c
#include <stdio.h>

void counter(void) {
    static int count = 0;  // static local variable
    count++;
    printf("%d\n", count);
}

int main(void) {
    counter();
    counter();
    counter();
}

// Output
// 1
// 2
// 3
```
**Initialization rules (important!)**
```c
void f(void) {
    static int x = 5 + 3;   // OK (constant expression)
    static int y = foo();   // × ERROR
}
```
- Must be a compile-time constant
- Initialized before main() starts

## Why use static local variables?
- Maintain state without globals
```c
int get_id(void) {
    static int id = 0;
    return ++id;
}
```
(1) State is hidden  
(2) Cleaner than a global variable

- Implement one-time initialization
```c
void init(void) {
    static int initialized = 0;
    if (!initialized) {
        setup();
        initialized = 1;
    }
}
```
- Counters, caches, memoization
```c
int fib(int n) {
    static int memo[100];
    if (memo[n]) return memo[n];
    /* compute */
}
```

# Static Global Variable
How to use static global variable is explained in the article [GlobalVariable.md](https://github.com/fumeshroomXY/EmbeddedC/blob/main/GlobalVariable.md).
