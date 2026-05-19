# Null Pointer
A null pointer is a pointer that points to **no valid object or function**.
- NULL is a macro (usually 0)
- Indicates “points to nothing”

```c
int *p = NULL;
*p = 10;   // ❌ undefined behavior (crash, hard fault)
```

**Why null pointers exist**
- To initialize pointers safely
- To test whether a pointer is valid
- To represent “no object”
```c
if (p == NULL) {  //Important: Always compare to NULL, not 0
    // handle error
}
```

# Void Pointer
A void pointer is a generic pointer that can hold the address of **any data type**.
```c
void *vp;
int x = 10;

vp = &x;
```

**Cannot dereference a void * directly**
```c
*vp = 10;    // ❌ illegal
*(int *)vp = 10;   // ✅ must cast it first
```


**Why `void*` exists**
- Generic programming (malloc, memcpy)
- Passing unknown types
- Reusable APIs
```c
void *malloc(size_t size);
```

