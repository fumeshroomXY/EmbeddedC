**Optimization levels** in embedded C control how aggressively the compiler trades **code size, speed, debuggability, and predictability**.

# What is an optimization level?
An optimization level tells the compiler:  

**How hard should I try to make the code faster/smaller?**

This affects:
- Instruction selection
- Inlining
- Loop transformations
- Register allocation
- Dead code removal
- Constant folding

# Common optimization levels (GCC / Clang)
| Level | Meaning                 |Characteristics                   |Embedded impact          |
| ----- | ----------------------- |--------------------------        |------------------------ |
| `-O0` | No optimization         |Very slow and large code          |   Best for debugging    |
| `-O1` | Basic optimization      | Some dead code removal, Low risk| Minor speed & size gains(Rarely used) |
| `-O2` | Aggressive optimization | Good balance of Speed and Code size |Default for release(**Most Common**), Good for Performance-sensitive code|
| `-O3` | Maximum optimization    |Code size increases, Unpredictable timing | Risky for embedded       |
| `-Os` | Optimize for size       |Disables optimizations that increase size| **Very common** in MCU, Perfect for Flash-limited MCUs|
| `-Og` | Debug-friendly optimization| 


# -O0
## What the compiler does
- Almost **1-to-1 mapping** from C → assembly
- Keeps:
  - local variables in memory
  - stack frames intact
  - instructions in source order
- No instruction reordering
- No inlining
- No dead code elimination

## Why it exists
- Debugging
- Learning assembly
- Stepping through code with `gdb`

## Example
```c
int add(int a, int b) {
    int c = a + b;
    return c;
}
```
With `-O0`, you’ll likely see:
- stack space allocated for `c`
- `a`, `b`, `c` all stored in memory
- extra `mov` instructions

**Very predictable, very readable assembly.**

# -O2
## What the compiler does
Some key optimizations enabled:
### Dead code elimination
```c
if (1 + 1 == 3) { // evaluates expressions it can fully determine at compile time
    do_something();  // the code vanishes under -O2
}
```
completely removed

```c
int x = read_sensor();

if (x) {
    do_something();
}
```
- Since `x` depends on runtime input, compiler must **keep the branch**.

### Inlining
```c
int square(int x) { return x * x; }
```
- function call disappears

### Register allocation
- Variables live in **registers**, not memory
- Stack variables may not exist at all

#### Example1: One variable
```c
int foo(void) {
    int x = 5;
    return x + 1;
}
```

##### -O0 (typical behavior)
- `x` gets a stack slot, **exists in memory**
- Load → store → reload

```markdown
push rbp
mov  rbp, rsp
sub  rsp, 16

mov  DWORD PTR [rbp-4], 5   ; x = 5
mov  eax, DWORD PTR [rbp-4]
add  eax, 1

leave
ret
```

##### -O2
- `x` **never existed**
- Compiler computed the result directly
- No stack frame at all
- Register allocation + constant folding

```markdown
mov eax, 6
ret
```

#### Example 2: Variable lives only in a register
```c
int foo(int a) {
    int x = a + 1;
    int y = x + 2;
    return y;
}
```

##### -O0
- `x` and `y` both on stack
```markdown
mov DWORD PTR [rbp-4], edi   ; a
add DWORD PTR [rbp-4], 1    ; x
mov eax, DWORD PTR [rbp-4]
add eax, 2                ; y = x + 2
```

##### -O2
```markdown
lea eax, [rdi + 1]   ; x in eax
add eax, 2        ; y in eax
ret
```
- `x` and `y` **share the same register**
- No memory
- Lifetime of x ends exactly where y starts

##### Why `gdb` says `<optimized out>`
At `-O2`:
- `x` exists only **temporarily**, then overwritten


Debugger view:
```bash
(gdb) print x
$1 = <optimized out>
```
Why?
- No memory location
- No stable register
- Value no longer exists
This is **register allocation + lifetime shortening**

#### Example 3: Register spilling (important!)
Registers are limited.  
Register spilling happens when the compiler runs out of registers and has to **temporarily store some values in memory** (usually the stack).

