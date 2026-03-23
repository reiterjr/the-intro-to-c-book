# CRT-free DLL (optional)

Some courses end with a **DLL** that minimizes or avoids the **C runtime**, walks **`InMemoryOrderModuleList`**, and compares **hashes**—similar ideas to earlier chapters but with **`DllMain`** constraints.

> [!NOTE]
> This is **supplementary**. Building DLLs involves **exports**, **`DllMain`**, loader **lock re-entrancy**, and special **linker** settings. Follow your instructor’s template and checklist.

> [!WARNING]
> Calling **LoadLibrary**-style APIs from **`DllMain`** while holding the **loader lock** can **deadlock**. Do not copy PEB-walking samples into **`DllMain`** without understanding **what** your init path calls.

## What to implement (if assigned)

- A **`DllMain`** that does minimal work (or defers heavy work per instructor guidance).
- Optional export **`boo`** or similar that performs **module enumeration** / **hash** match **outside** dangerous lock contexts.
- Linker settings and **CRT** avoidance exactly as the **Classroom boilerplate** specifies.

## Implement

1. Use **only** the **DLL project** and settings from **your Classroom template**.
2. **Build** the DLL, load/test per rubric, **commit**, and **push**.
