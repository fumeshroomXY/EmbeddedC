# Declaring and using a struct
```c
struct Person {
    int age;
    float height;
};

struct Person p1;  //Must write "struct", different from C++

p1.age = 25;
p1.height = 1.75f;

// Initialization
struct Person p = {25, 1.75f};

// Designated initializers (recommended)
struct Person p = {
    .age = 25,
    .height = 1.75f
};
```

## `struct` vs `typedef struct`
```c
// Without typedef
struct Person p;

// With typedef(Very Common)
typedef struct {
    int age;
    float height;
} Person;

Person p;   // no "struct" keyword needed
```

# `struct` memory layout (important!)  
Struct members are stored contiguously, but **padding** may be added for alignment.  
## Rule1: Each type has an alignment requirement
Typical (32-bit system):
| Type     | Size | Alignment             |
| -------- | ---- | --------------------- |
| `char`   | 1    | 1                     |
| `short`  | 2    | 2                     |
| `int`    | 4    | 4                     |
| `float`  | 4    | 4                     |
| `double` | 8    | 8 (or 4 on some MCUs) |
| pointer  | 4    | 4                     |


## Rule2: Each member **starts at an offset** that satisfies its alignment.
```c
struct A {
    char c;   // offset 0
    int i;    // offset 4 (padding added)
};

// Layout
c (1)
pad (3)
i (4)
Total = 8 bytes
```

## Rule3: Struct alignment = max member alignment
```c
struct B {
    char c;
    int i;
    short s;
};
```
- Max alignment = 4
- Total size rounded up to multiple of 4

## Why padding exists?
CPUs fetch memory in **words**, not bytes.
Misaligned access may:
- Be slower
- Require multiple memory cycles
- Cause a hardware fault (on some MCUs!)


## Reordering members from largest to smallest
```c
// 12 Bytes
struct Bad {
    char c; // 1 byte, 3 bytes padding
    int i;
    char d; // 1 byte, 3 bytes padding
};

// 8 Bytes
struct Good {
    int i;
    char c;
    char d; // 2 bytes padding
};
```

# Structs in embedded systems
Hardware register mapping
```c
typedef struct {
    volatile uint32_t MODER;
    volatile uint32_t ODR;
    volatile uint32_t IDR;
} GPIO_TypeDef;

#define GPIOA ((GPIO_TypeDef *)0x40020000)

GPIOA->ODR |= (1 << 5);
```


# Bit-fields in structs
A bit-field allows you to specify **how many bits** a struct member occupies.
```c
struct Flags {
    unsigned int a : 1; // a → 1 bit
    unsigned int b : 2; // b → 2 bits
    unsigned int c : 3; // c → 3 bits
};
```
- Total bits = 6
- All fit in one `unsigned int`
- Structure size = 4 bytes (typical)

Bit-fields do not cross storage units.  
If remaining bits aren’t enough, a new unit is started.
```c
struct A {
    unsigned int a : 31;
    unsigned int b : 2;   // forces new int
};
```
- a → first unsigned int
- b → second unsigned int
- Size = 8 bytes

## Why bit-fields are NOT “smaller ints”
Bit-fields:
- Are **not** addressable (&a is illegal)
- Are **not** independent objects
- Are compiler-generated bit slices  
They exist only as parts of a larger integer object.

## Why use bit-fields?
- Save memory
- Represent hardware flags
- Improve readability over manual bit masks
- Non-portable code is acceptable

```c
flags.a = 1;   // clearer than flags |= (1 << 0)
```
The compiler generates code equivalent to:
- Read the whole 32-bit storage unit
- Clear the bit corresponding to `a`
- Set that bit to `1`
- Write the whole 32-bit value back

## Why bit-fields are risky in embedded systems ⚠️
### Implementation-defined behavior
- Bit ordering (MSB vs LSB)
- Padding rules
- Endianness interaction

### Hardware register mismatch
- Bit-fields may not map correctly to registers.  

#### Embedded warning (important)
If `flags` maps to a hardware register:
```c
volatile struct Flags *reg;
reg->a = 1;
```
This may perform a **read-modify-write(RMW)**, accidentally change other bits and break hardware behavior:

A bit-field assignment cannot write just one bit.

So the compiler must do this:

- Read the entire register from hardware
- Modify only the bit for `a`
- Write back the entire register

This sequence is called **read–modify–write**.

#### Why this is dangerous for hardware registers
(1) Some bits are **write-only** or **read-clear**

Many hardware registers have special behavior:

- Reading a bit may clear it
- Writing `0` may clear flags
- Writing `1` may trigger an action

Example:
- Status register: reading clears interrupt flags

So RMW may already:
- Clear bits unintentionally
- Lost updates can occur
- Trigger an unintentional action

(2) `volatile` does **NOT** save you
volatile only guarantees:
- The read happens
- The write happens

It does **NOT** guarantee:
- Atomicity
- Single-bit access

**So avoid bit-fields for hardware registers!!!**
### Correct way for hardware registers
- Use explicit bit masks
```c
#define REG_A_BIT (1U << 0)
*REG |= REG_A_BIT;
```

- With volatile pointer
```c
reg32 |= (1U << 0);
```
This still does RMW, but you control the exact bits and can follow datasheet rules.

- Or use hardware-provided SET/CLEAR registers
```c
// Many MCUs provide:
GPIOx_BSRR = (1 << 5);   // atomic set
```
No read, No race, Safe

## Bit-fields vs bit masks (embedded best practice)
```c
// Bit-field (easy, risky)
flags.a = 1;

Bit mask (safe, explicit)
#define FLAG_A (1U << 0)
flags |= FLAG_A;
```
Most embedded teams prefer bit masks


