# HTTP with WinINet

This chapter follows **`IntroToC/Part9`**: a minimal **HTTP GET** using **WinINet** (`wininet.dll`), with error text from **`FormatMessageW`**.

## Flow in `main`

1. **`InitSession`** — `InternetOpenA` with a browser-like user agent and **`INTERNET_OPEN_TYPE_PRECONFIG`** (respects system proxy settings).
2. **`MakeRequest`** — `InternetConnectA` + `HttpOpenRequestA` (see **`HttpApis.c`** for full sequence).
3. **`SendRequest`** — sends the request; on failure **`CheckLastInetError`** prints a message.
4. **`ProcessResults`** — reads the response (implementation in **`HttpApis.c`**).
5. **`CloseSession`** — `InternetCloseHandle`.

## Formatting WinINet errors

**`Part9/Errors.c`** uses **`FormatMessageW`** with **`FORMAT_MESSAGE_FROM_HMODULE`** and **`GetModuleHandleW(L"wininet.dll")`** so error codes map to human-readable strings:

```c
FormatMessageW(
    FORMAT_MESSAGE_FROM_HMODULE,
    GetModuleHandleW(L"wininet.dll"),
    dwLastErr,
    0UL,
    szFormatBuffer,
    BUFFER_SIZE,
    NULL
);
```

> [!tip]
> Always distinguish **Win32 errors** (`GetLastError` after some APIs) from **HRESULT**-style returns. WinINet commonly sets extended error info; your debugger and `FormatMessage` are your friends.

> [!note]
> The sample targets **`127.0.0.1:8080`**. You need a listening HTTP server on that port, or change **`theTarget`** / **`thePort`** to match your lab environment.

**Project:** `IntroToC/Part9`.
