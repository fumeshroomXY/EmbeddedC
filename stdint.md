# stdint.h
A standard C header that defines **integer types with guaranteed sizes**.  
It was introduced in C99 to make code **portable and predictable**, especially for **embedded systems**.

**Why <stdint.h> is needed**

Before C99: Sizes(`int`, `long`, `short`) depend on the platform.

With <stdint.h>: `uint32_t`, `int16_t`, `uint8_t`. Sizes are explicit and guaranteed.

## Limit macros (very important)
```c
UINT32_MAX
INT16_MIN
INT32_MAX

if (x > UINT32_MAX) { ... }
```
