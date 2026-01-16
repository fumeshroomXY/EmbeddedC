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

**Why use static functions?**

# Static Local Variable

# Static Global Variable
