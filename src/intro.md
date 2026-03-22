# Introduction

Welcome to **Intro to C** on Windows. This book accompanies the C source code in the **`IntroToC`** folder: a sequence of small Visual Studio projects (Intro, lab1, Part2–Part11, and a few optional extras). Each chapter points at the relevant project so you can read, build, and experiment.

> [!IMPORTANT]
> **GitHub Classroom**
>
> 1. Open the assignment: **[Accept the GitHub Classroom assignment](https://classroom.github.com/a/6mIQr-ph)**.
> 2. Sign in to GitHub if prompted, then **accept** the assignment so a personal repository is created for you.
> 3. Watch for the **invitation / acknowledgement email** from GitHub (or your course) and complete any steps it asks for.
> 4. **Clone your Classroom repository** to your machine (HTTPS or SSH, as your instructor prefers) and open the solution or projects from there when doing homework.

> [!warning]
> **The “Feedback” pull request**
>
> The **Feedback** pull request in your repo is for the instructor to leave comments and suggestions. **Do not merge or close it.** Keep doing your work on **`main`** (or the branch your instructor specifies) and push regularly. New commits on **`main`** will show up for review; the Feedback PR is a conversation surface, not something to finish like a normal feature branch.

## What you need

- **Windows** and **Visual Studio** (or another build setup that can compile Windows C projects with the Windows SDK).
- Many later labs use **x64** only: several projects use `__readgsqword` to read the TEB/PEB and will **fail to compile in 32-bit (x86)** configurations by design.
- Basic familiarity with a terminal, Git, and cloning/pushing to GitHub.

## How this book is organized

Chapters follow the **same order as the parts** in `IntroToC`, from numeric types and `printf` through pointers, structs, the module list, PE basics, threads and file search, and WinINet HTTP. Later optional material covers intrinsics and low-level patterns.

## Repository layout (reference)

| Location | Purpose |
|----------|---------|
| `IntroToC/` | C lab projects. This book covers **`.c` / `.h`** work only—ignore sibling folders that are clearly **C++** (e.g. `FormatArgs`, `StructsWithUnions`) or build output under **`x64/`** unless your instructor says otherwise. |
| `IntroToCBook/` | This mdBook (what you are reading). |

When instructions say “open the **Part3** project,” that means the folder `IntroToC/Part3/` (and its `.vcxproj` in the solution).

## Conventions

- Snippets are shortened when helpful; the **full source** is always in `IntroToC`.
- Windows types such as `DWORD`, `ULONG`, `PCHAR`, and `UNICODE_STRING` come from **`Windows.h`** and related headers.
- `ERROR_SUCCESS` is a common “success” return for Win32-style `main` examples in this codebase.

Next chapter: **Windows types, `main`, and `printf`** (`Intro` + `lab1`).
