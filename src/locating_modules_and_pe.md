# Locating modules by name or hash and reading PE headers

This chapter centers on **`IntroToC/Part6`** (and the closely related **`Part7`**, which exercises the same APIs). The goal is to find a loaded module’s base address from the **PEB module list**, then interpret **PE** structures in memory.

## `FindSystemModule` parameters

**`FindSystemModule`** accepts optional **`UNICODE_STRING`** and/or **`ULONG` hash** pointers:

- If **name is NULL**, a **hash must** be supplied (`ERROR_INVALID_PARAMETER` otherwise).
- The implementation sets **`bHashOnly`** and calls **`GetModuleBase`**.

## `GetModuleBase` and the loader lock

**`GetModuleBase`**:

1. Calls **`GetPebPtr`** (via `__readgsqword` / TEB) to get the PEB.
2. **`EnterCriticalSection(PebPtr->LoaderLock)`** before reading loader lists.
3. Walks **`InMemoryOrderModuleList`**, comparing either:
   - **`BaseNameHashValue`** to the supplied hash (`RtlCompareMemory`), or  
   - **`_wcsicmp`** on full buffer strings when matching by name.
4. **`LeaveCriticalSection`** when done.

> [!important]
> Always release the loader lock on **all** exit paths. If you add early `return`s while debugging, ensure you don’t accidentally skip **`LeaveCriticalSection`**—that can **deadlock** the process.

## PE parsing overview (in-memory)

After a base address is found, the code:

- Validates **`IMAGE_DOS_SIGNATURE`** at the base (**DOS header**).
- Uses **`e_lfanew`** to locate the **NT headers**.
- Reads the **file header** and **optional header** (32 vs 64 depends on **machine** type).
- Locates the **export directory** via **`IMAGE_DIRECTORY_ENTRY_EXPORT`** and **`RVA2VA`** style macros.

**`DumpExports`**, **`DumpDosHeader`**, **`DumpFileHeader`**, etc., print pieces of this structure. Linking **`imagehlp.lib`** and including **ImageHlp** supports helpers like **`ImageRvaToVa`** / symbol utilities where used.

## Part6 vs Part7

**`Part7`** largely repeats the **`FindSystemModule`** / hash workflow with small variations—use it to confirm you understand the flow without treating the two as different topics.

**Projects:** `IntroToC/Part6`, `IntroToC/Part7`.
