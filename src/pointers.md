# Pointers and addresses

This chapter covers taking addresses, dereferencing, pointer sizes, and how pointer arithmetic depends on the **pointed-to type**. It ends with **parallel arrays** of names and function pointers—a simplified picture of an **export table**.

## Address-of and dereference

```c
ULONG ulCourse = 670UL;
PULONG pCourse = &ulCourse;

printf("1-address of ulCourse 0x%p \n", (void*)&ulCourse);
printf("2-address of ulCourse 0x%p \n", (void*)pCourse);
printf("3-address of pCourse 0x%p \n", (void*)&pCourse);
printf("4-dereference pCourse 0x%08lx \n", (unsigned long)*pCourse);

*pCourse = 665;
```

`pCourse` holds the address of `ulCourse`; `*pCourse` reads or writes the `ULONG` at that address.

## Pointer size vs pointee size

```c
printf("pointers, always the same size : %zu, %zu, %zu, %zu \n",
    sizeof(PCHAR), sizeof(PULONG), sizeof(PWORD), sizeof(LPSTR));
printf("what they point to is NOT always the same size : %zu, %zu, %zu, %zu \n",
    sizeof(CHAR), sizeof(ULONG), sizeof(WORD), sizeof(CHAR));
```

On a given platform, **all data pointers** are typically the same width (e.g. 8 bytes on x64), but **`sizeof(*p)`** depends on the type `p` points to.

## Pointer arithmetic and `CHAR` vs `WORD` vs `ULONG`

The same underlying bytes (e.g. a string `"yoda"`) can be walked with different pointer types; **incrementing** advances by `sizeof(*p)`:

- `PCHAR` / `LPSTR`: +1 byte  
- `PWORD`: +2 bytes  
- `PULONG`: +4 bytes  

Then walk a C string byte-by-byte:

```c
PCHAR p = jediMaster;
while ('\0' != *p)
{
    printf("char: %c, address : 0x%p \n", *p, (void*)p);
    p++;
}
```

> [!WARNING]
> Casting a `PCHAR` to `PULONG` or `PWORD` and then incrementing is **unsafe** unless you know the buffer is large enough and aligned for that access. This exercise illustrates **sizes and arithmetic**, not production string code.

## Parallel arrays (names and function pointers)

Finish with arrays of **names** and **`PVOID`** (or typed) **function pointers**, then find an index with **`strcmp`**—the same logical shape as **names / ordinals / functions** in a PE export directory.

```c
/* Example shape only—adjust types and count for your rubric */
#define COUNTOF_NAMES 4
PCHAR AddressOfNames[COUNTOF_NAMES] = { "MyA", "MyB", "MyC", "MyD" };
PVOID AddressOfFunctions[COUNTOF_NAMES] = { /* &MyA, &MyB, ... */ };

for (ULONG i = 0UL; i < COUNTOF_NAMES; i++)
{
    if (0 == strcmp("MyC", AddressOfNames[i]))
    {
        printf("found at index %lu @ %p \n", i, AddressOfFunctions[i]);
    }
}
```

## Implement

1. In **your template**, implement the pointer demos, the **unsafe** multi-type walk only if your rubric requires it, and the **parallel-array** lookup with real stub functions.
2. **Build** and run.
3. **Commit** and **push**.
