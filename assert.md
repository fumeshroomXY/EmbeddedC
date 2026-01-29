# `assert`
```c
#include <assert.h>

int divide(int a, int b) {
    assert(b != 0);   // checked at runtime
    return a / b;
}
```

- Checked while the program is running
- If the condition is false → program **aborts**
- Usually used for debugging
- Can be disabled with `-DNDEBUG`
```c
gcc -DNDEBUG main.c   // assert() does nothing
```

Typical uses
- Catch programmer bugs
- Validate assumptions inside functions
- Things that depend on runtime values


# `static_assert`
```c
static_assert(sizeof(int) == 4, "int must be 4 bytes");
```
- Checked at **compile time**
- If false → compilation fails
- Cannot be disabled
- Condition must be a constant expression
- C11+ or C++11+

