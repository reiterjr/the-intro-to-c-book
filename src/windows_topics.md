# Windows-Specific Topics

This chapter touches on Windows-only features used in the companion code: **calling conventions**, **pragma comment for linking**, **Win32 API** usage (e.g. CreateFile/CloseHandle), **RPC** enumeration with RAII-style cleanup, and **native application** entry. The snippets are from **CallMeMaybe**, **RPCEnum**, and **NativeApps/NativeHello**. All require the Windows SDK and appropriate link libraries.

## Calling conventions

On Windows, functions can use different **calling conventions** that define how arguments and the return value are passed. Two you may see:

- **__cdecl** — The default for C and many C++ functions (e.g. `main`). Caller cleans the stack.
- **__fastcall** — Allows passing some arguments in registers; often used for performance-sensitive or internal APIs.

From `IntroToCpp/CallMeMaybe/main.cpp`:

```cpp
int _cdecl main(INT argc, PCHAR argv[], PCHAR envp[])
{
	// ...
}

DWORD __fastcall _Use_decl_annotations_
ThisIsFastCall(INT& First, INT& Second, INT& Third)
{
	return First + Second + Third;
}
```

- **`_Use_decl_annotations_`** — Tells the compiler to apply SAL (Source Annotation Language) annotations so static analysis can better understand parameters (e.g. that they are read/written).

You usually only need to specify a convention when calling or implementing Windows API callbacks or when interfacing with code that expects a specific convention.

## Linking a library via #pragma comment

Instead of adding a library to the project settings, you can request it in source:

```cpp
#pragma comment(lib, "Shlwapi.lib")
```

The linker will link against `Shlwapi.lib`. The **CallMeMaybe** project uses this for `StrToIntExA` and similar functions. Other examples in the solution use `#pragma comment(lib, "Rpcrt4.lib")` for RPC.

## Win32 API: CreateFile and CloseHandle

From **CallMeMaybe**, a minimal pattern for creating a file and closing the handle:

```cpp
HANDLE hLogFile = INVALID_HANDLE_VALUE;
std::string logFileName{ "LogFile.txt" };

hLogFile = CreateFileA(
	logFileName.c_str(),
	GENERIC_WRITE,
	NULL,
	nullptr,
	CREATE_NEW,
	FILE_ATTRIBUTE_NORMAL,
	HANDLE()
);

if (INVALID_HANDLE_VALUE == hLogFile)
{
	printf("CreateFile error: %d\n", GetLastError());
	return ERROR_FILE_INVALID;
}

CloseHandle(hLogFile);
```

- **CreateFileA** — Opens or creates a file; returns a handle or `INVALID_HANDLE_VALUE` on failure.
- **CloseHandle** — Releases the handle. In real code you would often wrap this in an RAII class (like **GetProcAddr-RAII** does for LoadLibrary/FreeLibrary) so the handle is always closed.

This is Windows-specific and requires the Windows headers and linkage to the appropriate system libraries.

## RPC enumeration with RAII-style cleanup

The **RPCEnum** project uses the Windows RPC API to enumerate endpoints. The class holds RPC handles and frees them in the destructor so cleanup is automatic:

From `IntroToCpp/RPCEnum/main.cpp`:

```cpp
#pragma comment(lib, "Rpcrt4.lib")

class RPCEnum
{
public:
	explicit RPCEnum(const std::string& server) : _targetServer(server) {}

	~RPCEnum()
	{
		if (stringBinding) RpcStringFreeW(&stringBinding);
		if (bindingHandle) RpcBindingFree(&bindingHandle);
		if (inquiryHandle) RpcMgmtEpEltInqDone(&inquiryHandle);
	}

	void QueryEndpoints()
	{
		status = RpcMgmtEpEltInqBegin(
			nullptr,
			RPC_C_EP_ALL_ELTS,
			nullptr,
			RPC_C_VERS_ALL,
			nullptr,
			&inquiryHandle
		);
		// ... loop with RpcMgmtEpEltInqNextW, RpcBindingToStringBindingW, etc.
	}

private:
	std::string _targetServer{};
	RPC_STATUS status{};
	RPC_EP_INQ_HANDLE inquiryHandle{};
	RPC_BINDING_HANDLE bindingHandle{};
	RPC_WSTR stringBinding{};
	RPC_WSTR annotation{};
	// ...
};

int main()
{
	std::string target{ "192.168.236.136" };
	auto RPC{ RPCEnum(target) };
	RPC.QueryEndpoints();
}
```

The destructor releases the RPC string binding, binding handle, and inquiry handle so you don't have to remember to free them manually. The pattern is the same as in Chapter 7: acquire in the constructor (or in methods like `QueryEndpoints`), release in the destructor.

## Native application entry point

A **native** Windows application (e.g. one that runs without the C runtime or as a minimal process) can use a different entry point. From `IntroToCpp/NativeApps/NativeHello/main.cpp`:

```cpp
#include <Windows.h>
#include <winternl.h>

EXTERN_C_START

void NTAPI NtProcessStartup(PPEB peb)
{
	// native stuff here
}

EXTERN_C_END
```

- **NtProcessStartup** — Entry point used when the executable is run as a native application (e.g. by the native loader or in special environments). The **PPEB** (Process Environment Block) is passed by the loader.
- **EXTERN_C_START** / **EXTERN_C_END** — Ensure the function is linked with C linkage so the loader can find it by name without C++ name mangling.
- **NTAPI** — Denotes the Windows NT calling convention.

This is a very low-level topic; you would use it only when building a native executable (e.g. with the Windows NT native subsystem). For normal desktop apps, **main** or **wWinMain** is the entry point.

---

All of the code in this chapter is Windows-specific and requires the Windows SDK and the correct project settings (include paths, link libraries, and possibly subsystem and entry-point settings for native apps). Build **CallMeMaybe**, **RPCEnum**, and **NativeApps/NativeHello** only in a Windows development environment.
