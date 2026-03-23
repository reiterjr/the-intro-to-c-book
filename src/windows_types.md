# Windows types, `main`, and `printf`

This chapter covers a minimal Windows C program, Win32 typedefs (`INT`, `UINT`, `DWORD`, …), and printing values with the correct **`printf`** format specifiers. Your **Classroom template** may already contain an empty or stub `main`—you will replace or extend it using the patterns here.

> [!NOTE]
> **No separate “lab solution” tree**—everything you need to start is in **this chapter** plus whatever stubs your template provides.

## The entry point: `main`

Every C program needs a place to start. On Windows, when you build a typical **console** application and link the **C runtime library (CRT)**, the executable does **not** begin executing your `main` immediately: the CRT’s **startup code** runs first (stack, globals, security cookie work, and so on). When that setup is done, the CRT **calls your `main`**. That is why you write a function named **`main`**: it is the **contract** between your code and the CRT for this kind of app.

### Function signature

The **signature** is the function’s **return type**, **name**, and **parameter list**—taken together, they tell the compiler how `main` can be called and what it returns.

Common forms you will see:

```c
int main(void);

int main(int argc, char *argv[]);
```

In Windows headers, **`INT`** is a typedef for a signed integer type (typically the same width as **`int`**), and **`VOID`** means “no parameters,” so you will often see:

```c
INT main(VOID)
{
    /* ... */
    return 0;
}
```

Your **template** or rubric may use `int` or `INT`; both describe the same idea when `INT` is `typedef`’d to match `int`.

### Function body

The **body** is the block in `{ ... }` after the signature. That is where you:

- Declare **variables**
- Call **functions** (e.g. `printf`)
- Use **control flow** (`if`, `while`, …)
- **`return`** a value to the caller

For `main`, the return value is passed back through the CRT to the operating system: by convention, **`0`** means success; **non-zero** often signals an error (your course may standardize on `return 0` or `return ERROR_SUCCESS` from `Windows.h`).

### Why `main` (and not something arbitrary)?

The **linker + CRT** are built to look for a symbol with a specific name for the C **console** entry path—traditionally **`main`** (or **`wmain`** for wide-character argv). If you rename it to `start_here` without also changing startup objects and linker settings, the link step will fail or you will get the wrong entry point. **`WinMain`** is a **different** story: that is the usual entry for **Windows subsystem** GUI programs, with the CRT calling **`WinMain`** instead of **`main`**. For this course’s console-style labs, **`main`** is the right name.

---

## Variables that hold numbers

Before you print or combine values, you store them in **variables**: a name, a **type**, and (usually) an **initial value**.

On Windows, **`Windows.h`** (and related headers) provide typedefs so the same names appear in sample code and in system APIs:

| Typedef (typical use) | Role |
|----------------------|------|
| **`INT`** | Signed integer (often 32-bit); good for values that can be negative. |
| **`UINT`** | Unsigned integer; counts and bit patterns that should not be negative. |
| **`LONG`**, **`ULONG`** | Signed / unsigned long-width integers. |
| **`DWORD`** | Unsigned 32-bit type (“double word”); very common in Win32 APIs. |

Example declarations with **initializers**:

```c
INT   courseNumber = 670;
UINT  flags        = 0U;
DWORD mask         = 0x0000FFFFUL;
```

The **type** tells the compiler how much storage to use and **how to interpret the bits** (especially important for **signed** vs **unsigned**). The **value** after `=` sets the starting contents; you can change them later with assignment (`=`, `+=`, …).

### `sizeof`: how big is this type or variable?

C provides the **`sizeof`** operator. It yields the **size in bytes** of a type or of an expression’s type. You use it when the **correct amount of memory** matters—buffers, clearing structs, or matching what an API expects—instead of hard-coding numbers like `4` that might be wrong on another platform or for another type.

**On a type** (parentheses required around the type name):

```c
size_t n = sizeof(UINT);   /* how many bytes one UINT occupies */
```

**On a variable or expression** (parentheses around the variable are optional but common):

```c
DWORD mask = 0xFF00FF00UL;
printf("mask uses %zu bytes\n", sizeof mask);
```

`sizeof` is evaluated at **compile time** for normal types (not counting run-time variable **values**—only the **type**). The result has type **`size_t`**; use **`%zu`** with `printf` to print it portably.

**Why bother?**

- **Clarity:** `sizeof(buffer)` documents “all of this object,” not a magic constant.
- **Safety:** If you change a variable’s type later, `sizeof` updates automatically.
- **APIs:** Many functions want a **size in bytes**; passing `sizeof` avoids off-by-one errors when paired with the right object.

> [!TIP]
> `sizeof` does **not** tell you the **length of a string** in characters—use **`strlen`** for that. `sizeof(char_array)` includes the **`'\0'`** if the array was declared with fixed size; `strlen` does not.

---

## How many bytes? Overflow and wrapping

Exact **byte widths** are **implementation-defined**, but on **Visual Studio / MSVC** for typical **Windows x64** console programs, the Win32 typedefs you use early in this course are almost always **32-bit (4 bytes)** for integers like **`INT`**, **`UINT`**, **`DWORD`**, **`LONG`**, and **`ULONG`**. Wider types exist for 64-bit values (e.g. **`LONG64`**, **`ULONG64`**, **`DWORD64`**) and are **8 bytes**.

