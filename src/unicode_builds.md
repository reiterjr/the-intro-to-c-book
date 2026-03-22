# Unicode vs ANSI builds

This short chapter matches **`IntroToC/Part11`**: how the **`UNICODE`** / **`_UNICODE`** macros change which **`printf`** family you use.

## One macro, two personalities

```c
#ifdef _UNICODE
#define print wprintf
#else
#define print printf
#endif

int main()
{
    print(L"this is a cool \n");
    return 0;
}
```

When **`_UNICODE`** is defined, **`print`** becomes **`wprintf`** and the wide literal **`L"..."`** is consistent. When it is **not** defined, **`print`** is **`printf`**, but the **`L` prefix** on the string is still a **wide** string—so the narrow and wide worlds are accidentally mixed in this stub.

> [!warning]
> **Do not mix** wide string literals with **`printf`**, or narrow literals with **`wprintf`**, without explicit conversion. Enable **`UNICODE`** consistently in your project settings and includes, or standardize on **`printf` + UTF-8** / **`wprintf` + wide** deliberately.

## Why this matters in the course

Earlier parts use **`CHAR`**, **`printf`**, **`FindFirstFileA`**, and wide APIs like **`wprintf`** / **`UNICODE_STRING`** side by side for teaching. For your own code, pick a strategy:

- **A (ANSI / UTF-8 narrow):** `char`, `printf`, `CreateFileA`, etc.  
- **B (Unicode wide):** `wchar_t`, `wprintf`, `CreateFileW`, `UNICODE_STRING`, etc.

**Project:** `IntroToC/Part11`.
