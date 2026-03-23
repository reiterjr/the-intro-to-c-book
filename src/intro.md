# Introduction

> [!warning]
> Warning! This is not complete in the slightest and is only just a placeholder for now.

This book is an introduction to C++ for those who have already completed the 10-part series introducing the C language found here: [SANS Intro to C](https://www.sans.org/webcasts/intro-c-windows-devs). If you have not completed that series, no worries, this series will cover some of those foundational topics. We assume you are comfortable with C fundamentals: variables, pointers, functions, and basic use of the preprocessor and compiler. If not, you will be by the end of this series.

## Audience and focus

- **Prerequisites:** Completion of the introductory C series.
- **Platform:** The material and accompanying code are aimed at **Windows**. You will use the Windows SDK and Visual Studio (or compatible tooling) to build and run the examples.
- **Goal:** To move from C to C++ in a structured way, using modern C++ (C++20/23) where it helps clarity and safety.

## The companion code

All example code lives in the **IntroToCpp** Visual Studio solution:

1. Open **IntroToCpp.sln** in Visual Studio (or open the solution folder in your IDE).
2. Each chapter corresponds to one or more projects in the solution (e.g. FirstModule, PointersAndReferences).
3. Set the project you want as the startup project, then build and run (F5 or Ctrl+F5).

Building the solution will compile the relevant C++ files; some projects use C++20 modules and the standard library as a module (`import std;`), so ensure your project is configured for C++20 or later.

## GitHub Pages and GitHub Classroom

- **GitHub Pages:** This mdBook can be built to static HTML and hosted on [Intro to CPP](https://cpp.sec665.io), so the guide is available online.
- **GitHub Classroom:** This series will be used with the GitHub Classroom with the classroom link here: [Cpp Class Invite Link](https://classroom.github.com/a/JwxMCL_i). The classroom is entirely optional but it will allow you to better participate and follow along with the exercises. The chapter order and project names in the book are chosen so that assignments can reference specific chapters and projects (e.g. “Complete the RAII project from Chapter 7”).

## How to use this book

Chapters are ordered from basic to advanced. Each chapter introduces concepts and shows short code snippets that illustrate one idea at a time. When a snippet comes from the companion code, the project path is noted so you can open and run the full program. We recommend reading in order and building the corresponding projects as you go.
