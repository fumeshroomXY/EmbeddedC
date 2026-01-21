# What is a `union`
In embedded C, `union` is a tool for **controlling memory and hardware** — not just a language feature.
```c
union Data {
    uint32_t value;
    uint8_t  bytes[4];
};

union Data d;
d.value = 0x12345678;

// Memory view (little-endian MCU):
bytes[0] = 0x78
bytes[1] = 0x56
bytes[2] = 0x34
bytes[3] = 0x12
```

- All members share the **same address**
- The size of the union is the size of **its largest member**
- Writing to one member affects how the others are interpreted

# Why unions are common in embedded systems
## Save RAM (very important on MCUs)
```c
union Buffer {
    uint8_t  rx[64];
    uint16_t adc[32];
};
```
- Only 64 bytes total, not `64 + 64`
- Useful when buffers are used **at different times**

## Access hardware registers in different ways
```c
typedef union {
    uint32_t REG;
    struct {
        uint32_t EN   : 1;
        uint32_t MODE : 2;
        uint32_t      : 29;
    } BIT;
} CTRL_Reg;

CTRL_Reg ctrl;

ctrl.REG = 0;      // clear register
ctrl.BIT.EN = 1;   // set enable bit
ctrl.BIT.MODE = 2;
```
- Whole-register access
- Bit-field access
- Cleaner code than manual bit masking

## Protocol parsing (CAN, UART, SPI, I²C)
```c
typedef union {
    uint8_t raw[8];
    struct {
        uint16_t id;
        uint8_t  len;
        uint8_t  data[5];
    } frame;
} CAN_Packet;
```
You receive bytes, then interpret them as a structured packet.

# `union` + `volatile` (very important!)
For **memory-mapped registers**:
```c
typedef volatile union {
    uint32_t REG;
    struct {
        uint32_t RDY : 1;
        uint32_t ERR : 1;
        uint32_t     : 30;
    } BIT;
} STATUS_Reg;

#define STATUS (*(STATUS_Reg *)0x40000000)
```
Without `volatile`:
- Compiler may cache `STATUS.REG`
- Bit-field reads may be optimized away
- Hardware changes may never be seen

With `volatile`:
- Every read of `STATUS.REG` or `STATUS.BIT.RDY` generates a real memory access
- Hardware changes are always observed

## Why `volatile` must be on the UNION, not just members
```c
typedef union {
    volatile uint32_t REG;
    struct {
        volatile uint32_t RDY : 1;
        volatile uint32_t ERR : 1;
    } BIT;
} STATUS_Reg;
```
Why this is bad:
- Bit-fields don’t guarantee volatile behavior correctly
- Accessing the union itself may still be optimized


# When *not* to use unions
- High-level application logic
- When **portability** across compilers/architectures is critical
- When `struct` + `memcpy` is clear and fast enough instead of using a `union` to reinterpret memory




