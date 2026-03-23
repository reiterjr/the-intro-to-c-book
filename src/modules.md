# Modules

C++20 **modules** let you import the standard library and your own module units instead of relying only on `#include`. That can improve build times and isolate interfaces. This chapter uses **FirstModule** (which already uses `import std`) and **ErrorModule** with a custom **errors** module implemented in `errors.ixx`.

## Importing the standard library

From the start of the book you've seen:

```cpp
import std;

int main()
{
	std::println("{}:{}: hello!", __FUNCTION__, __LINE__);
}
```

**`import std;`** imports the C++ standard library as a module. Your project must be configured to build the standard library module (e.g. in Visual Studio, use a C++20 or later standard and the option that enables the `std` module). After that, you don't need to `#include` most standard headers for the parts you use via the module.

## Defining and exporting a custom module

A **module** is declared in a **module unit** (e.g. an `.ixx` file). The module name must be the first declaration; no `#include` or other code may appear before it.

From `IntroToCpp/ErrorModule/errors.ixx`:

```cpp
// Module name must be first; no includes before this line
export module errors;

#include <Windows.h>

import std;

export std::string ResolveErrorCode(
	const std::string_view& Message,
	const DWORD ErrorCode
);

std::string ResolveErrorCode(
	const std::string_view& Message,
	const DWORD ErrorCode
)
{
	LPSTR messageBuffer{};
	std::string resolvedMessage{};

	DWORD retval{
		FormatMessageA(
			FORMAT_MESSAGE_ALLOCATE_BUFFER | FORMAT_MESSAGE_FROM_SYSTEM | FORMAT_MESSAGE_IGNORE_INSERTS,
			nullptr,
			ErrorCode,
			0,
			(LPSTR)&messageBuffer,
			0,
			nullptr
		)
	};

	if (!retval)
		return std::string{ "" };

	resolvedMessage = std::format("{} {}", Message, std::string(messageBuffer, retval));

	if (messageBuffer)
		LocalFree(messageBuffer);

	return resolvedMessage;
}
```

- **`export module errors;`** — Declares this translation unit as the module named **errors**. It must be the first declaration.
- **`export std::string ResolveErrorCode(...);`** — The function is **exported**, so any translation unit that **import**s **errors** can call it.
- The implementation of `ResolveErrorCode` uses the Windows API `FormatMessageA` to turn an error code into a string, then returns a formatted message. The function is defined in the same file after the `export` declaration.

(Some setups require a pragma to allow `#include <Windows.h>` inside the module; the project uses `#pragma warning(disable : 5244)` for that.)

## Importing and using the custom module

From `IntroToCpp/ErrorModule/main.cpp`:

```cpp
import std;
import errors;

int main()
{
	std::println("testing with error code 5: {} ", ResolveErrorCode("Oops! ", 5));
	return 0;
}
```

- **`import errors;`** — Makes the **errors** module’s exported names visible. No path or file extension is used; the build system maps the module name to the correct `.ixx` (or other module unit).
- **`ResolveErrorCode("Oops! ", 5)`** — Calls the function exported from the **errors** module. The result is a string that combines the prefix and the system message for error code 5.

So the flow is: define a module in an `.ixx` (or other module source), export the interface you want, then **import** that module in other files. Build and run the **ErrorModule** project to see custom modules in action; **FirstModule** shows the standard library as a module.
