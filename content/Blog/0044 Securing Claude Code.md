---
title: "Securing Claude Code via Client Side & Server Side"
created: 2026-06-01
modified: 2026-06-01
tags: ["AI", "Claude", "Claude Code"]
draft: true    
---

## Introduction

Claude comes with Claude Code which is a product that allows developers to analyze and build applications through the terminal. It's also used by developers to automate repetitive tasks which allows them to focus on more important things such as optimization and architecture.

Claude Code can also read, write, and execute commands on the system therefore it's important to secure Claude Code from reading, writing, and executing specific things. 

## Claude Code Security

Inside the **Claude Code Organization Panel** there is a setting which allows us to restrict Claude Code from reading, writing, and executing specific things.

| Syntax      | Description                                                 |
| ----------- | ----------------------------------------------------------- |
| `Bash`      | Used to allow or deny Claude Code from executing a command. |
| `Read`      | Used to allow or deny Claude Code from reading a file.      |
| `Write`     | Used to allow or deny Claude Code from writing to a file.   |

All these options allows us to secure Claude Code from executing specific commands and from reading critical files which has API keys and much more information. Here is an example configuration for Claude Code:

```json title="managed-settings.json"
{
    "permissions": {
        "disableBypassPermissionsMode": "disable",
        "deny": [
            "Read(**/.env)",
            "Read(**/local-settings.json)",
            "Write(**/etc/passwd)",
            "Bash(sudo *)"
            "Bash(su *)",
            "Bash(curl *)",
            "Bash(git *)",
            "Bash(passwd *)",
            "Bash(chmod *)",
            "Bash(chown *)"
        ]
    }
}
```

So basically the managed-settigns.json file basically secures Claude Code by preventing it from reading files which has API keys and blocking execution of specific commands. The disableBypassPermissionsMode disallows the users from bypassing the security configuration set in managed-settings.json.

## Securing through Server-Side

Claude allows us to secure Claude Code from server-side through the managed-settings.json which is accessible with the following steps.

1. Go to **Claude Organization Settings**
2. Click on **Configure Managed Settings**
3. Use the example above to configure your own security.


## Securing through Client-Side (Windows)

Claude also allows us to secure Claude Code from client-side by adding the managed-settings.json file inside of a registry key.

1. Go to **Microsoft Intune**
2. Create a **Remediation Script**
3. Enter the Name and Description.
4. Use the following remediation script and detection script.
5. Click on Create.

## Securing through Client-Side (MacOS)

Claude also allows us to secure Claude Code from client-side on MacOS devices by adding the managed-settings.json fiel inside of a mobileconfig file which will be added into the MacOS MDM enforcement directory.

1. Go to Microsoft Intune.
2. Click on **Configuration Profile**
3. Enter the Name and Description.
4. Upload the following mobileconfig file.

## Conclusion

Antrophics the founders of Claude built a great tool which increases engineers productivity. However, as Security Engineers it's crucial for us to implement least privileges on these CLI tools as these comes with the capabilities to read, write, and execute on end-users systems to minimize the attack surface.

Fortunately for us Antrophics allows us to force security settings through both client-side and server-side which allows us to ensure that the attack surface is reduced. I would highly recommend to setup managed-settings.json for Claude Code to reduce the attack surface.