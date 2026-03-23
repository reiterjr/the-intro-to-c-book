# Locating modules by name or hash and reading PE headers

This chapter covers finding a loaded module’s **base address** from the **PEB module list**, then parsing **PE** structures in memory (DOS header, NT headers, optional header, export directory).

## `FindSystemModule`-style API

Design a function (name it as your rubric says) that accepts optional **`UNICODE_STRING*`** and/or **`ULONG*`** hash:

- If **name is NULL**, a **hash must** be supplied; otherwise return **`ERROR_INVALID_PARAMETER`** (or equivalent).
- Set an internal **`bHashOnly`** flag and call into **`GetModuleBase`**.

## `GetModuleBase` and the loader lock

**`GetModuleBase`** should:

1. Resolve the **PEB** (e.g. **`GetPebPtr`** using `__readgsqword` / TEB).
2. **`EnterCriticalSection(PebPtr->LoaderLock)`** before reading loader lists.
3. Walk **`InMemoryOrderModuleList`**, comparing either:
   - **`BaseNameHashValue`** to the supplied hash (`RtlCompareMemory`), or  
   - **`_wcsicmp`** on wide name buffers when matching by name.
4. **`LeaveCriticalSection`** on **every** path.

> [!IMPORTANT]
> Always release the loader lock on **all** exit paths. Early **`return`s** during debugging must not skip **`LeaveCriticalSection`** or you can **deadlock** the process.

## PE parsing overview (in-memory)

After you have **`ModuleBase`**:

- Validate **`IMAGE_DOS_SIGNATURE`** at the base (**DOS header**).
- Use **`e_lfanew`** to locate the **NT headers**.
- Read the **file header** and **optional header** (32 vs 64 depends on **Machine**).
- Locate the **export directory** via **`IMAGE_DIRECTORY_ENTRY_EXPORT`** and convert **RVAs** to **VAs** with a macro such as:

```c
#define RVA2VA(Type, Base, Rva) ((Type)((DWORD_PTR)(Base) + (DWORD_PTR)(Rva)))
```

Add **`DumpDosHeader`**, **`DumpFileHeader`**, **`DumpExports`** (or similar) as separate functions that print fields you care about. Link **`imagehlp.lib`** if you use **ImageHlp** helpers (**`ImageRvaToVa`**, etc.).

## Second pass (optional reinforcement)

If your course assigns a **second milestone** with the same APIs (e.g. hash-only lookup vs name lookup), treat it as **practice**—same lock rules, same PE steps—rather than a different codebase.

## Implement

1. Implement **lookup by name** and/or **by hash** per rubric.
2. Parse and print **DOS** + **file** headers (and **exports** if required).
3. **Build x64**, test against a known module (e.g. `KERNELBASE.dll`), **commit**, and **push**.
