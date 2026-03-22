# CRT-free DLL (`CRT-DLL`)

**`IntroToC/CRT-DLL/dllmain.c`** (and related headers) explores a **DLL** that avoids the C runtime for certain operations and walks **`InMemoryOrderModuleList`** similarly to earlier parts—comparing a hash produced by an **intrinsic-style hasher** to locate a module.

> [!note]
> This project is **supplementary**. Building DLLs involves **exports**, **entry points** (`DllMain`), loader lock re-entrancy rules, and linking **without** the CRT—topics your instructor may cover separately.

> [!warning]
> Holding the **loader lock** from **`DllMain`** while calling APIs that load other DLLs can **deadlock**. Do not merge PEB-walking samples blindly into production `DllMain` code.

**Project:** `IntroToC/CRT-DLL`.
