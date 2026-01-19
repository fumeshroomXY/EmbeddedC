# Registers
In embedded context, a register is a **small hardware storage location** inside a peripheral or CPU.

- Very small (8/16/32 bits)
- Very fast
- Has hardware meaning
- Write 1 → turn LED on, write 0 → turn LED off
- Read value → check button state
- **Not C variables** — they are hardware-controlled.

Many registers are **bit fields**:
```text
31      16 15    8 7      0
+---------+--------+--------+
| reserved| MODE   | PINSEL |
+---------+--------+--------+
```
- Whole register: 32 bits
- Fields: individual bits or groups  

So:
- Register ≠ bit
- Register contains bits

# GPIO
GPIO = General Purpose Input/Output  
A **peripheral** that lets the MCU interact with the outside world.  

Each GPIO pin can:
- Output: LED, relay
- Input: button, sensor signal  

GPIO itself is controlled by **registers**. You read/write its registers.

# Memory-Mapped I/O
Peripheral registers appear as **memory addresses**. The CPU accesses them using normal **load/store instructions**.


Example
```text
0x0000_0000  Flash
0x2000_0000  SRAM
0x4000_0000  GPIOA registers
0x4000_0400  GPIOB registers
```  


Most MCUs expose registers as **word-sized addresses**.  
Example:
```c
0x4000_0000  CONTROL (32-bit) // The address itself is byte-based. A 32-bit register occupies 4 consecutive bytes.
0x4000_0004  STATUS  (32-bit) // So the next register starts at +0x4.
```

Even if:  
- Only 1 bit matters
- Other bits are reserved

The access size is still a word




**Why memory map is powerful**
- No special I/O instructions needed
- Same LDR / STR instructions
- C code can access hardware

**How they relate**
```text
CPU
 │
 │  load/store instructions
 ▼
Memory-Mapped I/O
 │
 ▼
Peripheral Registers
 │
 ▼
GPIO pins
 │
 ▼
LED / Button / Sensor
```

C code example:
```c
// behaves like defining a variable GPIOA_ODR mapped to the address 0x40000014
#define GPIOA_ODR  (*(volatile unsigned int*)0x40000014)

int main(void)
{
    // (*(volatile unsigned int*)0x40000014) |= (1 << 5);
    // 1 << 5  →  0b0010_0000 (Bit 5 = 1, other bits = 0)
    // GPIOA_ODR = GPIOA_ODR | 0b0010_0000, Read GPIOA_ODR 32-bit value, OR with bit 5 set, Write back
    GPIOA_ODR |= (1 << 5);   // turn PA5 HIGH
}
```

