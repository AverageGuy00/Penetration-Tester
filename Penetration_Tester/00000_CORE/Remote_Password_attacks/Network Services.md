During our `penetration tests`, every `computer network` we encounter will have services installed to manage, edit, or create content. All these services are hosted using specific `permissions` and are assigned to specific users. Apart from `web applications`, these services include (but are not limited to) `FTP`, `SMB`, `NFS`, `IMAP/POP3`, `SSH`, `MySQL/MSSQL`, `RDP`, `WinRM`, `VNC`, `Telnet`, `SMTP`, and `LDAP`.

---

## WinRM

`WinRM` is `Microsoft's` implementation of the `WS-Management` protocol: a `remote-management protocol` built on `XML` web services using `SOAP`. It brokers communication between `WBEM` and `WMI`, which in turn can invoke `DCOM`. Because it exposes powerful management functionality, `WinRM` is disabled by default on `Windows 10`/`Windows 11` and must be explicitly enabled and configured before use.

For security, `WinRM` should be constrained by the environment — use `HTTPS` with proper `certificates` or restrict authentication methods to trusted ones. By default `WinRM` listens on TCP ports `5985` (HTTP) and `5986` (HTTPS). A useful tool for password-style attacks and protocol testing is `NetExec`, which also supports `SMB`, `LDAP`, `MSSQL`, and others; read its official docs before running it, because poking management services `without permission` is how you lose your job and possibly your weekend.

---

## NetExec

```bash
OathBornX@htb[/htb]$ sudo apt-get -y install netexec
```
#### NetExec Menu Options

Running the tool with the `-h` flag will show us general usage instructions and some options available to us.

```bash
OathBornX@htb[/htb]$ netexec -h

usage: netexec [-h] [--version] [-t THREADS] [--timeout TIMEOUT] [--jitter INTERVAL] [--verbose] [--debug] [--no-progress] [--log LOG] [-6] [--dns-server DNS_SERVER] [--dns-tcp]
               [--dns-timeout DNS_TIMEOUT]
               {nfs,ftp,ssh,winrm,smb,wmi,rdp,mssql,ldap,vnc} ...

     .   .
    .|   |.     _   _          _     _____
    ||   ||    | \ | |   ___  | |_  | ____| __  __   ___    ___
    \\( )//    |  \| |  / _ \ | __| |  _|   \ \/ /  / _ \  / __|
    .=[ ]=.    | |\  | |  __/ | |_  | |___   >  <  |  __/ | (__
   / /ॱ-ॱ\ \   |_| \_|  \___|  \__| |_____| /_/\_\  \___|  \___|
   ॱ \   / ॱ
     ॱ   ॱ

    The network execution tool
    Maintained as an open source project by @NeffIsBack, @MJHallenbeck, @_zblurx
    
    For documentation and usage examples, visit: https://www.netexec.wiki/

    Version : 1.3.0
    Codename: NeedForSpeed
    Commit  : Kali Linux
```

Note that we can specify a specific `protocol` and receive a more detailed help menu of all of the options available to us. `NetExec` currently supports `remote authentication` using `NFS, FTP, SSH, WinRM, SMB, WMI, RDP, MSSQL, LDAP, and VNC`.

```bash
OathBornX@htb[/htb]$ netexec smb -h

usage: netexec smb [-h] [--version] [-t THREADS] [--timeout TIMEOUT] [--jitter INTERVAL] [--verbose] [--debug] [--no-progress] [--log LOG] [-6] [--dns-server DNS_SERVER] [--dns-tcp]

<...SNIP>
```
#### NetExec Usage

The general format for using `NetExec` is as follows:

```bash
OathBornX@htb[/htb]$ netexec <proto> <target-IP> -u <user or userlist> -p <password or passwordlist>
```

As an example, this is what attacking a `WinRM` endpoint might look like:

```bash
OathBornX@htb[/htb]$ netexec winrm 10.129.42.197 -u user.list -p password.list

WINRM       10.129.42.197   5985   NONE             [*] None (name:10.129.42.197) (domain:None)
WINRM       10.129.42.197   5985   NONE             [*] http://10.129.42.197:5985/wsman
WINRM       10.129.42.197   5985   NONE             [+] None\user:password (Pwn3d!)
```

When you see `(Pwn3d!)` after logging in, it’s basically the red `flag `waving that you’ve got a shell or command execution on the target—your `brute-forced` user credentials worked and you’re in.

`Evil-WinRM` is the go-to tool here. It’s like the Swiss Army knife for `WinRM`, letting you easily interact with the Windows machine over that protocol—run commands, `upload/download` files, and `pivot` further. It streamlines what would otherwise be a painful, manual process of interacting with `WinRM` via PowerShell or other less-friendly interfaces.
#### Evil-WinRM Usage

```bash
OathBornX@htb[/htb]$ evil-winrm -i <target-IP> -u <username> -p <password>
```

```bash
OathBornX@htb[/htb]$ evil-winrm -i 10.129.42.197 -u user -p password

Evil-WinRM shell v3.3

Info: Establishing connection to remote endpoint

*Evil-WinRM* PS C:\Users\user\Documents>
```

---
## SSH

`SSH` is the secure way to remotely run commands or move files, usually over TCP port `22`. It uses three crypto layers to keep your connection locked down:

**`Symmetric encryption`**: Same key for locking and unlocking data. The catch? You need a secure way to share that key without someone else sniffing it out. That’s where the `Diffie-Hellman` key exchange kicks in—both `client` and `server` agree on a secret key without ever sending it directly. After that, they use fast `symmetric ciphers` like `AES`, `Blowfish`, or `3DES` to scramble traffic.

**`Asymmetric encryption`**: Two keys—`public key` (free to share) and `private key` (keep this locked down). The `server` uses the `public key` to challenge the `client`, which must prove it holds the matching `private key` by decrypting a message. If you lose your `private key` or it’s unprotected, anyone can waltz in without a password. This step authenticates the `client`.

**`Hashing`**: Think of it as a cryptographic fingerprint. `SSH` `hashes` data to verify messages haven’t been altered during transit. It’s a one-way street—you can’t reverse it. This ensures message authenticity and integrity.

All these layers combine to make `SSH` a fortress—efficient and secure for remote access. But remember: weak `private key` protection or sloppy key management is your Achilles’ heel.

