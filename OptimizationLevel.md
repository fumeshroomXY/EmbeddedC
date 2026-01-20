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
