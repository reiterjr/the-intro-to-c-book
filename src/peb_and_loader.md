# The PEB, loader data, and module lists

This chapter follows **`IntroToC/Part5`**: reading the **TEB/PEB** on **x64**, reaching **`PEB->Ldr`**, and walking **`InMemoryOrderModuleList`** with **`CONTAINING_RECORD`**.

> [!warning]
> This project **requires x64**. It uses **`__readgsqword(FIELD_OFFSET(TEB, ProcessEnvironmentBlock))`** and `#error`s on x86 builds. Always select an **x64** configuration in Visual Studio.

## Get a pointer to the PEB

```c
PPEB PebPtr = (PPEB)__readgsqword(FIELD_OFFSET(TEB, ProcessEnvironmentBlock));
PPEB_LDR_DATA LdrDataPtr = (PPEB_LDR_DATA)PebPtr->Ldr;
```

## Walk `InMemoryOrderModuleList`

The list is circular; you stop when **`CurrentModule == HeadList`**:

```c
PLIST_ENTRY HeadList = &(LdrDataPtr->InMemoryOrderModuleList);
PLIST_ENTRY CurrentModule = HeadList->Flink;

while (CurrentModule != HeadList)
{
    PLDR_DATA_TABLE_ENTRY LdrDataEntry =
        CONTAINING_RECORD(CurrentModule, LDR_DATA_TABLE_ENTRY, InMemoryOrderLinks);
    /* use LdrDataEntry->BaseDllName, DllBase, BaseNameHashValue, ... */
    CurrentModule = CurrentModule->Flink;
}
```

`CONTAINING_RECORD` converts a pointer to an embedded **`LIST_ENTRY`** into a pointer to the **containing** structure.

## Hash link table (`Helpers.c`)

**`Part5/Helpers.c`** adds hashing helpers (`LdrpHashUnicodeString`, `LdrHashEntry`) and **`ObtainHashTable`**, which takes the **loader lock**, scans **initialization-order** module entries, and derives a pointer to the loader’s hash bucket array. This is advanced material; treat it as an optional deep dive after you are comfortable with the basic list walk in **`main.c`**.

> [!tip]
> When you touch loader structures in real tools, assume **other threads** can mutate the loader data. The sample uses **`EnterCriticalSection` / `LeaveCriticalSection`** around some paths—understand **why** before copying patterns into your own code.

**Project:** `IntroToC/Part5`.
