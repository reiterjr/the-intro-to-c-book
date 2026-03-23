# Unicode vs ANSI builds

This short chapter explains how **`UNICODE`** / **`_UNICODE`** affect which **`printf`** family you use—and how easy it is to mix wide and narrow APIs by mistake.

## One macro, two personalities

```c
#ifdef _UNICODE
#define print wprintf
#else
#define print printf
#endif

int main(void)
{
    print(L"this is a cool \n");  /* BUG if _UNICODE is not defined: printf + wide literal */
    return 0;
}
```

When **`_UNICODE`** is defined, **`print`** becomes **`wprintf`** and the wide literal **`L"..."`** is consistent. When it is **not** defined, **`print`** is **`printf`** but the string is still **wide**—that is a **bug**.

> [!WARNING]
> **Do not mix** wide string literals with **`printf`**, or narrow literals with **`wprintf`**, without explicit conversion. Pick **one** story for your template: **A** narrow (`CHAR`, `printf`, `*A` APIs) or **B** wide (`wchar_t`, `wprintf`, `*W` APIs, `UNICODE_STRING`).

## Why this matters

Later topics use **`CHAR`** and **`FindFirstFileA`** alongside **`UNICODE_STRING`** and wide system types. Your **project properties** and **`#ifdef UNICODE`** blocks should match—avoid “half Unicode” builds.

## Implement

1. Align **your template** with your instructor’s required configuration (ANSI vs Unicode).
2. Fix or remove any **`print`** macro that mixes families incorrectly.
3. **Build**, verify, **commit**, and **push**.
