# Strings, `strlen`, and memory helpers

This chapter follows **`IntroToC/Part2`**: C strings, `string.h`, and a few macros used throughout the course.

## `strlen` vs `sizeof`

- **`strlen`** counts characters **until** the null terminator; it does **not** include `'\0'`.
- **`sizeof`** on a fixed array includes the **whole array**, including the null byte if the initializer provided one.

From **`Part2/main.c`**:

```c
CHAR bad[10] = {0};
SIZE_T badLen = strlen(bad);   /* 0 */

memcpy(bad, "bad", strlen("bad") + 1);  /* copy includes '\0' */
```

Truncating a string in place:

```c
CHAR cmd[] = "cmd.exe";
cmd[3] = '\0';   /* now prints as "cmd" */
```

## Array vs pointer to string literal

```c
CHAR cmd[] = "cmd.exe";      /* array; sizeof(cmd) is size of array */
CHAR* command = "cmd.exe";   /* pointer; sizeof(command) is pointer size */
```

## `strcmp` and `atoi`

`strcmp` returns **0** when strings are equal. The project prints comparisons with a ternary for readability:

```c
printf("is k32 == kbase? -> %i : %s \n", strcmp(k32, kbase),
    strcmp(k32, kbase) == 0 ? "yes" : "no");
```

`atoi` converts a decimal string to `int` (use with care in production; no strong error reporting).

## Macros: flags and page constants

**`Part2`** defines macros such as `FlagOn`, `SetFlag`, `ClearFlag`, and constants like `PAGE_EXECUTE_READ`. You will see the same **flag-testing** style again when working with PE characteristics and file attributes in later parts.

**Project:** `IntroToC/Part2`.
