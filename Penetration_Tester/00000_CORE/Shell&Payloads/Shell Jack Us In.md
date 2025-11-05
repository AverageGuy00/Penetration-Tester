A `shell` is a program that gives you an interface to send instructions to a system and see the text output. Examples include `Bash`, `Zsh`, `cmd`, and `PowerShell`.

## Why Get a Shell?

A `shell` gives direct hands-on access to the `OS`, built-in `system` commands, and the `file system`. With a shell you can enumerate the host, find privilege-escalation vectors, `pivot`, and `transfer files`. Without a shell your actions are extremely limited.

A shell also makes persistence and automation far easier, which buys time to run tools, collect evidence, and `exfiltrate` sensitive data. Command-line `CLI` access is usually quieter than a graphical session (`VNC`/`RDP`), faster to work in, and simpler to script — all of which help you move and act with less noise.

We treat shells in this module from these practical angles:

#### Computing  
A text-based userland environment used to run admin tasks and send instructions on a PC. Examples: `Bash`, `Zsh`, `cmd`, `PowerShell`.
#### Exploitation & Security  
A `shell` is frequently the result of exploiting a vulnerability or bypassing defenses to gain interactive access to a host. Example: triggering `EternalBlue` on a Windows host to get a `cmd` prompt remotely.
#### Web  
A `web shell` behaves like a standard shell but is delivered via a web vulnerability (typically an `upload`/file inclusion) that lets an attacker run commands, read/write files, and manipulate the underlying host. Access is usually done by invoking the script through a `browser` request.

---
## Payloads Deliver us Shells

Within the IT industry as a whole, a `payload` can be defined in a few different ways:

- `Networking`: The encapsulated data portion of a packet traversing modern computer networks.
- `Basic Computing`: A payload is the portion of an instruction set that defines the action to be taken. Headers and protocol information removed.
- `Programming`: The data portion referenced or carried by the programming language instruction.
- `Exploitation & Security`: A payload is `code` crafted with the intent to exploit a vulnerability on a computer system. The term payload can describe various types of malware, including but not limited to ransomware.
---
# CAT5 Security's Engagement Preparation

#### Shell basics

- Replicate being able to get a bind and reverse shell.
- Bind Shell on Linux host.
- Reverse Shell on Windows Host.

#### Payload Basics

- Demonstrate launching a payload from MSF.
- Demonstrate searching and building a payload from PoC on ExploitDB.
- Demonstrate knowledge of payload creation.

#### Getting a Shell on Windows

- Using the recon results provided, craft or use a payload that will exploit the host and provide a shell back.

#### Getting a Shell on Linux

- Using the recon results provided, craft or use a payload to exploit the host and establish a shell session.

#### Landing a Web Shell

- Demonstrate knowledge of web shells and common web applications by identifying a common web application and its corresponding language.
- Using the recon results provided, deploy a payload that will provide shell access from your browser.

#### Spotting a Shell or Payload

- Detect the presence of a payload or interactive shell on a host by analyzing relevant information provided.

#### Final Challenge

- Utilize knowledge gained from the previous sections to select, craft, and deploy a payload to access the provided hosts. Once a shell has been acquired, grab the requested information to answer the challenge questions.