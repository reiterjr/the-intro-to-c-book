# Namespaces and Organizing Code

Namespaces help you group names and avoid collisions with other code. C++ also has **enum class** for type-safe enumerations. This chapter uses the **namespaces** project to show nested namespaces, an enum class, and a simple class interface declared in a header.

## Nested namespaces and enum class

From `IntroToCpp/namespaces/tables.hpp`:

```cpp
#pragma once

#include <string>
#include <vector>

#define TABLE_SEPARATOR "-----"
#define TABLE_MAX_SIZE_DEFAULT (64)

namespace utils
{
	namespace ui {
		enum class TableAlign
		{
			LEFT,
			RIGHT
		};
```

- **`namespace utils { namespace ui { ... } }`** — Nested namespaces. You can also write `namespace utils::ui { ... }` in C++17.
- **`enum class TableAlign`** — A scoped enumeration. Values are referred to as `TableAlign::LEFT` and `TableAlign::RIGHT`, and they do not implicitly convert to integers, which reduces bugs.

## A class in a namespace

The same header declares a `Table` class inside `utils::ui`:

```cpp
		class Table
		{
		private:
			std::vector<std::vector<std::string>> headers;
			std::vector<utils::ui::TableAlign> column_align;
			std::vector<std::vector<std::vector<std::string>>> data;
			std::vector<std::vector<std::string>> current_line;

			bool border_top = true;
			bool border_bottom = true;
			bool border_left = true;
			bool border_right = true;
			bool interline = false;
			bool header_interline = true;
			bool corner = true;
			uint32_t margin_left;
			uint32_t cell_max_size = TABLE_MAX_SIZE_DEFAULT;
		public:
			explicit Table();

			void add_header_line(std::string header, utils::ui::TableAlign align = TableAlign::LEFT);
			void add_header_multiline(std::initializer_list<std::string> header, utils::ui::TableAlign align = TableAlign::LEFT);
			void add_item_line(std::string item);
			void add_item_multiline(std::initializer_list<std::string> list);
			void add_item_multiline(std::vector<std::string> list);

			void set_margin_left(uint32_t margin_left);
			void set_cell_max_size(uint32_t max_size);
			void set_corner(bool show) { corner = show; }
			void set_interline(bool show) { interline = show; }
			void set_header_interline(bool show) { header_interline = show; }
			void set_border_left(bool show) { border_left = show; }
			void set_border_top(bool show) { border_top = show; }
			void set_border_right(bool show) { border_right = show; }
			void set_border_bottom(bool show) { border_bottom = show; }
			void set_border(bool show) { border_bottom = show; border_left = show; border_right = show; border_top = show; }

			void new_line();
			void render(std::ostream& out);
		};
	}
}
```

Takeaways:

- **`explicit Table();`** — The constructor is explicit so it cannot be used for implicit conversions.
- **`std::initializer_list<std::string>`** — Allows callers to pass a brace-enclosed list of strings, e.g. `add_header_multiline({"Col1", "Col2"}, TableAlign::LEFT)`.
- **Inline member functions** — e.g. `void set_corner(bool show) { corner = show; }` are defined in the header.
- **Default argument** — `TableAlign align = TableAlign::LEFT` so callers can omit the second argument.

The implementation lives in `tables.cpp`, where member functions are qualified with the namespace: `utils::ui::Table::Table()`, `void utils::ui::Table::add_header_line(...)`, etc. The implementation uses a helper `utils::strings::utf8_string_size`; in a classroom or full repo you would either provide that function in a `utils::strings` namespace or simplify the table logic so the book and code stay in sync.

## Using the namespace in main

From `IntroToCpp/namespaces/main.cpp`:

```cpp
import std;

int main()
{
	std::println("{}:{}: hello!", __FUNCTION__, __LINE__);
}
```

In a fuller example you would construct `utils::ui::Table`, call `add_header_line`, `add_item_line`, `new_line`, and `render` to build and print a table. The header snippet above shows how namespaces and an enum class organize the public interface of a small utility class.
