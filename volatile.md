# Volatile
One of the hardest C keywords  
`volatile` tells the compiler:  
“This value can **change at any time**, outside of this code’s control.”

## The compiler’s default assumption (important)
Without volatile, the compiler assumes:  
“If this code didn’t change it, it must be the same.”

So the compiler may:
- Cache the value in a CPU register
- Remove repeated reads
- Remove “useless” writes
- Reorder accesses  

All of this is legal and correct for normal memory.

## Why this breaks hardware
Hardware registers:
- **Change due to hardware events**
- Trigger actions when written
- Must be read/written exactly as written  

The compiler does not know this unless you tell it. That’s what `volatile` does.

Example:
```c
int flag = 0;

while (flag == 0) {
    // wait
}

// The compiler may optimize this to:
if (flag == 0) {
    while (1);   // infinite loop
}
```
Because:
- Nothing in the loop changes flag
- So it assumes flag never changes

Now add `volatile`:
```c
volatile int flag = 0;

while (flag == 0) {
}
```

Compiler must:
- Re-read flag every loop iteration
- Cannot assume it stays 0


Example:
```c
#define GPIOA_ODR (*(volatile unsigned int*)0x40000014)
```

Without `volatile`, the compiler might:

- Treat it as a normal variable in memory
- Read it once
- Modify it in a register
- Never write back

With `volatile`, the compiler might:
- Every read → actual memory read
- Every write → actual memory write
- No caching
- No removal

```c
GPIOA_ODR |= (1 << 5);
GPIOA_ODR |= (1 << 6);
```
Without volatile, compiler might:
- Load once
- OR both bits in register
- Write once

With `volatile`, compiler:
- Two separate writes
- Each write may trigger side effects

## What volatile does NOT do (very important)

- Does NOT make code thread-safe
- Does NOT prevent race conditions
- Does NOT insert memory barriers
- Does NOT make operations atomic

It ONLY affects **compiler optimizations**.

## Why `volatile` does NOT stop optimization
`volatile` only **prevents removal/reordering of accesses to that variable**.  

Other optimizations still happen.
```c
volatile int a;
int b;

b = a + a;
```
- Compiler must read a twice
- Still optimizes arithmetic

## Embedded golden rule  
Every memory-mapped hardware register must be accessed through a **volatile-qualified type**. No exceptions.
