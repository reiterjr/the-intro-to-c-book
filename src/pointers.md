# Pointers and addresses

This chapter matches **`IntroToC/Part3`**: taking addresses, dereferencing, pointer sizes, and how pointer arithmetic depends on the **pointed-to type**.

## Address-of and dereference

```c
ULONG ulCourse = 670UL;
PULONG pCourse = &ulCourse;

printf("1-address of ulCourse 0x%p \n", &ulCourse);
printf("2-address of ulCourse 0x%p \n", pCourse);
printf("3-address of pCourse 0x%p \n", &pCourse);
printf("4-dereference pCourse 0x%08x \n", *pCourse);

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

The same underlying bytes (a string `"yoda"`) are walked with different pointer types; **incrementing** advances by `sizeof(*p)`:

- `PCHAR` / `LPSTR`: +1 byte  
- `PWORD`: +2 bytes  
- `PULONG`: +4 bytes  

Then a simple loop walks a C string byte-by-byte:

```c
PCHAR p = jediMaster;
while ('\0' != *p)
{
    printf("char: %c, address : 0x%p \n", *p, (void*)p);
    p++;
}
```

> [!warning]
> Casting a `PCHAR` to `PULONG` or `PWORD` and then incrementing is **unsafe** unless you know the buffer is large enough and aligned for that access. The lab uses it to illustrate **sizes and arithmetic**, not as a pattern for production string code.

## Parallel arrays (names and function pointers)

**`Part3`** ends with arrays of names and `PVOID` function pointers, then searches by name with `strcmp`—a simplified picture of how an **export table** pairs names with addresses.

**Project:** `IntroToC/Part3`.
