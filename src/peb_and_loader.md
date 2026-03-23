# The PEB, loader data, and module lists

This chapter covers reading the **TEB/PEB** on **x64**, reaching **`PEB->Ldr`**, and walking **`InMemoryOrderModuleList`** with **`CONTAINING_RECORD`**.

> [!WARNING]
> This topic **requires x64**. Use **`__readgsqword(FIELD_OFFSET(TEB, ProcessEnvironmentBlock))`** and reject **x86** builds with `#error` if your rubric says so. Always select an **x64** configuration in Visual Studio.

## Get a pointer to the PEB

```c
PPEB PebPtr = (PPEB)__readgsqword(FIELD_OFFSET(TEB, ProcessEnvironmentBlock));
PPEB_LDR_DATA LdrDataPtr = (PPEB_LDR_DATA)PebPtr->Ldr;
```

You will need **`winternl.h`** / **`ntddk.h`-style** types your template exposes (`TEB`, `PEB`, `PEB_LDR_DATA`, `LDR_DATA_TABLE_ENTRY`, etc.)—often via a single **NT headers** include your instructor supplies.

## Walk `InMemoryOrderModuleList`

The list is circular; stop when **`CurrentModule == HeadList`**:

```c
PLIST_ENTRY HeadList = &(LdrDataPtr->InMemoryOrderModuleList);
PLIST_ENTRY CurrentModule = HeadList->Flink;

while (CurrentModule != HeadList)
{
    PLDR_DATA_TABLE_ENTRY LdrDataEntry =
        CONTAINING_RECORD(CurrentModule, LDR_DATA_TABLE_ENTRY, InMemoryOrderLinks);
    /* e.g. wprintf(L"%wZ @ %p\n", LdrDataEntry->BaseDllName, LdrDataEntry->DllBase); */
    CurrentModule = CurrentModule->Flink;
}
```

`CONTAINING_RECORD` converts a pointer to an embedded **`LIST_ENTRY`** into a pointer to the **containing** **`LDR_DATA_TABLE_ENTRY`**.

## Optional: hashing and the loader hash table

Advanced follow-on (if assigned): implement **`LdrpHashUnicodeString`-style** hashing, **`LdrHashEntry`**, and logic that takes the **loader lock**, walks **initialization-order** modules, and locates the **hash bucket** array. Treat this as a second milestone after the basic **InMemoryOrder** walk works.

> [!TIP]
> Loader data can change under you—other threads load DLLs. Any serious walk should use **`LoaderLock`** (**`EnterCriticalSection` / `LeaveCriticalSection`**) the way your rubric describes.

## Implement

1. In **your template**, implement **PEB → Ldr → InMemoryOrder** walk and print each module’s **base** and **name** (as your instructor specifies).
2. Add the **optional** hash-table exploration only if assigned.
3. **Build x64**, run, **commit**, and **push**.
