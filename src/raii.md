# RAII

**RAII** (Resource Acquisition Is Initialization) is a core C++ idiom: you acquire a resource when you construct an object and release it when the object is destroyed. That way you don't forget to free the resource, and the compiler guarantees cleanup even when exceptions are thrown. This chapter uses the **RAII** and **GetProcAddr-RAII** projects to illustrate RAII for raw memory and for Windows API handles.

## What RAII means

From the comments in `IntroToCpp/RAII/main.cpp`:

- The moment you **acquire** a resource, you **initialize** an object with it (e.g. in the constructor).
- The resource's **release** should be **automatic** (in the destructor), so you don't have to remember to free it.
- The resource can be memory, file handles, process handles, mutexes, etc.
- Whether the code succeeds or fails (including by throwing), the destructor runs when the object goes out of scope, so the resource is freed.

## Example: RAII for a dynamic array (DoubleArrayRAII)

From `IntroToCpp/RAII/main.cpp`:

```cpp
class DoubleArrayRAII final
{
public:
	// ctor: acquire the resource
	explicit DoubleArrayRAII(size_t size) : m_resource{ new double[size] } {}

	// dtor: release the resource
	~DoubleArrayRAII()
	{
		std::println("freeing memory");
		delete[] m_resource;
	}

	// No copying: two objects would point to the same resource
	DoubleArrayRAII(const DoubleArrayRAII&) = delete;
	DoubleArrayRAII& operator=(const DoubleArrayRAII&) = delete;

	// array subscript operator
	double& operator[](size_t index) noexcept { return m_resource[index]; }
	const double& operator[](size_t i) const noexcept { return m_resource[i]; }

	double* get() const noexcept { return m_resource; }

	// Optional: hand ownership to the caller; RAII object no longer frees
	double* release() noexcept
	{
		double* result = m_resource;
		m_resource = nullptr;
		return result;
	}
private:
	double* m_resource;
};
```

- **Constructor** — Allocates `new double[size]` and stores the pointer in `m_resource`.
- **Destructor** — Frees the array with `delete[] m_resource`. This runs when the object goes out of scope or is destroyed, so the memory is always released.
- **Deleted copy constructor and assignment** — Prevents two `DoubleArrayRAII` objects from sharing the same pointer; otherwise both would try to delete it.
- **`operator[]`** — Provides array-like access.
- **`release()`** — Transfers ownership to the caller: returns the pointer and sets `m_resource` to `nullptr` so the destructor no longer deletes it. Use when you need to pass ownership out of the RAII object.

Usage:

```cpp
DoubleArrayRAII values{ 5 };
for (size_t i{}; i < 5; i++)
{
	values[i] *= 2;  // If something throws here, the dtor still runs and frees memory
}
```

## RAII for Windows API: LoadLibrary / FreeLibrary

The **GetProcAddr-RAII** project wraps `LoadLibrary`, `GetProcAddress`, and `FreeLibrary` so the DLL is always freed. From `IntroToCpp/GetProcAddr-RAII/main.cpp`:

```cpp
class ProcedurePtr
{
public:
	explicit ProcedurePtr(FARPROC ptr) : _funcptr(ptr) {}

	template <typename TEMP, typename = std::enable_if_t<std::is_function_v<TEMP>>>
	operator TEMP* () const
	{
		return reinterpret_cast<TEMP*>(_funcptr);
	}

private:
	FARPROC _funcptr;
};

class LoadLibHelper
{
public:
	explicit LoadLibHelper(std::string_view DllName) : _modHandle(LoadLibraryA(DllName.data())) {}
	~LoadLibHelper() { FreeLibrary(_modHandle); }

	ProcedurePtr operator[](std::string_view funcName) const
	{
		return ProcedurePtr(GetProcAddress(_modHandle, funcName.data()));
	}

private:
	HMODULE _modHandle;
};
```

- **LoadLibHelper** — Constructor calls `LoadLibraryA`; destructor calls `FreeLibrary`. When a `LoadLibHelper` goes out of scope, the DLL is unloaded.
- **`operator[](std::string_view funcName)`** — Returns a `ProcedurePtr` that wraps the result of `GetProcAddress`. The caller can use it to get a function pointer.
- **ProcedurePtr** — Holds a `FARPROC`. The template conversion operator `operator TEMP* ()` allows casting to a specific function pointer type; `std::enable_if_t<std::is_function_v<TEMP>>` restricts it to function types so it doesn't match pointer-to-object types.

Example usage pattern (conceptually):

```cpp
LoadLibHelper lib("some.dll");
auto func = lib["SomeFunction"];  // ProcedurePtr; can convert to void (*)(), etc.
// When lib goes out of scope, FreeLibrary is called automatically.
```

Together, **RAII** and **GetProcAddr-RAII** show the same idea: acquire in the constructor, release in the destructor, and avoid copying when the resource is not shareable. Build and run these projects to see RAII in action.
