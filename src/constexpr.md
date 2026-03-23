# Compile-Time Programming

**constexpr** lets you mark variables and functions so they can be evaluated at **compile time**. That enables constant expressions, better optimization, and in some cases compile-time data (e.g. string obfuscation). This chapter uses the **ConstExpr** project and **helpers.hpp** to show `constexpr` variables and functions, `if constexpr`, and type traits.

## constexpr variables

A `constexpr` variable must be initialized with a constant expression and can be used where the language requires a compile-time constant:

```cpp
constexpr std::string_view __EXE__{ "ConstExpr" };
std::println("{} was built on {}", __EXE__, __TIMESTAMP__);
```

`__EXE__` is a compile-time constant string view. `__TIMESTAMP__` is a preprocessor macro that expands to the build time.

## constexpr functions

A `constexpr` function can be evaluated at compile time when its arguments are constant. From `IntroToCpp/ConstExpr/helpers.hpp`:

```cpp
constexpr int FactorialLoop(int n)
{
	int result{ 1 };

	for (int i{ 2 }; i <= n; ++i)
	{
		result *= i;
	}

	return result;
}
```

When you call it with a constant, the result can be computed at compile time:

```cpp
constexpr int result{ FactorialLoop(5) };
std::println("factorial loop of 5 is {}", result);  // 120
```

The same function can still be used at runtime with non-constant arguments.

## if constexpr and type traits

**if constexpr** chooses a branch at **compile time**. The other branch is discarded, so it can contain code that would be invalid for some types. Combined with type traits you can specialize behavior per type:

From `IntroToCpp/ConstExpr/helpers.hpp`:

```cpp
template <typename TYPE>
constexpr TYPE add(TYPE a, TYPE b)
{
	if constexpr (std::is_integral_v<TYPE>)
	{
		return a + b;
	}
	else
	{
		return a * b;
	}
}
```

- **`std::is_integral_v<TYPE>`** — True for integral types (int, char, etc.), false for floating-point and other types.
- For integral types the compiler uses the `return a + b;` branch.
- For other types (e.g. `double`) it uses the `return a * b;` branch.

So `add(5, 11)` is 16, and `add(5.0, 11.0)` is 55.0. The choice is made at compile time, with no runtime overhead.

## Compile-time string obfuscation (optional)

The **ConstExpr** project also includes a macro that obfuscates a string at compile time so the literal does not appear plainly in the binary. The idea is to store an encrypted form in a `constexpr`-friendly structure and decrypt at runtime. From `helpers.hpp`, simplified:

- **`EncryptedString<N>`** — A small struct that holds encrypted character data and a key, with a `constexpr` constructor that XORs the string at compile time.
- **`STR_OBFUSCATE("text")`** — Produces an `EncryptedString` that decrypts to `"text"` when used (e.g. via `operator const char*` that calls `decrypt()`).

In `main`:

```cpp
auto sec670{ STR_OBFUSCATE("SEC670 should be hidden") };
std::cout << sec670 << std::endl;
```

The string `"SEC670 should be hidden"` appears only once in the source; in the compiled binary the stored form is obfuscated. This pattern is useful when you want to avoid plain-string literals in the binary while still using normal string literals in code.

Build and run the **ConstExpr** project to see `constexpr`, `FactorialLoop`, `add` with `if constexpr`, and the obfuscation in action.
