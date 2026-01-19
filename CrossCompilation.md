# Native Compilation
compiling on a machine for the same architecture/OS that will run the program

**Build machine = target machine**

**Native compilation characteristics**
| Aspect    | Native           |
| --------- | ---------------- |
| CPU       | Same             |
| OS        | Same             |
| Compiler  | Native toolchain |
| Testing   | Immediate        |
| Speed     | Usually fast     |
| Debugging | Simple           |

# Cross Compilation
compiling on one machine (**host**) to run on a different machine (**target**).

Example (very common in embedded)  
Host:
- x86_64 Linux PC

Target:
- ARM Cortex-M MCU (no OS)
Command:
```bash
arm-none-eabi-gcc main.c -o main.elf
```
- arm → target CPU
- none → no vendor OS
- eabi → embedded ABI
- gcc → compiler


You cannot run main.elf on your PC.

**Cross compilation characteristics**
| Aspect    | Cross                   |
| --------- | ----------------------- |
| CPU       | Different               |
| OS        | Often different or none |
| Compiler  | Cross toolchain         |
| Testing   | On target or emulator   |
| Debugging | Via JTAG/SWD            |
| Speed     | Slower feedback loop    |

## Why cross compilation is necessary
Embedded targets usually:
- Have no compiler
- Have no filesystem
- Have limited RAM/Flash
- Cannot run gcc

So you must compile elsewhere.

# Build stages comparison
| Stage      | Native   | Cross                   |
| ---------- | -------- | ----------------------- |
| Preprocess | Same     | Same                    |
| Compile    | Same     | Same                    |
| Assemble   | Same     | Same                    |
| Link       | Same     | Different memory layout |
| Run        | Local OS | On target hardware      |


