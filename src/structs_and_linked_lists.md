# Structs, `UNICODE_STRING`, and doubly linked lists

This chapter covers Windows-style **structs**, **designated initializers**, allocation from the **process heap**, and linking records with **`LIST_ENTRY`** (doubly linked list), the same abstraction the loader uses for modules.

## Struct types to model

Define types similar to kernel patterns (names may vary; match your rubric):

- **`UNICODE_STRING`** — `Length`, `MaximumLength`, `Buffer`.
- **`STRING_EX`** (optional) — narrow or wide buffer depending on whether you compile with **`UNICODE`**.
- A record (e.g. **`PROCESS_INFO_EX`**) that embeds a **`LIST_ENTRY`** (e.g. `ProcessInfoLinks`) plus a module or process **name**.

## Initializing structs

Examples:

```c
STRING_EX Name2 = { 0 };
RtlSecureZeroMemory(&Name3, sizeof(STRING_EX));

STRING_EX Name4 = { .Buffer = s, .MaximumLength = sizeof(s), .Length = sizeof(s) - sizeof((s)[0]) };
```

Use an **`RTL_CONSTANT_STRING`-style macro** for string literals if your instructor provides or asks you to write one.

## Heap allocation and list operations

Allocate a **list head** with **`HeapAlloc`** / **`GetProcessHeap`**, check for **`NULL`**, then:

```c
InitializeListHead(TempHeadList);
bIsEmpty = IsListEmpty(TempHeadList);
```

Allocate a **node struct**, fill fields (including a **`UNICODE_STRING`** from `RTL_CONSTANT_STRING` or manual setup), then insert:

```c
InsertTailList(TempHeadList, &(pProcInfo->ProcessInfoLinks));
```

## List helper routines

You need **`InitializeListHead`**, **`IsListEmpty`**, **`InsertTailList`**, **`RemoveEntryList`**, and related routines—the same operations the Windows loader uses for module lists. Your **template** may already ship **`ListHelpers.h`**; if not, implement these **FORCEINLINE**-style functions in your own header (match the classic doubly linked list algorithms).

> [!NOTE]
> Printing a **`UNICODE_STRING`** with **`printf`** may require a vendor-specific format (e.g. `%wZ`) or manual loops over **`Buffer`**. If your build complains, print **`Length`** / **`Buffer`** in a simple loop for debugging.

## Implement

1. Add the **struct** definitions and **list helpers** to headers/sources in **your template**.
2. In **`main`**, allocate head + at least one node, **insert**, and verify **`IsListEmpty`** toggles as expected.
3. **Build**, run, **commit**, and **push**.
