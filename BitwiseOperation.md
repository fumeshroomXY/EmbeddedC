# Bitwise Operation

## Test if a specific bit is set(=1) in a number

```c
if (number & (1 << bit_position)) {
    // bit is set (1)
} else {
    // bit is not set (0)
}

// Example: Test if the 5th bit is set
int number = 0b110101;

if (number & (1 << 5)) {
    // bit 5 is set
} else {
    // bit 5 is not set
}
```

**Common mistakes to avoid**:
- ❌ Using OR (|) to test a bit
```c
// Method1: 
if (x | (1 << 5)) {
    // always true, not works.
}

// Method2: 0 << 5  ==  0, x | 0 == x
if (x | (0 << 5)) { //does NOT test bit 5. It tests any bit at all
}
```

## Operation Rules
- Use AND to test
- Use OR to set
- Use AND with the inverse of a mask to clear(=0)
- Use XOR to toggle

# LSB, MSB
LSB — Least Significant Bit  
- The rightmost bit
- Has the smallest value

MSB — Most Significant Bit  
- The leftmost bit
- Has the largest value

## Endianness (related but different!)
| Concept           | Meaning              |
| ----------------- | -------------------- |
| LSB/MSB           | Bit significance     |
| Little/Big Endian | Byte order in memory |  

**Do not** confuse them.



