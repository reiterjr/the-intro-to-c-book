# HTTP with WinINet

This chapter covers a minimal **HTTP GET** using **WinINet** (`wininet.dll`), plus readable errors via **`FormatMessageW`** pulling messages from **`wininet.dll`**.

Link **`Wininet.lib`** and include **`Wininet.h`**.

## Flow in `main`

Typical sequence:

1. **`InitSession`** — `InternetOpenA` with a user agent and **`INTERNET_OPEN_TYPE_PRECONFIG`**.
2. **`MakeRequest`** — `InternetConnectA`, then `HttpOpenRequestA` (and related) to obtain an **`HINTERNET`** request handle.
3. **`SendRequest`** — send headers/body; on failure, call your error helper.
4. **`ProcessResults`** — `InternetReadFile` (or similar) in a loop until done.
5. **`CloseSession`** — `InternetCloseHandle` for each open handle (request, connection, session).

Split these across **`.c`** files in **your template** (`HttpApis.c`, `Errors.c`, etc.) if your rubric asks for separation—**the book describes the pattern**; your repo holds the actual file names.

## Formatting WinINet errors

```c
#define BUFFER_SIZE 1024

void CheckLastInetError(LPCWSTR message, DWORD dwLastErr)
{
    WCHAR buf[BUFFER_SIZE];
    DWORD n = FormatMessageW(
        FORMAT_MESSAGE_FROM_HMODULE,
        GetModuleHandleW(L"wininet.dll"),
        dwLastErr,
        0UL,
        buf,
        BUFFER_SIZE,
        NULL
    );
    if (n)
        wprintf(L"%s: %s", message, buf);
}
```

> [!TIP]
> Distinguish **Win32** errors from **`InternetError`** / extended WinINet codes your debugger shows—always thread **`GetLastError`** (or the API’s documented error path) into **`FormatMessage`** with the right module when possible.

> [!NOTE]
> Samples often target **`127.0.0.1:8080`**. Run a local listener or change **host** / **port** to match your lab.

## Implement

1. Add **WinINet** session/connect/request/send/read/close in **your template**.
2. Wire **`CheckLastInetError`** (or equivalent) for failed steps.
3. **Build**, test against a real or mock server, **commit**, and **push**.
