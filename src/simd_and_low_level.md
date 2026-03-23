# SIMD intrinsics, hashing, and low-level extras

This chapter is **optional** and **more advanced** than the core track. Topics include **SSE-style** hashing of wide strings, **SIMD XOR** of buffers, and (if assigned) **shellcode** or **raw bytes** embedded in C—only in environments your instructor authorizes.

> [!WARNING]
> These examples use **SSE/AVX intrinsics** (`immintrin.h`, `emmintrin.h`, etc.). You need CPU + compiler support for the instruction set you use, and you must understand **alignment** and **strict aliasing** before using intrinsics in real code.

## XOR-fold hash with SSE (wide string)

Pattern:

1. Initialize **`__m128i hash`** to zero.
2. For each 8-**WCHAR** chunk (16 bytes), **`_mm_loadu_si128`**, **`_mm_xor_si128`** into **`hash`**.
3. **`_mm_storeu_si128`** to a **`DWORD[4]`**, XOR the four lanes into one **`DWORD`**.
4. XOR remaining **`WCHAR`s** in a scalar loop.

Build a **`UNICODE_STRING`** for a module name using a **wide char array** if you need per-character control (e.g. `'K','E',...,'L','L', UNICODE_NULL`).

## `IntrinsicXOR`: 16-byte blocks

Load a 16-byte **key** into **`__m128i`**. For each full block of plaintext: **`_mm_loadu_si128`**, **`_mm_xor_si128`**, **`_mm_storeu_si128`**. Handle a **tail** smaller than 16 bytes with a byte loop. Print ciphertext as **hex** for debugging.

## Shellcode / `hexData` arrays (assigned only)

Some courses embed **machine code** as **`unsigned char hexData[] = { 0x48, ... };** and jump into it or study it in a debugger. That belongs only in **controlled** lab VMs.

> [!CAUTION]
> Running or adapting **shellcode** and low-level **process memory** tricks can violate policy or law outside an authorized class environment. Follow your instructor’s rules only.

## Placeholder milestones

If your template includes a stub function (e.g. a single **`boo()`**), replace it when the rubric advances—or use it as a scratch entry point for **intrinsic** experiments.

## Implement

1. Only if assigned: add **SIMD** hashing and/or **XOR** demo **`.c`** files to **your template**, enable required **compiler intrinsics** flags.
2. **Build**, run under a debugger if asked, **commit**, and **push**.
