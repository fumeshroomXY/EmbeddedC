# ASCII
ASCII is a standard that assigns numbers (0–127) to characters.  
C does not have a special “character type” internally — characters are just **small integers**.
```c
char c = 'A';
int x = c;   // x == 65
```
## `char` is an integer type
- Size is 1 byte
- Can be signed or unsigned (implementation-defined!)
```c
char c = 'A';
printf("%d\n", c);  // prints 65 (usually)

// On some systems:
char c = 200;  // may be negative if char is signed
```

## Common ASCII conversions
```c
// Character → digit
char c = '7';
int n = c - '0';   // 7

// Digit → character
int n = 5;
char c = n + '0';  // '5'

// Uppercase ↔ lowercase (ASCII only!)
char lower = 'a';
char upper = lower - ('a' - 'A');  // 'A'

// Better practice
#include <ctype.h>
char upper = toupper(lower);
```

# Typecasting between `char` and `int`
## Implicit casting (automatic)
```c
char c = 'a';
int i = c;   // promoted to int (ASCII value)
```

## Explicit casting
```c
int i = 65;
char c = (char)i;   // 'A'
```

## Typecasting rules
- Casting can lose data
```c
int x = 300;
char c = (char)x;   // overflow / wrap-around
```
Result depends on:  
(1) `CHAR_BIT`  
(2) Signedness of char

# Signed vs Unsigned char
Whether plain `char` is signed or unsigned is implementation-defined.**You must not assume** which one it is.  

Assuming 8-bit bytes (most systems):
| Type | Range | 
|:------:|:-----:|
| signed char | −128 to 127 |
| unsigned char | 0 to 255 |
| char | either of the above |

Check at runtime:
```c
#include <limits.h>

printf("%d\n", CHAR_MIN);
printf("%d\n", CHAR_MAX);
```

## Why does this matter?
- overflow surprise
```c
char c = 200;
printf("%d\n", c);
```
If char is signed -> negative value  
If unsigned -> 200
- comparisons break
```c
char c = 0xFF;
if (c == 255) {
    /* maybe never true */
}
```
- character classification bugs
```c
#include <ctype.h>

char c = 0xE9;   // é in extended ASCII
if (isalpha(c)) { }   // UB if c < 0
if(isalpha((unsigned char)c)) { }  // Correct.
```
- integer promotion(important!)
```c
char c = 0xFF;
int x = c;   // sign-extended if signed char
```
Signed char -> x == -1  
Unsigned char -> x == 255

## When to use each type
- Use char for **text / characters**
```c
char letter = 'A';
```
- Use unsigned char for raw bytes, buffers or safe use with **<ctype.h>**
```c
unsigned char byte = 0xFF;
```
- Rarely use signed char
