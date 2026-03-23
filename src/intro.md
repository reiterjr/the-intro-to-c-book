# Introduction

Welcome to **Intro to C** on Windows. This book walks through topics in order. Your **actual homework** lives in **one** GitHub Classroom repository (see below)—you start from a **base template** with boilerplate and grow it chapter by chapter.

> [!IMPORTANT]
> **GitHub Classroom**
>
> 1. Open the assignment: **[Accept the GitHub Classroom assignment](https://classroom.github.com/a/6mIQr-ph)**.
> 2. Sign in to GitHub if prompted, then **accept** the assignment so a **personal repository** is created for you.
> 3. That repo is created from a **base template** project: it includes **boilerplate** (solution, project files, stubs, etc.) that you will **keep using for the entire series**.
> 4. Watch for the **invitation / acknowledgement email** from GitHub (or your course) and complete any steps it asks for.
> 5. **Clone your Classroom repository** to your machine (HTTPS or SSH, as your instructor prefers) and open **that** solution in Visual Studio for all labs.

> [!NOTE]
> **One project, growing over time**
>
> As you move through the chapters, **add to the same template**—new files, functions, and behavior stack on what you already wrote. You do **not** start a fresh project each week unless your instructor says otherwise. Chapter titles (e.g. “Pointers”, “The PEB and loader”) describe **what you are learning next** and what to implement **inside** your template next.

> [!TIP]
> **Submit work after each chapter**
>
> When you finish the code for a **chapter** (or whatever unit your instructor defines), **commit** your changes with a clear message (e.g. `Complete Ch. 4 – lists and structs`) and **push** to **`main`** (or the branch your instructor uses). That push is what signals “ready for review.” Push **regularly** so feedback can keep up with your progress—not only at the very end of the course.

> [!WARNING]
> **The “Feedback” pull request**
>
> The **Feedback** pull request in your repo is for the instructor to leave comments and suggestions. **Do not merge or close it.** Keep implementing in **your template on `main`** (or the branch your instructor specifies), then **commit and push**; new commits on **`main`** are what get reviewed. The Feedback PR is a **conversation surface**, not a feature branch you complete or merge.

## How this book works

- **Theory and guidance** are written in each chapter: what the Windows APIs and C features are doing and why it matters.
- **Code in the book** is what you study and reproduce: samples are **complete enough to type in or adapt**; they are **not** a second codebase you must hunt for elsewhere.
- **Your Classroom template** holds the real project: you add `.c` / `.h` files, fill in `main`, split modules as directed, and wire everything to build.
- At the end of each chapter, a short **“Implement”** section reminds you to **build, test, commit, and push**—the book does not assume you have a separate “solution repo” to copy from.

## What you need

> [!NOTE]
> **VSCode in your browser**
>
> An attempt has been made to enable coding from your browser or even installing the GitHub Classroom VSCode extension. However, there is no guarantee that setup will work perfectly 100% of the time. The recommendation is to use VS2022 Community locally on a Windows host or Windows VM.

- **Windows** and **Visual Studio 2022 Community** (or another build setup that can compile Windows C projects with the Windows SDK).
- Many later labs use **x64** only: several topics use `__readgsqword` to read the TEB/PEB and will **fail to compile in 32-bit (x86)** configurations by design.
- Basic familiarity with a terminal, Git, and cloning/pushing to GitHub.

## How this book is organized

Chapters proceed from numeric types and `printf` through pointers, structs, the module list, PE basics, threads and file search, and WinINet HTTP. Later optional material covers intrinsics and low-level patterns. The order matches how the course builds concepts—not a folder tree on disk.

## Where things live

| Location | Purpose |
|----------|---------|
| **Your cloned Classroom repo** | The **base template** you accepted from GitHub Classroom—this is where you **edit, commit, and push** all course work. |
| **This book (`IntroToCBook`)** | **Primary reference** for explanations and code samples. |
| **`IntroToCBook/`** (published site or repo) | The rendered mdBook you read while coding. |

## Conventions

- Long samples may be **abbreviated** with `/* ... */` in the book; you still implement the **full** behavior in your template unless the chapter says otherwise.
- Windows types such as `DWORD`, `ULONG`, `PCHAR`, and `UNICODE_STRING` come from **`Windows.h`** and related headers.
- `ERROR_SUCCESS` is a common “success” return for Win32-style `main` examples.

Next chapter: **Windows types**—read the theory and samples, then implement them in **your template**, **commit**, and **push** when that stage is done.
