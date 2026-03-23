# Output and Formatting

C++20's `std::println` uses format strings similar to `std::format`: placeholders like `{}` are replaced by formatted arguments. This chapter shows alignment, fill, width, numeric options, and how to format pointers and runtime-built format strings using the **STD_println** and **DynamicFormatArgs** projects.

## Alignment and width

From `IntroToCpp/STD_println/main.cpp`:

```cpp
std::println("using alignment with 10 spaces... ");
std::println("\t|{:>10}|", "right");   // Right-aligned
std::println("\t|{:<10}|", "left");    // Left-aligned
std::println("\t|{:^10}|", "center");  // Centered
```

- **`{:>10}`** — Right-align in a field of width 10.
- **`{:<10}`** — Left-align in a field of width 10.
- **`{:^10}`** — Center in a field of width 10.

## Fill character

You can specify a fill character before the alignment character:

```cpp
std::println("\t|{:*>10}|", "right");   // Right-aligned, filled with '*'
std::println("\t|{:-<10}|", "left");    // Left-aligned, filled with '-'
std::println("\t|{:.^10}|", "center");   // Centered, filled with '.'
```

So `{:*>10}` means "fill with `*`, then right-align in width 10."

## Numeric alignment and signed display

```cpp
unsigned long number = 42;
std::println("\tRight-aligned: {:>10}", number);
std::println("\tLeft-aligned: {:<10}", number);

unsigned long positive = 42;
int negative = -42;
std::println("\tPositive: {:+}", positive);  // Always show '+' for positive
std::println("\tNegative: {:+}", negative);
```

The `+` in `{:+}` forces the sign to be shown for both positive and negative numbers.

## Dynamic width and precision

Width and precision can be taken from arguments instead of being literal:

```cpp
unsigned long width = 10;
double pi = 3.141592653589793;
std::println("{:{}.{}f}", pi, width, 4);  // Width 10, precision 4
```

Here the first `{}` is for `pi`, the next two supply width and precision for the floating-point format.

## Formatting pointers with std::vformat

`std::println` does not accept pointer arguments in the usual way for hex output. To format a pointer (e.g. as hex), build the string with `std::vformat` and `std::make_format_args`, then print it. From `DumpPointers()` in `IntroToCpp/STD_println/main.cpp`:

```cpp
unsigned long course{ 670 };

std::string templateFields{ "{}: 0x{:x}" };

// Use uintptr_t for the pointer value to avoid format/runtime issues
auto pointer{ std::bit_cast<uintptr_t>(&course) };

std::string finalFields{
	std::vformat(
		templateFields,
		std::make_format_args(__FUNCTION__, pointer)
	)
};

std::println("{}", finalFields);
```

- **`std::vformat(templateFields, std::make_format_args(...))`** — Formats the template string with the given arguments and returns a `std::string`.
- Casting the address to **`uintptr_t`** (here via `std::bit_cast`) gives an integer type that the format layer can format as hex (`{:x}`) without undefined behavior. Using the raw pointer in the format API can cause runtime errors on some implementations.

Alternatively, for simple pointer output you can use streams:

```cpp
std::cout << "the address of course is: 0x" << std::hex << &course << std::endl;
```

## Runtime format string (DynamicFormatArgs)

When the format string itself is built at runtime (e.g. from user input or configuration), use `std::vformat` with `std::make_format_args`:

From `IntroToCpp/DynamicFormatArgs/main.cpp`:

```cpp
std::string temp{
	"this {} is just {} vformatted strings \n"
};

std::string szFinal{
	std::vformat(
		temp,
		std::make_format_args(argv[0], argv[1])
	)
};

std::cout << szFinal;
```

Here `temp` is the format string and the arguments are filled in from `argv`. This pattern is useful whenever the format template is not a compile-time constant.

Build and run **STD_println** and **DynamicFormatArgs** to see all of these formatting options in action.
