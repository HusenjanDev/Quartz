---
title: "Decode Me - Reverse Engineering Challenge"
created: 2025-11-11
modified: 2024-11-11
description: "The \"Decode Me\" challenge from crackmes.one consists of understanding basic assembly code such as loops, character replacement, and encoding. In this article I'll go through the technical details for solving the \"Decode Me\" challenge."
tags: ["RE", "IDA"]
draft: false
---

## Introduction

The [Decode Me Challenge](https://crackmes.one/crackme/69245c422d267f28f69b806e) from crackmes.one consists of understanding basic assembly code such as loops, character replacement, and encoding. I would highly recommend beginners who are interested in reverse engineering to try it out.

Anyway in this article I'll go through the technical details for the challenge to help you if you were struggling with challenge.

## Application Information

* **Application Name:** `decodeme.exe`
* **SHA-256:** `0D900E451D636A0E915FFEB35FC96C68AB3D14D055A8EAFFA8FD1E6E40533136`

## Reversing Application

The `main` function has the password in plaintext `Pa100-322-1L@101` and the password address is assinged to `RCX` register which will be used with the `transformChar` function.

![[0000 Decode-Me-Reverse-Engineering-Challenge-01.png]]

The `transformChar` functoin performs modifications to the plaintext password by looping through all the characters and replacing characters such as 1, 2, and 3 with special characters `_`, `>`, and `\`.

![[0000 Decode-Me-Reverse-Engineering-Challenge-02.png]]

The `byteEncoder` function performs additional modification after the characters 1, 2, and 3 has replaced by looping through all the characters and converting them into hexadecimal code.

![[0000 Decode-Me-Reverse-Engineering-Challenge-03.png]]

The `checker` function will remove characters such as `\` and `x` from the hexadecimal code string and from there it will check if the hexadecimal code string matches.

![[0000 Decode-Me-Reverse-Engineering-Challenge-04.png]]

The user will need to replace the characters inside `Pa100-322-1L@101` with special characters that are shown in above and from there enter that password to the application. If the password is correct the application will print `Yes!` otherwise the application will print `No!` which means we failed the challenge.

## Programming

Instead of manually modifing the password I decided to create a python script which will replace characters such as 1, 2, and 3 with the special characters `_`, `>`, and `\`.

```python title="solve.py"
def solve(password : str):
    password = password.replace("0", "_")
    password = password.replace("1", ">")
    password = password.replace("2", "\\")

    print("Plaintext Password: " + password)

if __name__ == "__main__":
    solve("Pa100-322-1L@101")
```

```powershell title="Output"
PS C:\Users\Student> .\decodeme.exe
Write password: Pa>__-3\\->L@>_>
Yes!
Press any key to continue . . .
```

I would highly recommend solving the challenges using a programming language as that will allow us to become more familiar with the programming language but also solve complex problems quicker in the future.

## Conclusion

The `decodeme.exe` was actually a really fun reverse engineering challenge and I would recommend beginners to try it out as it will help them with understanding reverse engineering more in-depth the way programmers obfuscates secret keys. Hopefully, the article helped you with solving the `decodeme.exe` challenge.