# Strings, `strlen`, and memory helpers

This chapter covers C strings, `<string.h>`, and simple **macros** for flag tests that reappear when you work with PE flags and file attributes later.

## `strlen` vs `sizeof`

- **`strlen`** counts characters **until** the null terminator; it does **not** include `'\0'`.
- **`sizeof`** on a fixed array includes the **whole array**, including the null byte if the initializer provided one.

Example:

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

`strcmp` returns **0** when strings are equal:

```c
printf("is k32 == kbase? -> %i : %s \n", strcmp(k32, kbase),
    strcmp(k32, kbase) == 0 ? "yes" : "no");
```

`atoi` converts a decimal string to `int` (use with care in production; no strong error reporting).

## Macros: flags and page constants

Define helpers such as:

```c
#define FlagOn(Flags, TheFlag)   (((Flags) & (TheFlag)))
#define SetFlag(Flags, TheFlag)  ((Flags) |= (TheFlag))
#define ClearFlag(Flags, TheFlag) ((Flags) &= ~(TheFlag))
```

…and constants like `PAGE_EXECUTE_READ` when your chapter or rubric references executable memory flags. You will reuse **flag-testing** style when parsing PE **Characteristics** and **`WIN32_FIND_DATA`** attributes.

## Implement

1. Add a **`.c`** file (or use your template’s `main`) that exercises **`strlen`**, **`sizeof`**, **`memcpy`**, truncation, **`strcmp`**, and **`atoi`** as shown (or as your instructor specifies).
2. Add any **macros** your rubric requires to a **`.h`** file and include it where needed.
3. **Build**, run, and confirm output.
4. **Commit** and **push**.
