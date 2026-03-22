# Windows types, `main`, and `printf`

This chapter aligns with **`IntroToC/Intro`** and **`IntroToC/lab1`**: building a minimal Windows C program, using Win32 typedefs, and printing values with the correct `printf` format specifiers.

> [!note]
> **`lab1` is your worksheet.** The `lab1/main.c` file is mostly TODO comments. Use this chapter and `Intro/main.c` as references while you fill in `lab1`.

## Signed vs unsigned and one `printf` pitfall

In **`Intro/main.c`**, an `INT` is pushed past its maximum positive value by adding a `UINT`, and another `UINT` is decremented:

```c
INT mix = 0x7FFFFFFF;
UINT match = 1U;
mix += match;
match -= 2;

printf("mix:   (hex)[0x%x] (signed)[%i] (unsigned)[%u] \n", mix, mix, mix);
printf("match: (hex)[0x%x] (signed)[%i] (unsigned)[%u] \n", match, match, match);
```

The **same bits** are printed three ways: `%x` / `%i` / `%u` interpret the value differently. This is why choosing the **right type** (`INT`, `UINT`, `DWORD`, …) and the **matching format** matters.

## `main`, arithmetic, and bit shifts

**`lab1`** expects you to write `main` with:

- Variables of types like **`INT`**, **`UINT`**, **`DWORD`** with given initial values (including hex constants and expressions like `1 << 12`).
- **`printf`** lines with correct specifiers for hex vs decimal.
- Compound assignment, e.g. updating an `INT` with a `UINT`.

Bit shift example (from the starter comments in `lab1`):

```c
DWORD forty96 = 1 << 12;  /* 4096 */
DWORD ten24   = 1 << 10;  /* 1024 */
```

## Format string cheat sheet (`notes.h`)

**`Intro/notes.h`** documents common `printf` types and size prefixes (`%d`, `%u`, `%x`, `%I32`, `%I64`, `%ll`, etc.). Keep that header open while you work on **`lab1`**.

> [!tip]
> If output looks wrong, check **both** the **variable type** and the **format specifier**. Mismatches (e.g. printing a `DWORD` with the wrong size or signedness) cause confusing output or undefined behavior.

**Projects:** `IntroToC/Intro`, `IntroToC/lab1`.
