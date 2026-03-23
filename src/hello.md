# Hello C++

This chapter gets you to a minimal C++ program: one that uses the C++20 standard library module and prints a line of text. If you can build and run this, your environment is ready for the rest of the book.

## Your first C++ program

In modern C++ with modules, the program can be very small. Here is the full program from the **FirstModule** project (`IntroToCpp/FirstModule/main.cpp`):

```cpp
import std;

int main()
{
	std::println("{}:{}: hello!", __FUNCTION__, __LINE__);
}
```

- **`import std;`** — Imports the C++20 standard library as a module. Your project must be set up to use the standard library module (e.g. MSVC with `/std:c++20` or later and the std module enabled).
- **`int main()`** — Program entry point, same idea as in C.
- **`std::println("{}:{}: hello!", __FUNCTION__, __LINE__);`** — Prints a line. The `{}` placeholders are filled by the following arguments. `__FUNCTION__` and `__LINE__` are preprocessor macros that expand to the current function name and line number, so you get output like `main:5: hello!`.

Run the **FirstModule** project from `IntroToCpp/FirstModule/` to see this in action. Once this builds and runs, you're ready for variables, pointers, and references in the next chapter.
