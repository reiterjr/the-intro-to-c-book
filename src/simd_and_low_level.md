# SIMD intrinsics, hashing, and low-level extras

This chapter ties together **`Part10-TheEnd`**, **`IntrinsicXOR`**, and **`FreeEXE`**—all **optional** and **more advanced** than the core 1–9 track.

> [!warning]
> These examples use **SSE/AVX intrinsics** (`immintrin.h`, `emmintrin.h`, etc.). You need a CPU and compiler flags that support the instruction set you use, and you must understand **alignment and aliasing** before copying patterns into security-sensitive code.

## `Part10-TheEnd`: XOR-fold hash with SSE

**`IntrinsichHasher`** loads 8 wide characters at a time into **`__m128i`**, XORs into an accumulator, then folds the 128-bit state into a **`DWORD`**. Remaining characters are XOR’d in a scalar loop. **`start`** builds a **`UNICODE_STRING`** for **`KERNELBASE.dll`** from a **wide character array** (not a single literal) to illustrate byte-level control.

**Project:** `IntroToC/Part10-TheEnd`.

## `IntrinsicXOR`: encrypting a buffer in 16-byte blocks

**`IntrinsicXOR/main.c`** allocates a buffer, XORs **`PlainText`** against a 16-byte key using **`_mm_loadu_si128`** / **`_mm_xor_si128`**, and prints hex bytes—useful for seeing how intrinsics map to **`xmm`** registers in a debugger.

**Project:** `IntroToC/IntrinsicXOR`.

## `FreeEXE`: shellcode as a byte array

**`FreeEXE/main.c`** contains large **`unsigned char hexData[...]`** arrays (and comments) representing extracted machine code—**PEB walking** and hashing at a very low level. Treat this as **reference / lab support**, not as a template for arbitrary networked programs.

> [!caution]
> Running or adapting **shellcode** and **process-memory tricks** can violate policy or law outside a controlled class VM. Only use this material in environments your instructor explicitly authorizes.

**Project:** `IntroToC/FreeEXE`.

## `Part10` stub

**`Part10/main.c`** currently defines a trivial **`boo`** function—placeholder for future extension. **`Needle/main.c`** is mostly commented **010 Editor** hex dumps; skim if your course points you there.

**Projects:** `IntroToC/Part10`, `IntroToC/Needle`.
