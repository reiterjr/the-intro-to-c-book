# Variables, Pointers, and References

If you're coming from C, you already know variables and pointers. In C++, **references** give you a way to pass or refer to an object without copying it and without using pointer syntax. This chapter contrasts variables, pointers, and pass-by-value vs pass-by-reference using the **PointersAndReferences** project.

## Variables and pointers

A **variable** holds a value. A **pointer** is a variable that holds the address of another object. The address-of operator `&` gives you the address of an object.

From `IntroToCpp/PointersAndReferences/main.cpp`:

```cpp
// a basic variable
DWORD theAnswer{ 42 };

// a basic pointer to variable
PDWORD ptrTheAnswer{ &theAnswer };

std::println(
	"{}: theAnswer: {}, it's address: {:p}, ptrTheAnswer: {:p} \n",
	__FUNCTION__,
	theAnswer,
	(PVOID)&theAnswer,
	(PVOID)ptrTheAnswer
);
```

Here `theAnswer` is a variable (value 42), and `ptrTheAnswer` is a pointer that holds the address of `theAnswer`. The `{ 42 }` and `{ &theAnswer }` are brace-initialization. On Windows, `DWORD` and `PDWORD` are the usual 32-bit type and pointer-to-DWORD; the cast to `(PVOID)` is used so the format specifier can print the address.

## Pass-by-value (a copy)

When you pass an argument **by value**, the function receives a **copy**. Changes inside the function do not affect the original.

Declaration in `ByValue.hpp`:

```cpp
DWORD ModifyByValue(DWORD dwByValue);
```

Definition in `ByValue.cpp`:

```cpp
DWORD ModifyByValue(DWORD dwByValue)
{
	dwByValue += 42UL;
	return dwByValue;
}
```

The parameter `dwByValue` is a copy. Adding 42 to it only changes the copy; the caller's variable is unchanged. The function returns the modified value so the caller can use it if needed.

## Pass-by-reference (no copy)

When you pass by **reference**, the parameter is an alias for the original object. Changes inside the function affect the original. In C++ you use `&` after the type to denote a reference.

Declaration in `ByReference.hpp`:

```cpp
DWORD ModifyByReference(DWORD& dwByReference);
```

Definition in `ByReference.cpp`:

```cpp
DWORD ModifyByReference(DWORD& dwByReference)
{
	dwByReference += 42UL;
	return dwByReference;
}
```

Here `dwByReference` is a reference to the caller's `DWORD`. Adding 42 to it changes the caller's variable. No pointer syntax is needed at the call site.

## Seeing the difference in main

In `main`, the same variable is passed to both functions:

```cpp
auto retval = ModifyByValue(theAnswer);
// theAnswer is still 42 here; retval is 84

retval = ModifyByReference(theAnswer);
// theAnswer is now 84; retval is 84
```

After `ModifyByValue(theAnswer)`, `theAnswer` is unchanged. After `ModifyByReference(theAnswer)`, `theAnswer` has been updated. For large objects or when you want the function to modify the argument, passing by reference is usually the better choice.

You can build and run the **PointersAndReferences** project to see the before/after values printed for both styles.
