The source code structure of a typical embedded project is shown as follows:
```css
project/
├── src/
│   ├── main.c
│   ├── uart.c
│   ├── gpio.c
│   └── isr.c
├── inc/
│   ├── uart.h
│   ├── gpio.h
│   └── device.h
├── startup/
│   └── startup.s
├── linker/
│   └── linker.ld
├── Makefile / CMakeLists.txt
└── build/
```
# Preprocessing
```bash
arm-none-eabi-gcc -E main.c
```
What happens:
- #include expansion
- #define macro replacement
- #if / #ifdef conditional compilation
- Comments removed
- Line markers (#line) added


Output: **.i file** for every .c file

# Compilation
```bash
arm-none-eabi-gcc -c main.c
```

What happens:
- C → Assembly → Object file (.o)
- No linking yet

## Parsing
- parse .i files
- syntax checking and report errors
- build an Abstract Syntax Tree (**AST**)

## Semantic analysis
- analyze AST and resolve identifiers
- build symbol tables
- check types
- check lvalues, conversions, qualifiers, etc.

## Code Generation
turns the analyzed C program into actual machine code or assembly
- instruction selection
```c
// c
a = b + c;
// ARM
ADD r0, r1, r2
```
- register allocation
- address calculation
- calling convention handling(parameter passing and return value handling of functions)
- control flow generation
- data layout(correct alignment, padding, ABI compliance)

Output: **.s files**

## Assembly
converts human-readable assembly instructions into **machine code** and produces **object files**.
```bash
arm-none-eabi-gcc -c startup.s
```
What happens:  
- Instruction encoding
```c
// ARM
ADD r0, r1, r2
// machine code
0xE0800002   (example)
```

- Symbol resolution (local)  
(1) Calculates offsets for local jumps  
(2) Marks unresolved external symbols for the linker
- Section creation

| Section   | Purpose          |
| :-------: | :--------------: |
| `.text`   | Code             |
| `.data`   | Initialized data |
| `.bss`    | Zero-init data   |
| `.rodata` | Constants        |
| `.heap`   | Heap             |
| `.stack`  | Stack            |

- Assembles startup code
- Creates reset vector & interrupt table


Output: **main.o, uart.o, gpio.o**

# Linking(very important!)
```bash
arm-none-eabi-gcc \
  main.o uart.o gpio.o startup.o \
  -T linker.ld \
  -o firmware.elf
```
What linker does:
- Combines all object files into one executable **.elf file**(debugging)
- Resolves symbols
- Places code & data into physical memory
- Uses **linker script**

**Role of the linker script (linker.ld)**  
Embedded systems have no OS to load programs.

The linker must:
- Decide exact addresses
- Map sections to Flash / RAM

Example:
```c
MEMORY
{
  FLASH (rx) : ORIGIN = 0x08000000, LENGTH = 512K
  RAM   (rwx): ORIGIN = 0x20000000, LENGTH = 128K
}
```

# Post-processing(optional)
- object copy
- produce .hex or .bin files for release<br>

# Startup code execution (runtime init)
After reset:  
(1) CPU jumps to reset vector  
(2) Startup code:  
- Sets stack pointer
- Copies `.data` from Flash → RAM
- Clears `.bss`

(3) Calls main()



## Embedded-specific differences from PC builds
| PC program              | Embedded firmware  |
| ----------------------- | ------------------ |
| OS loader               | Startup code       |
| Virtual memory          | Physical addresses |
| Dynamic linking         | Static linking     |
| malloc always available | Optional           |
| Filesystem              | Often none         |
