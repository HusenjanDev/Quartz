---
title: "Nexus - Reverse Engineering Challenge"
created: 2025-12-26
modified: 2025-12-26
tags: ["CRACKMES", "RE", "IDA"]
draft: true
---

## Introduction

The [Nexus! Reverse Engineering Challenge](https://crackmes.one/crackme/694af49f0c16072f40f5a379) is an really interesting challenge which consists of reading through the assmebly code to find secret key but finding the secret key is relatively easy. In this article I'll go through finding the secret key and deep dive into the way application encrypts the secret key.

## Application Information

* **Application Name:** `nexus-lite.exe`
* **SHA-256:** `05BFA131FEF54FFC3DEC074FD61009118E7B7880F50CC0262F7BA25020393557`

## Reverse Engineering

The secret key can be found inside the `main` function with the variable name `aNexusMasterKey`. 

![[0005 Nexus-Reverse-Engineering-Challenge-01.png]]

After running the `nexus-lite.exe` application and entering the secret key `NEXUS-MASTER-KEY-2025` the encrypted form of the string is shown. 

![[0005 Nexus-Reverse-Engineering-Challenge-02.png]]

I becmae interested the way the application encrypts the secret key therefore I decided to dig deeper into the application code and after researching for a bit I found the function `sub_140001800`.

![[0005 Nexus-Reverse-Engineering-Challenge-03.png]]

The `sub_140001800` is responsible for handling all the different encryptions for the secret key.

![[0005 Nexus-Reverse-Engineering-Challenge-04.png]]

The `sub_140001630`, `sub_140001470`, and `sub_1400012A0` functions are responsible for encrypting the `NEXUS-MASTER-KEY-2025` secret key.

![[0005 Nexus-Reverse-Engineering-Challenge-05.png]]

The `sub_140001630` is performing XOR encryption on the `NEXUS-MASTER-KEY-2025` string using a single character as `@` as a key.

![[0005 Nexus-Reverse-Engineering-Challenge-06.png]]

The `sub_140001470` function was a bit more difficult to understand but after researching for a while I figred out that the function is performing calculation on all characters that are lowercase using the calculation `((array[i]- 84) % 26 + 97)`.


![[0005 Nexus-Reverse-Engineering-Challenge-07.png]]

The `sub_1400012A0` performs the base64 encoding to the string to add extra complexity to decrypt the secret key.

## Solution

After finding out the application performs XOR encryption using `@` character then performs a complex calculation on lowercase characters `((array[i]- 84) % 26 + 97)` and then base64 encodes the function, I decided to build the following script which allows us to encode and decode the string.

```python title="main.py"
import base64

def encrypt(string):
    # Variables
    x = []
    b = ""

    # Performing XOR encryption/decryption
    for i in range(0, len(string)):
        x.append(ord(string[i]) ^ ord('@'))
    
    # Converting hex to ascii
    for i in range(0, len(x)):
        x[i] = chr(x[i])

    # Performing calculation on lowecase characters
    for i in range(0, len(x)):
        if ord(x[i]) > 60:
            x[i] = chr((ord(x[i]) - 84) % 26 + 97)

    # Base64 Decoding
    b = "".join(x)
    return base64.b64encode(b.encode("UTF-8")).decode()

def decrypt(string):
    # Base64 Decoding
    b64 = base64.b64decode(string)
    b64 = b64.decode("UTF-8")

    # Variables
    a = []
    x = []
    r = ""

    # Converting each character to hex character
    for i in range(0, len(b64)):
        a.append(hex(ord(b64[i])))
    
    # Performing calculation on lowecase characters
    for i in range(0, len(a)):
        if int(a[i], 16) > 60:
            a[i] = hex(((int(a[i], 16) - 84) % 26 + 97 ))
    
    # Performing XOR encryption/decryption
    for i in range(0, len(a)):
        x.append(int(a[i], 16) ^ ord('@'))
    
    # Displaying text in plaintext
    for i in range(0, len(x)):
        r += chr(x[i])
    
    return r

print(encrypt("NEXUS-MASTER-KEY-2025"))
print(decrypt("DgUYFRN6DQETFAUSegsFGXplY2Vo"))
```

```shell title="Output"
DgUYFRN6DQETFAUSegsFGXplY2Vo
NEXUS-MASTER-KEY-2025
```

## Conclusion