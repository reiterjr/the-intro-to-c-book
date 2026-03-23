# Threads, heap args, and directory enumeration

This chapter follows **`IntroToC/Part8`**: passing a structure to a worker thread, and enumerating files with **`FindFirstFile`** / **`FindNextFile`**.

## Passing a parameter to `CreateThread`

**`Part8/main.c`** allocates a **`ARGS`** structure on the **process heap**, fills a path/pattern (here `C:\Users\*`), and starts **`DirSearch`** in a new thread:

```c
PARGS pArgs = HeapAlloc(GetProcessHeap(), HEAP_ZERO_MEMORY, sizeof(ARGS));
memcpy(pArgs->fileName, "C:\\Users\\*", strlen("C:\\Users\\*"));

hThread = CreateThread(NULL, 0, (LPTHREAD_START_ROUTINE)DirSearch, pArgs, 0, NULL);
```

Then it waits, closes the handle, and frees the heap block.

> [!warning]
> Casting **`DirSearch`** to **`LPTHREAD_START_ROUTINE`** only works if the function signature is compatible (return type and calling convention). Mismatches here cause **stack corruption** or subtle bugs.

## `DirSearch` and `WIN32_FIND_DATAA`

**`Part8/dirutils.c`**:

- Calls **`FindFirstFileA`**; on failure returns **`GetLastError()`**.
- Loops with **`FindNextFileA`** until done; **`FindClose`** releases the search handle.
- Uses **`FileTimeToSystemTime`** on **`ftCreationTime`**.
- Combines **`nFileSizeHigh`** / **`nFileSizeLow`** into a 64-bit size via **`LARGE_INTEGER`**.
- Skips **`.`** and **`..`**, and branches on **`FILE_ATTRIBUTE_DIRECTORY`**.

> [!note]
> The hard-coded path **`C:\*`** in **`dirutils.c`** is a teaching default. On your machine you may change it to a folder you are allowed to scan.

**Project:** `IntroToC/Part8`.
