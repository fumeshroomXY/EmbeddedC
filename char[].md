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
```


# printf("%s");
`%s` requires a null‑terminated string:
```c
char str[] = "Hello";
printf("%s", str);  // ✅ Good

char str[6] = {'H','e','l','l','o','\0'};
printf("%s", str);  // ✅ Good

char str[10] = "Hello";
printf("%s", str);  // ✅ Good

char str[5] = {'H', 'e', 'l', 'l', 'o'};  //  NOT a C string
printf("%s", str);  // ❌ Not guaranteed, UB may occur
```

Better use **printf("%.*s", len, buf)**: prints exactly len bytes starting at buf.
- \* → precision is taken from an int argument `len`
- precision for %s → maximum number of characters to print
- **No need for `\0`**
```c
unsigned char buf[20] = {'H','e','l','l','o'};
int len = 5;
printf("received data = %.*s\n", len, buf);
```