```c
int foo(int a, int b, int c, int d, int e) {
    int v1 = a + b;
    int v2 = b + c;
    int v3 = c + d;
    int v4 = d + e;
    return v1 + v2 + v3 + v4;
}
```
Possible behavior:
- Some variables in registers
- Others **spilled** to stack
```c
mov DWORD PTR [rsp-4], edx   ; spill v3
...
```

A variable is **live** if:
- its value has been computed
- AND it will be used again later

If many variables’ live ranges **overlap**, high register pressure goes up.


##### Spilling around function calls (VERY common)
Calling conventions say:
- some registers are caller-saved
- function calls may **clobber** them(= to overwrite a value you didn’t mean to keep)

So if a value must survive a call:
- it either goes into a callee-saved register
- OR gets spilled to the stack

```c
int foo(int a) {
    int x = a + 1;
    bar();          // unknown function
    return x + 2;
}

lea eax, [rdi + 1]
mov DWORD PTR [rsp-4], eax   ; spill x
call bar
mov eax, DWORD PTR [rsp-4]   ; reload x
add eax, 2
ret
```
This is *forced* spilling due to ABI rules.

##### Loop-related spilling
Loops increase pressure because:
- values must persist across iterations
- unrolling duplicates live values

```c
for (int i = 0; i < n; i++) {
    sum += a[i] * b[i] + c[i];  // i, n, sum, a, a[i], b, b[i], c, c[i] are live
                                // i, n, sum, a, b, c need to be live across iterations.
}

// Unroll by 2
for (int i = 0; i < n; i += 2) {
    sum += a[i] * b[i] + c[i];
    sum += a[i + 1] * b[i + 1] + c[i + 1]; // i, n, sum, a, a[i], a[i+1] b, b[i], b[i+1], c, c[i], c[i+1] are live
                                           // Unrolling duplicates live temporaries.
}
```
Unrolled loop → more temporaries → more spills.

#### Example 4: When register allocation is FORCED off
`volatile` blocks the optimization.
```c
int foo(void) {
    volatile int x = 5; // here is for demonstration, volatile on a plain local is usually uncommon
    return x + 1;
}
```
Now compiler **MUST**:
- write `x` to memory
- read it back

Even at `-O2`:
```markdown
mov DWORD PTR [rsp-4], 5
mov eax, DWORD PTR [rsp-4]
add eax, 1
ret
```
Very important for **embedded**.

### Instruction reordering
- As long as observable behavior is the same
- Improves CPU pipeline & cache usage

#### Example: Loop-invariant code motion (big one in -O2)
```c
for (int i = 0; i < n; i++) {
    sum += a[i] * c;
}
```
`-O2` reorders
- load `c` **once**
- **before** the loop

```c
mov ecx, c        ; hoisted out of loop
.Lloop:
imul eax, ecx
...
```

### Loop optimizations
- Loop unrolling
- Strength reduction
- Removing redundant calculations

#### Example: Strength reduction (cheap ops replace expensive ones)
```c
for (int i = 0; i < n; i++) {
    sum += a[i * 4];
}

// -O2 rewrite:
int *p = a;
for (int i = 0; i < n; i++) {
    sum += *p;
    p += 4;
}
```
- no multiply inside loop
- just pointer increments

### What disappears under `-O2`
- Some variables
- Some stack frames
- Some function calls
- Sometimes entire functions

This is why optimized assembly can look **nothing like** your C code.

# Practical advice
## Common workflow
```bash
# Debug build
gcc -O0 -g main.c

# Release build
gcc -O2 main.c
```
## When learning assembly
Always start with `-O0`.  
Then later:

- compile the same code with `-O2`
- diff the assembly
- ask: what did the compiler remove or rewrite?

That’s how you really learn optimization.

```c
int x = 10;
printf("%d\n", x);
```
Under `-O2`:
- `x` may **never exist**
- `10` may be printed directly
- No stack slot, no memory write

So if you’re asking: “Why doesn’t the stack look like I expected?”

**Check your optimization flag first.**

