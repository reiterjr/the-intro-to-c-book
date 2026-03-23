# Structs, `UNICODE_STRING`, and doubly linked lists

This chapter follows **`IntroToC/Part4`**: defining Windows-style structs, using designated initializers, allocating from the process heap, and linking nodes with **`LIST_ENTRY`**.

## Struct types in this lab

**`Part4/main.c`** defines (or typedefs) shapes similar to kernel patterns:

- **`UNICODE_STRING`** — `Length`, `MaximumLength`, `Buffer`.
- **`STRING_EX`** — narrow or wide buffer depending on `UNICODE`.
- **`PROCESS_INFO_EX`** — example process record embedding a **`LIST_ENTRY`** (`ProcessInfoLinks`) and a **`UNICODE_STRING` name**.

## Initializing structs

Examples include zeroing with `{ 0 }`, `RtlSecureZeroMemory`, designated initializers in C:

```c
STRING_EX Name4 = { .Buffer = s, .MaximumLength = sizeof(s), .Length = sizeof(s) - sizeof((s)[0]) };
```

and the `RTL_CONSTANT_STRING`-style macro for string literals.

## Heap allocation and list operations

The lab allocates a **`LIST_ENTRY`** head with **`HeapAlloc`** / **`GetProcessHeap`**, checks for failure, then:

```c
InitializeListHead(TempHeadList);
bIsEmpty = IsListEmpty(TempHeadList);
```

A **`PROCESS_INFO_EX`** is allocated, partially filled (including a **`UNICODE_STRING`** built with `RTL_CONSTANT_STRING`), and inserted:

```c
InsertTailList(TempHeadList, &(pProcInfo->ProcessInfoLinks));
```

## `ListHelpers.h`

**`Part4/ListHelpers.h`** provides **`InitializeListHead`**, **`IsListEmpty`**, **`InsertTailList`**, **`RemoveEntryList`**, etc.—the same logical operations used by the Windows loader’s module lists (you will walk those lists in later parts).

> [!note]
> **`DumpStruct`** in the project uses a `printf` format that depends on your CRT/SDK setup (`%wZ`-style extension). If your build warns or fails, compare your toolset documentation or temporarily print `Buffer` manually while learning the list mechanics.

**Project:** `IntroToC/Part4`.
