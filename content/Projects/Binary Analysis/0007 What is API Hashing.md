---
title: "What is API Hashing?"
created: 2026-02-20
modified: 2026-02-20
tags: ["RE"]
draft: true
---

## Information

API Hashing is a technique that is commonly used by threat actors to hide functions that is imported by malicious executable program. An example is when `LoadLibraryA`, `VirtualMemoryAlloc`, and `CreateThread` functions are part of the import table it's a strong indication that it's a malicious executable program.

## Understanding TEB & PEB

The way threat actors calls these functions without them showing up on the import table is by getting the `kernel32.dll` address from `TEB -> PEB -> Ldr -> InLoadOrderModuleList` and then accessing the `AddressOfFunctionsRVA`, `AddressOfNamesRVA`, and `AddressOfOrdinalsRVA` to find the function. However, instead of comparing function names we compare the hashed function names.

## Finding Kernel32.DLL

![[0007 What-is-API-Hashing.svg]]

## API Hashing

## Conclusion

