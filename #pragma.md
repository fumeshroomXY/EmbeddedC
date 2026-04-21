# #pragma
A **compiler directive** used in C and C++ to give **special instructions to the compiler** that are not part of the language syntax itself.
## What is #pragma?
It literally means: “Compiler, please do something special here.”, but it is **compiler‑specific**.
```c
#pragma something
```
- It is **handled before compilation**
- It does **not generate code**
- It **controls compiler behavior**

## Most common and important uses
### #pragma once (VERY common)
Prevents a header file from being included more than once.
```c
#pragma once

class MyClass {
};

// This replaces the traditional include guards:
#ifndef MYCLASS_H
#define MYCLASS_H
// code
#endif
```

### Warning control
Enable or disable warnings. **Silence known, safe warnings**.
```c
// MSVC example:
#pragma warning(disable : 4996)

// GCC / Clang example:
#pragma GCC diagnostic ignored "-Wunused-parameter"
```

### Platform / compiler‑specific features
```c
#pragma optimize("", off)

#pragma comment(lib, "ws2_32.lib")
```

## Is #pragma portable?
Partially
- Some pragmas are **standardized**
- Many are **compiler-specific**
- Code using #pragma may behave differently on different compilers

Best practice:
```c
#ifdef _MSC_VER
#pragma warning(disable:4996)
#endif
```
