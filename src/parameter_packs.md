# Variadic and Generic Code

**Parameter packs** and **variadic templates** let you write functions that accept zero or more arguments in a type-safe way. With C++20 you can constrain the pack with concepts like **std::convertible_to** so only certain types are accepted. This chapter uses the **ParameterPacks** project to show a variadic function, pack expansion, and a type constraint.

## Parameter pack basics

- A **parameter pack** is declared with an ellipsis before the name, e.g. **`...s`**. The pack can hold multiple arguments of (possibly different) types.
- **Expanding** the pack means using it in a pattern that produces a comma-separated list; the pack name goes on the other side of the ellipsis, e.g. **`s...`**.
- You can only use a parameter pack in a context that expands it (e.g. passing to a function, building an initializer list, or a template argument list).

## A constrained variadic function

From `IntroToCpp/ParameterPacks/main.cpp`:

```cpp
void VariadicTemp1(std::convertible_to<std::string_view> auto&& ...s);

int main()
{
	std::println("{}: {} was built on {}", __FUNCTION__, __EXE__, __TIMESTAMP__);

	VariadicTemp1("hello", std::string{ "there" }, std::string_view{ "these are strings" });
}

void VariadicTemp1(std::convertible_to<std::string_view> auto&& ...s)
{
	for (auto thing : std::initializer_list<std::string_view>{ s... })
	{
		std::cout << thing << " ";
	}
	std::println("");
}
```

- **`std::convertible_to<std::string_view> auto&& ...s`** — Each argument must be convertible to `std::string_view`. **auto&&** allows both lvalues and rvalues (forwarding reference for each element). **...s** is the parameter pack.
- **`std::initializer_list<std::string_view>{ s... }`** — **s...** expands the pack into the initializer list: each argument is converted to `std::string_view` and the list is built. The loop then iterates over those string views.

So the function accepts any number of arguments as long as each is convertible to `std::string_view`, and it prints them space-separated. The call passes a string literal, a `std::string`, and a `std::string_view`; all satisfy the constraint.

## Why use a type constraint?

Without **std::convertible_to<std::string_view>**, callers could pass types that don't convert to string view, and errors would appear deep in the template or at runtime. The constraint gives clearer compile-time errors and documents the intended use. Other constraints (e.g. `std::same_as<DWORD>` for a pack of DWORDs) can be used the same way.

Build and run the **ParameterPacks** project to see variadic templates and pack expansion in action.
