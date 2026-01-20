`const` behaves very differently on MCUs than on PCs. 

# Where are `const` objects stored in Embedded C?

`const` global data is usually stored in **Flash (ROM)**  
Non-const data is stored in **RAM**  

Why?
- Flash is non-volatile
- `const` means **read-only**
- Linker places read-only sections in Flash

**Typical memory layout (MCU)**
```markdown
FLASH / ROM
--------------------
.text      → code
.rodata    → const globals, string literals

RAM
--------------------
.data      → initialized globals
.bss       → uninitialized globals
.stack
.heap
```
## `const` local variable
```c
void foo(void) {
    const int x = 10;
}
```
`x` is usually stored in **RAM (stack)** because it has **automatic storage duration**.

# `const` pointer vs `const` variable (THIS IS KEY)
Rule to remember:  
`const` applies to what is immediately **on its left**.  
If nothing is on the left, it applies to the right.



## Case 1: `const int *p`
```c
const int *p; // == int const *p;
*p = 5;   // ❌
p = &x;   // ✅
```
- ❌ Cannot modify `*p`
- ✅ Can change `p`
- Data is read-only

## Case 2: `int * const p`
```c
int * const p = &x;
*p = 5;   // ✅
p = &y;   // ❌
```
- ✅ Can modify `*p`
- ❌ Cannot change `p`
- Pointer address is fixed

## Where are pointers themselves stored?
The pointer variable is stored **wherever it is declared**:
| Declaration | Pointer stored in |
| ----------- | ----------------- |
| Global      | RAM (`.data`)     |
| Local       | Stack             |
| `static`    | RAM               |

`const` does **not** move the pointer to Flash — only what it points to may be in Flash.



**DO NOT** change the value of `const int` by using a pointer. 
```c
const int x = 10;
int *p = (int *)&x;   // cast away const
*p = 20;              // ❌ Undefined Behavior
```
