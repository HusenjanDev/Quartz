---
title: "What is TEB and PEB?"
created: 2026-01-10
modified: 2026-01-10
tags: ["EXPDEV", "RE"]
draft: true
---

## Introduction

The Thread Environment Block (TEB) and Process Environment Block (PEB) are data structures that contains information about the thread and process. The TEB and PEB is commonly accessed by developers and threat actors to obtain critical informations about the running process.

## Thread Environment Block

The Thread Environment Block (TEB) is also known as Thread Information Block (TIB) is a the `_TEB` data structure in 32-bit and 64-bit systems that stores information about the currently running thread. The TEB data structure contains pointers to Process Environment Block, Structured Exception Handler, and much more which is available at [Wikipedia](https://en.wikipedia.org/wiki/Win32_Thread_Information_Block) 

* In 32-bit systems the TEB can be accessed through `FS:[]0x18`.
* In 64-bit systems the TEB can be accessed through `GS:[0x30]`.

The TEB is commonly accessed to obtain the `ProcessEnvironmentBlock` (PEB) pointer to obtain the base address of loaded modules in the current running process. 

## Process Environment Block

The Process Environment Block (PEB) is the `_PEB` data structure that contains information about the process itself. The PEB contains information's such as BeingDebugged, Ldr, Process Parameters, OSBuiidNumber and much more which is available at [Wikipedia](https://en.wikipedia.org/wiki/Process_Environment_Block).

* In 32-bit systems the PEB can be accessed through `FS:[0x30]`.
* In 32-bit systems the PEB can be accessed through `FS:[0x60]`.

The PEB is accessed to access the `Ldr` pointer to obtain the base addresses of all the loaded modules such as their base addess, names, and etc... 

## LDR

The Process Environment Block (PEB) has a pointer to `Ldr` which is the `_PEB_LDR_DATA` data structure that will allow us to locate modules inside the our process.

* **InLoadOrderModuleList** : The list is sorted by the order the modules were loaded.
* **InMemoryOrderModuleList** : The list sorts the modules by the base address.
* **InInitializationOrderModuleList** The list is sorted by the order which the modules were initalized.

The main difference between these different pointers is the way modules are sorted, this doesn't matter much for us as we enumerate through them.

## WinDBG Traversal

In this section I'll go through analyzing the TEB, PEB, LDR and the way to locate modules.

1. Attach `Windbg` to any process.

2. Use the command `dt ntdll!_TEB @$TEB` to access TEB.
![[0006 What-is-TEB-and-PEB-01.png]]

3. Use the command `dt ntdll!_PEB <PEB-ADDRESS>` to access PEB.
![[0006 What-is-TEB-and-PEB-02.png]]

4. Use the command `dt ntdll!_PEB_LDR_DATA <ADDRESS>` to access the data structure which consists of different stages of loaded modules.
![[0006 What-is-TEB-and-PEB-03.png]]

5. Use the command `dt ntdll!_LIST_ENTRY <ADDRESS>` to obtain address of a module.
![[0006 What-is-TEB-and-PEB-04.png]]

6. Use the command `dt ntdll!_LDR_DATA_TABLE_ENTRY <ADDRESS>` to obtain information about the module.
![[0006 What-is-TEB-and-PEB-05.png]]

## Traversing with Code

This section of the article will focus on developing an terminal application using Rust to traverse through the `InLoadOrderModuleList`  to obtain the data inside `DllBase` and `DllBaseName`.

```rust title="main.rs"
use std::{os::raw::c_void};

#[repr(C)]
struct _TEB {
    Reserved : [u8; 0x60],
    ProcessEnvironmentBlock  : *mut _PEB
}

#[repr(C)]
struct _PEB {
    Reserved : [u8; 0x10],
    ImageBaseAddress: *mut c_void,
    Ldr : *mut _PEB_LDR_DATA
}

#[repr(C)]
struct _PEB_LDR_DATA {
    Reserved: [u8; 0x10],
    InLoadOrderModuleList : _LIST_ENTRY,
    InMemoryOrderModuleList : _LIST_ENTRY,
    InInitializationOrderModuleList: _LIST_ENTRY
}

#[repr(C)]
struct _LIST_ENTRY {
    Flink : *mut _LDR_DATA_TABLE_ENTRY,
    Blink : *mut _LDR_DATA_TABLE_ENTRY
}

#[repr(C)]
struct _LDR_DATA_TABLE_ENTRY {
    InLoadOrderLinks : _LIST_ENTRY,
    InMemoryOrderLinks : _LIST_ENTRY,
    InInitializationOrderLinks : _LIST_ENTRY,
    DllBase : *mut c_void,
    EntryPoint : *mut c_void,
    SizeOfImage : u32,
    Reserved1 : u32,
    FullDllName : _UNICODE_STRING,
    BaseDllName : _UNICODE_STRING
}

#[repr(C)]
struct _UNICODE_STRING {
    Length : u16,
    MaximumLength : u16,
    Reserve: u32,
    Buffer : *const u16
}

fn main() {
    let teb : *mut _TEB;

    unsafe {
        std::arch::asm!(
            "mov {teb}, gs:[0x30]",
            teb = out(reg) teb,
            options(nostack, nomem)
        );

        println!("_TEB Address: {:?}", teb);
        println!("_TEB -> _PEB Address: {:?}", (*teb).ProcessEnvironmentBlock);
        println!("_TEB -> _PEB -> _PEB_LDR_DATA Address: {:?}", (*teb).ProcessEnvironmentBlock);
        println!("_TEB -> _PEB -> _PEB_LDR_DATA -> InLoadOrderModuleList Address: {:?}", (*(*teb).ProcessEnvironmentBlock).Ldr);

        let mut begin_list = (*(*(*teb).ProcessEnvironmentBlock).Ldr).InLoadOrderModuleList.Flink;
        let end_list = (*(*(*teb).ProcessEnvironmentBlock).Ldr).InLoadOrderModuleList.Blink;

        while begin_list != end_list {
            let base_dll_name = &(*begin_list).BaseDllName;
            let len_u16 = (base_dll_name.Length / 2) as usize;
            let wide : &[u16] = std::slice::from_raw_parts(base_dll_name.Buffer, len_u16);
            let s = String::from_utf16_lossy(wide);

            println!("Module Name: {:?}\t\tModule Location: {:?}", s, (*begin_list).DllBase);

            begin_list = (*begin_list).InLoadOrderLinks.Flink;
        }
    }
}
```

```shell title="Output"
> cargo run
_TEB Address: 0x72ab8db000
_TEB -> _PEB Address: 0x72ab8da000
_TEB -> _PEB -> _PEB_LDR_DATA Address: 0x72ab8da000
_TEB -> _PEB -> _PEB_LDR_DATA -> InLoadOrderModuleList Address: 0x7ffe328728e0
Module Name: "PEB-Enum.exe"             Module Location: 0x7ff74d7a0000
Module Name: "ntdll.dll"                Module Location: 0x7ffe326a0000
Module Name: "KERNEL32.DLL"             Module Location: 0x7ffe312d0000
Module Name: "KERNELBASE.dll"           Module Location: 0x7ffe2ff90000
Module Name: "ucrtbase.dll"             Module Location: 0x7ffe2f820000
```

All the Rust code does is obtaining the `TEB` pointer address by using `GS:[0x30]` and from there it's using the data structures declared by us to access the `InLoadOrderModuleList` to locate the different modules.

## Conclusion

Understanding the TEB and PEB can be difficult as it requires us to understand the way structures, pointers, and offsets works. I would recommend getting hands on experience by programming an application which enumerates through the module list using the TEb. This will help with increasing your understanding about the way TEB and PEB works.