# Attributes

Attributes add metadata or hints for the compiler: they can enforce usage (e.g. “don’t ignore the return value”), mark functions that never return, or deprecate old APIs. This chapter uses the **Attributes** project (`Useful.hpp`, `Useful.cpp`, `main.cpp`) to show **[[nodiscard]]**, **[[noreturn]]**, and **[[deprecated]]**, plus a brief note on **__assume**.

## [[nodiscard]]

If a function’s return value is important (e.g. an error code or a newly allocated resource), you can mark it with **[[nodiscard]]**. The compiler will warn when the return value is discarded.

From `IntroToCpp/Attributes/Useful.hpp`:

```cpp
[[nodiscard]]
unsigned long ResolveLastError(const unsigned long& errorCode);

[[nodiscard("You must check the return value")]]
unsigned long ResolveLastError(const int& errorCode);
```

- The first overload uses **[[nodiscard]]** with no message.
- The second uses **[[nodiscard("You must check the return value")]]** so the warning includes a custom message.

If callers write `ResolveLastError(5);` without using the result, the compiler can emit a warning. Use `[[nodiscard]]` on functions whose return value should always be checked.

## [[noreturn]] and [[deprecated]]

**[[noreturn]]** tells the compiler that the function never returns (e.g. it always throws or terminates). **[[deprecated]]** marks something as obsolete; the compiler can warn when it is used and optionally show a message.

From `Useful.hpp`:

```cpp
[[noreturn, deprecated("This function is deprecated. Use the newer version.")]]
void DumpString(const std::string_view& strings);
```

- **[[noreturn]]** — The compiler may assume that code after a call to `DumpString` is unreachable (useful for optimization and static analysis).
- **[[deprecated("...")]]** — Calls to `DumpString` can produce a deprecation warning with the given message.

You can combine multiple attributes in one list, as shown. The implementation in `Useful.cpp` would typically not return (e.g. call `std::terminate` or throw); as written in the project it is a stub.

## __assume (compiler hint)

In the **Attributes** implementation, **__assume** is used to tell the compiler something about the program so it can optimize (e.g. assume a path is never taken). From `Useful.cpp`:

```cpp
unsigned long ResolveLastError(const unsigned long& errorCode)
{
	__assume(errorCode > 0);
	SetLastError(errorCode);
	std::println("{}: GetLastError: {}", errorCode, GetLastError());
	return 0;
}

unsigned long ResolveLastError(const int& errorCode)
{
	switch (errorCode)
	{
	case 1:
	case 2:
	case 3:
		std::println("{}: the case: {}", __FUNCTION__, errorCode);
		break;
	default:
		__assume(0);  // dead code; compiler may omit it
	}
	return 0;
}
```

- **__assume(condition)** — A hint that the condition is true at that point; undefined behavior if it isn’t. Used here to document assumptions or unreachable code.
- **__assume(0)** in `default` — Tells the compiler this branch is never taken, so it may omit the code. Use only when you are certain the branch is unreachable.

**__assume** is a compiler-specific extension (e.g. MSVC); it is not standard C++. Use it only where you need that kind of optimization hint.

Build and run the **Attributes** project to see how the compiler responds to `[[nodiscard]]` and `[[deprecated]]` in practice.
