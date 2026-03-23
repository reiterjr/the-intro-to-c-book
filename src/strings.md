# Strings in C++

C++ provides `std::string` for dynamic character sequences, plus `std::wstring` for wide characters and `std::string_view` for non-owning views. This chapter uses the **Strings** and **STD_strings** projects to cover string types, lifecycle, parsing, and a simple flag map.

## std::string lifecycle: empty, size, capacity

From `IntroToCpp/Strings/main.cpp`, an empty string still has a size and capacity managed by the implementation:

```cpp
std::string name{};

if (name.empty())
{
	std::println("{}:{} the string is empty at this point", __FUNCTION__, __LINE__);
}
std::println("{}:{} the variable's size: {}, the variable's capacity: {}", __FUNCTION__, __LINE__, name.size(), name.capacity());

name = "Jonathan";
std::println("{}:{} the string finally has something: {}", __FUNCTION__, __LINE__, name);
std::println("{}:{} the variable's size: {}, the variable's capacity: {}", __FUNCTION__, __LINE__, name.size(), name.capacity());

name.clear();
if (name.empty())
{
	std::println("{}:{} doing name.clear() makes the string empty again", __FUNCTION__, __LINE__);
}
```

- **`empty()`** — Returns whether the string has no characters.
- **`size()`** — Current number of characters.
- **`capacity()`** — How many characters the string can hold before reallocating.
- **`clear()`** — Empties the string (size becomes 0; capacity may remain).

## Splitting a string with ranges (StringSplitter)

The **Strings** project parses a line by splitting on spaces using C++20 ranges. From `ParsingUtils.hpp` and `ParsingUtils.cpp`:

```cpp
// ParsingUtils.hpp
std::vector<std::string> StringSplitter(const std::string& theLine);
```

```cpp
// ParsingUtils.cpp
std::vector<std::string> StringSplitter(const std::string& theLine)
{
	std::vector<std::string> values{};

	for (auto&& token : std::views::split(theLine, ' '))
	{
		values.emplace_back(token.begin(), token.end());
	}

	return values;
}
```

`std::views::split(theLine, ' ')` yields subranges; each is converted to a `std::string` and pushed into the vector. In `main` you can then iterate over the tokens:

```cpp
std::string args{"this is a command line test for arg parsing"};
auto tokens{ StringSplitter(args) };
for (auto& token : tokens)
{
	std::println("{}: token: {}", __FUNCTION__, token);
}
```

## std::string, std::wstring, and std::string_view

From `IntroToCpp/STD_strings/main.cpp`:

```cpp
std::string szCourse{ "ANSI: Welcome to class!" };
std::wstring wszCourse{ L"UNICODE: Welcome to class!" };

std::cout << szCourse << std::endl;
std::wcout << wszCourse << std::endl;
std::println("{}", szCourse);
```

- **`std::string`** — Narrow (char) string; works with `std::cout` and `std::println`.
- **`std::wstring`** — Wide (wchar_t) string; use `std::wcout`. Note: `std::println` with the usual format does not accept wide strings directly.
- **`std::string_view`** — A non-owning view over a contiguous sequence of characters. Good for function parameters when you don't need to own or modify the string.

A function that takes `std::string_view` can accept `std::string` or string literals without copying:

```cpp
void DumpString(std::string_view message)
{
	std::string formatted{ message.substr(0, 4) };
	std::println("extracting first 4 letters from: {} \n\t{}", message, formatted);
}
// Call: DumpString(szCourse);  or  DumpString("does this thing work?");
```

## Tokenizing with std::istringstream

Tokenizing by whitespace can be done with an input stream:

```cpp
std::vector<std::string> Tokenize(std::string& message)
{
	std::istringstream iss(message);
	std::string word{};
	std::vector<std::string> tokens{};

	while (iss >> word)
	{
		tokens.push_back(word);
	}
	return tokens;
}
```

The stream operator `>>` reads space-separated words into `word`; each is pushed into `tokens`.

## Parsing flags into a map

The **STD_strings** example builds a map of flags to optional values (e.g. `/file somefile.txt`, `/verbose` with no value). Simplified structure:

```cpp
std::unordered_map<std::string, std::string> MakeFlagsVales(const std::vector<std::string>& tokenVector)
{
	std::unordered_map<std::string, std::string> flagMap{};

	for (size_t i = 0; i < tokenVector.size(); ++i)
	{
		const auto& token{ tokenVector.at(i) };

		if (token.starts_with("/") || token.starts_with("-") || token.starts_with("--"))
		{
			std::string flag{ token };
			std::string value{};
			// If next token exists and is not another flag, use it as the value
			if (/* next is not a flag */)
				value = tokenVector.at(++i);
			flagMap[flag] = value;
		}
	}
	return flagMap;
}
```

## Structured bindings when iterating the map

To print each flag and its value, C++17 structured bindings keep the loop readable:

```cpp
void DumpFlagsValues(std::unordered_map<std::string, std::string>& flagMapping)
{
	for (const auto& [flag, value] : flagMapping)
	{
		std::println("{}: {}", flag, (value.empty() ? "(no value)" : value));
	}
}
```

`[flag, value]` binds to the key and value of each map element. Run the **Strings** and **STD_strings** projects to see these operations in action.
