# Threads, heap args, and directory enumeration

This chapter covers passing a **structure** to a worker thread with **`CreateThread`**, and enumerating files with **`FindFirstFileA`** / **`FindNextFileA`** / **`FindClose`**.

## Passing a parameter to `CreateThread`

Allocate a struct (e.g. **`ARGS`**) on the **heap**, fill fields such as a search pattern, pass the pointer as **`lpParameter`**, then wait and clean up:

```c
typedef struct _ARGS {
    CHAR fileName[MAX_PATH];
} ARGS, *PARGS;

PARGS pArgs = HeapAlloc(GetProcessHeap(), HEAP_ZERO_MEMORY, sizeof(ARGS));
if (!pArgs)
    return GetLastError();

memcpy(pArgs->fileName, "C:\\Users\\*", strlen("C:\\Users\\*") + 1);

HANDLE hThread = CreateThread(NULL, 0, (LPTHREAD_START_ROUTINE)DirSearch, pArgs, 0, NULL);
if (!hThread) {
    HeapFree(GetProcessHeap(), 0, pArgs);
    return GetLastError();
}

WaitForSingleObject(hThread, INFINITE);
CloseHandle(hThread);
HeapFree(GetProcessHeap(), 0, pArgs);
```

> [!WARNING]
> Casting **`DirSearch`** to **`LPTHREAD_START_ROUTINE`** only works if **`DirSearch`** uses the correct **return type** and **calling convention** (`DWORD WINAPI ThreadProc(LPVOID)`). Mismatches cause **stack corruption**.

## Thread procedure: `FindFirstFile` loop

Inside **`DirSearch`** (or your name):

- If **`lpParam`** is non-NULL, read **`((PARGS)lpParam)->fileName`** (or use a default pattern).
- **`FindFirstFileA`** → check **`INVALID_HANDLE_VALUE`** → **`GetLastError`** on failure.
- **`do` … `while (FindNextFileA(...))`**.
- **`FileTimeToSystemTime`** for display.
- Combine **`nFileSizeHigh`** / **`nFileSizeLow`** with **`LARGE_INTEGER`** for 64-bit size.
- Skip **`.`** and **`..`**; branch on **`FILE_ATTRIBUTE_DIRECTORY`**.
- **`FindClose`** before return.

> [!NOTE]
> Pick a directory you are **allowed** to scan. **`C:\*`** or **`C:\Users\*`** are teaching examples only.

## Implement

1. Define **`ARGS`** (or use the template’s typedef), implement **`DirSearch`** in its own **`.c`** file if your rubric splits modules.
2. **`CreateThread`** from **`main`**, wait, free heap.
3. **Build**, run, **commit**, and **push**.
