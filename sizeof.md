# `sizeof` string in C
## What `'\0'` actually is
`'\0'` is not a normal character.

It is the **null terminator**, used by C to mark the end of a string.

So this loop:
```c
char alphabet[] = "abcdefghijklmnopqrstuvwxyz";
char input = getchar();
for(i = 0; i < sizeof(alphabet); i++){
  if(input == alphabet[i])...
}
```
will compare `'\0'` once, but it will never match.

Better:
```c
for(i = 0; i < sizeof(alphabet) - 1; i++)
```

## When the original code behaved strangely
```c
for(i = 0; i <= sizeof(alphabet); i++)
```

This is wrong because:
- `sizeof(alphabet)` = 27
- Valid indexes = 0–26
- You accessed index 27, which is **out of bounds**

Out-of-bounds access causes **undefined behavior**, meaning:
- random values
- weird output
- **“i goes to 110”**
- program crashes
- or nothing happens

This is why your program behaved strangely.

## Users cannot input `'\0'`
A user cannot type `'\0'` from a keyboard.

- It is not a printable character

- It is not a key on the keyboard

- Input functions like `getchar()`, `scanf("%c")`, do not return '\0' as user input

The only way to get `'\0'` is:

- reading from a binary file
- manually assigning it in code
- receiving it from a network packet

But **not from keyboard input**.

Since the user will never input `'\0'`, checking it is pointless.
