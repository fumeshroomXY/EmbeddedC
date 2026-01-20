# scanf
A standard input function declared in <stdio.h>. 
```c
scanf("format", &var1, &var2, ...);  // addresses of variables (very important!)

int x;
scanf("%d", &x); // Forgetting & is a classic bug.
```

## Return value
```c
int ret = scanf("%d", &x);

// Always check it
if (scanf("%d", &x) != 1) {
    // input error
}
```
scanf returns:
- Number of successfully assigned items
- `EOF` on failure or end of input

## Why it is unsafe
### No automatic bounds checking (BIGGEST problem)
```c
char buf[8];
scanf("%s", buf);   // ❌ unsafe

// input: this_is_very_long

// safer version:
scanf("%7s", buf);
```
`%s` does not know buffer size unless you tell it:
- Overwrites stack memory
- Causes crashes or security vulnerabilities

### Hard to detect errors if return value is ignored
```c
int x;
scanf("%d", &x);   // ❌ return value ignored

// input: abc
```
- `scanf` fails
- `x` remains uninitialized
- Program continues with garbage data

### Partial reads leave garbage in input buffer
```c
scanf("%d", &x);
scanf("%d", &y);

// input: 10abc20
```
- First read → 10
- `abc` remains in buffer
- Second scanf fails  

This causes confusing bugs.

### Format string mismatch = undefined behavior
```c
double d;
scanf("%f", &d);   // ❌ WRONG (%lf required)
```

## Embedded systems warning
- Heavy code size
- Requires heap / large stack
- Hard to retarget input
- No control over blocking behavior  

Many embedded coding standards (MISRA, CERT C): **Discourage or forbid** `scanf`

## Safer alternative
For strings
```c
fgets(buf, sizeof buf, stdin);
```

For numbers
```c
char buf[32];
fgets(buf, sizeof buf, stdin);
sscanf(buf, "%d", &x);
```
