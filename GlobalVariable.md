# Global Variable
```c
int g_count = 0;   // global variable
```

- Defined at file scope (outside any function)
- Exists for the entire lifetime of the program
- Stored in static storage
- Initialized to **0** by default if not explicitly initialized

## Linkage of global variables
### External linkage (default)
- Accessible from other .c files
- Only **one definition** allowed in the entire program

**Use it across files with *extern*:**
```c
// header.h
extern int total;

// source.c
int total = 10;
```  
<br>


**Using a global before its definition (same file):**   
If you want to use the global before it’s defined, you need extern as a **forward declaration**:
```c
extern int counter;   // forward declaration

void inc(void) {
    counter++;        // OK
}

int counter = 0;      // definition
```

### Internal linkage (static global variable)
- Accessible only within **the same .c file**(`static` = **private global**)
- Hidden from the linker(**Not visible** to other translation units during linking)
- Prevents name collisions

**Hidden from the linker**
```c
// file1.c
static int counter = 0;
```
What the linker sees:
- The symbol counter does NOT exist outside file1.c
- Other .c files cannot see or reference it
- The linker treats it as a **private** symbol

Contrast with:
```c
// file1.c
int counter = 0;   // external linkage
```
- Now `counter` is a global symbol

- The linker allows other files to reference it via extern

<br>

**Prevents name collisions**  
Imagine a large project:
```c
// a.c
int buffer[128];
// b.c
int buffer[256];
```
This causes a **linker error**: multiple definition of `buffer`.  
Now with static:
```c
// a.c
static int buffer[128];
// b.c
static int buffer[256];
```
No error, because:
- Each buffer exists only inside its own file
- The linker never tries to merge them

## Common mistakes
- Global variables in header files(**Wrong!**)
```c
// header.h
int value = 0;
```
If included in multiple .c files -> **multiple definition linker error**

- Static global variables in header files(**Wrong!**)
```c
// header.h
static int value = 0;
```
Each .c file gets **its own copy** (not shared)


## Best practices
- Prefer static globals over external ones
- Limit usage and scope
- Use extern in headers, definition in exactly one source file
