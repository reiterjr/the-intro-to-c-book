# Lambdas

A **lambda** is an anonymous callable object: you define a small function inline, often to pass to an algorithm or to capture local variables. This chapter uses the **Lambdas** project to show lambda syntax, captures, `mutable`, and using lambdas with the standard library.

## Lambda syntax

A lambda has three parts:

- **`[]`** — Capture list (what from the surrounding scope the lambda can use).
- **`()`** — Parameter list (like a function).
- **`{}`** — Body (the code that runs when the lambda is called).

So a minimal lambda looks like `[](){}`. It can be written on one line or split across lines.

## Generic lambda with std::find_if

From `IntroToCpp/Lambdas/main.cpp`, a lambda is used as the predicate for `std::find_if`:

```cpp
constexpr std::array<std::string_view, 4> users{ "Admin", "Guest", "sec670", "System" };

auto granted = std::find_if(
	users.begin(),
	users.end(),
	[](std::string_view user) { return user.find("System") != std::string_view::npos; }
);

if (granted == users.end())
	std::println("user not found");
else
	std::println("user found: {}", *granted);
```

- **`[]`** — Empty capture: the lambda doesn't use any local variables from the enclosing scope.
- **`(std::string_view user)`** — One parameter; `find_if` passes each element as `user`.
- **`{ return user.find("System") != std::string_view::npos; }`** — Returns true when the string contains `"System"`.

So the lambda is a small predicate that says “this element is the one we want.”

## Value capture [previous]

Capturing by value copies the variable into the lambda:

```cpp
int previous{ 10 };

auto lamb = [previous](auto nextClass) { return 5 + nextClass + previous; };

std::println("lamb is: {}", lamb(100));   // 5 + 100 + 10 = 115
std::println("previous after lamb(100) is: {}", previous);  // still 10
```

`previous` is read-only inside the lambda (by value). Changing it inside the lambda would not be allowed unless the lambda is `mutable`.

## Mutable capture

If you want to modify the captured copy, mark the lambda **mutable**:

```cpp
int previous = 10;

auto lamb = [previous](auto nextClass) mutable { return 5 + nextClass + ++previous; };

std::println("previous is: {}", previous);
std::println("lamb(100) is: {}", lamb(100));
std::println("previous after lamb(100) is: {}", previous);  // still 10; the copy inside the lambda changed
```

Inside the lambda, `previous` is a copy that can be modified; the outer `previous` is unchanged.

## Reference capture [&] and [&previous]

Capturing by reference lets the lambda see and modify the original variable:

```cpp
int previous = 10;
int next = 20;

auto lamb = [&]() {
	++previous;
	++next;
};

lamb();
lamb();
std::println("previous after lamb() is: {}", previous);  // 12
```

- **`[&]`** — Capture all local variables by reference. Any use of `previous` or `next` inside the lambda refers to the outer variables.

To capture only one variable by reference:

```cpp
auto lamb = [&previous]() {
	++previous;
	// ++next;  // error: next not in capture list
};
```

## Parameters by reference

Lambda parameters can be references so the lambda can modify the argument:

```cpp
std::string message{ "I changed the message!" };
int age{ 40 };

auto lamb = [](std::string& message, int& age) {
	std::println("the message is: {}", message);
	message = "woot!";
};

lamb(message, age);
std::println("did the message change? {}", message);  // "woot!"
```

## Capturing [this]

In a member function, a lambda can capture **`this`** to access the object's members (including private ones):

```cpp
class TheLamb
{
public:
	void Hello()
	{
		auto lambda = [this]() { SayHello(); };
		lambda();
	}

private:
	void SayHello()
	{
		std::println("Hello there! I'm this lambda :) ");
	}
};
```

`[this]` gives the lambda access to the current object so it can call `SayHello()`. Use `[this]` only when the lifetime of the object is guaranteed for as long as the lambda might be called.

Build and run the **Lambdas** project to see all of these forms in action.
