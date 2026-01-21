In C, a string is defined as a sequence of characters **terminated by a null character `\0`**

# Correct ways to initialize a C string
```c
char str[] = "Hello";  // recommended, 'H' 'e' 'l' 'l' 'o' '\0'

char str[6] = {'H','e','l','l','o','\0'};

char str[10] = "Hello"; // Remaining bytes are set to '\0'. 'H'  'e'  'l'  'l'  'o' '\0' '\0' '\0' '\0' '\0'
str[5] = 'm';  // can modify its elements as long as NOT exceed the size
str[6] = 'a';
str[7] = 'n';
str[8] = '\0';

char str[5] = {'H', 'e', 'l', 'l', 'o'};  // ❌ NOT a C string