| Idea | Typical size (MSVC, x64 labs in this course) |
|------|-----------------------------------------------|
| **`CHAR`** | 1 byte |
| **`SHORT` / `USHORT`** | 2 bytes |
| **`INT` / `UINT` / `DWORD` / `LONG` / `ULONG`** | 4 bytes |
| **`LONG64` / `ULONG64` / `DWORD64`** | 8 bytes |
| **Pointers** (`PCHAR`, `void *`, …) | **8 bytes** on **x64**, 4 on x86 |

To **verify** on your machine, print them once in a scratch `main`:

```c
printf("sizeof(INT)=%zu, sizeof(DWORD)=%zu, sizeof(void*)=%zu\n",
    sizeof(INT), sizeof(DWORD), sizeof(void *));
```

### What if the value does not fit?

Each unsigned integer type can only hold **`0 .. 2^(8×N) − 1`** for **`N`** bytes. If you **assign** or **compute** a value that is **too large**:

- **Unsigned** types: the C standard defines **wrapping** (reduction **modulo** `2^(8×N)`). The value “folds” and you keep only the low bits—silently. Example: putting **`300`** into an **`unsigned char`** (often 1 byte) does not store 300; it stores **`300 % 256`**.

- **Signed** types: **overflow in arithmetic** (e.g. `INT_MAX + 1` in a pure `int` expression) is **undefined behavior** in C—you cannot rely on it wrapping like two’s-complement hardware might. Compilers may assume it never happens and optimize in surprising ways.

**Practical takeaway:** pick a type **wide enough** for the range you need (`DWORD` vs `DWORD64`, etc.), check sizes with **`sizeof`** when writing to buffers, and be careful mixing **signed** and **unsigned** in expressions (see the next section).

---

## Signed vs unsigned—and mixing them

**Signed** types can represent negative numbers; **unsigned** types treat the same storage as **non-negative** only. If you **assign** or **arithmetic-mix** signed and unsigned values, C’s **usual arithmetic conversions** can **widen** or **reinterpret** values in ways that surprise you. Always know which type each variable has before you combine them.

Here is a small program that **mixes** `INT` and `UINT` and then prints the results. An `INT` is pushed past its maximum positive value by adding a `UINT`, and a `UINT` is decremented:

```c
INT mix = 0x7FFFFFFF;
UINT match = 1U;
mix += match;
match -= 2;

printf("mix:   (hex)[0x%x] (signed)[%i] (unsigned)[%u] \n", mix, mix, mix);
printf("match: (hex)[0x%x] (signed)[%i] (unsigned)[%u] \n", match, match, match);
```

The **same bits** in a variable can be printed three ways: **`%x`** (hex), **`%i`** (signed decimal), **`%u`** (unsigned decimal). The **format specifier** tells `printf` how to **interpret** the value you pass—not the other way around. Passing the wrong combination of type and format is undefined behavior or misleading output. That is why choosing the **right type** (`INT`, `UINT`, `DWORD`, …) and the **matching format** matters.

---

## More arithmetic: compound assignment and bit shifts

Extend **`main`** with:

- More variables of types like **`INT`**, **`UINT`**, **`DWORD`** with given initial values (including hex constants and expressions like `1 << 12`).
- Additional **`printf`** lines with correct specifiers for hex vs decimal.
- **Compound assignment**, e.g. updating an `INT` with a `UINT` (`mix += something`).

Bit shift examples:

```c
DWORD forty96 = 1 << 12;  /* 4096 */
DWORD ten24   = 1 << 10;  /* 1024 */
```

## `printf` format cheat sheet (MSVC / Windows)

Use this table while you choose specifiers (see also [Microsoft `printf` format specification](https://learn.microsoft.com/en-us/cpp/c-runtime-library/format-specification-syntax-printf-and-wprintf-functions))):

```c
/*
 * Common conversions:
 *   %d or %i   signed decimal
 *   %u         unsigned decimal
 *   %o         unsigned octal
 *   %x / %X    unsigned hex (lower/upper)
 *
 * Size / width hints (examples):
 *   %lld       long long
 *   %I32d      32-bit int (MSVC)
 *   %I64d      64-bit int (MSVC)
 *   %zu        size_t (C99; use for strlen result, etc.)
 */
```

> [!TIP]
> If output looks wrong, check **both** the **variable type** and the **format specifier**. Mismatches (e.g. printing a `DWORD` with the wrong size or signedness) cause confusing output or undefined behavior.

## Implement

1. In **your Classroom template**, ensure **`main`** has the correct **signature** and **body** for a console CRT app, then add the **numeric variables**, the **signed/unsigned** demo, and any **bit-shift** / **`printf`** work your rubric requires.
2. **Build** (correct platform: usually **x64** for later chapters, but this chapter may work in either—follow your template).
3. Run and verify the printed hex/signed/unsigned lines match your expectations.
4. **Commit** and **push** when this chapter’s goals are met.
