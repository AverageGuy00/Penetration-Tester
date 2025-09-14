
# Remote Desktop Protocol (RDP)

RDP is a proprietary Microsoft protocol that allows a user to connect to a remote system over a network and access a full graphical user interface (GUI).

**Key Points:**

- Uses **port 3389** by default.
- Allows remote users to operate a computer as if physically present.
- Commonly used by system administrators for remote management.
- Can be used by employees to access work systems remotely via **VPN**.

**RDP Clients / Connections:**

- **Windows:** `mstsc`
- **Linux:** `xfreerdp / rdesktop`
- **macOS:** Microsoft Remote Desktop App

Connect to RDP using Windows

```Powershell
mstsc /v:<target_ip>:3389
```

Connect to RDP using Linux

```ruby
xfreerdp /v:<target_ip> /u:<username> /p:<password>
```

---

# Command Prompt (CMD)

CMD (`cmd.exe`) is the Windows command-line interpreter used to enter and execute commands. It can perform one-off tasks or complex operations via scripts and batch files.

```nginx
#View IP configuration
ipconfig

#View active network connections
netstat -an

#Ping a host
ping <hostname_or_ip>

#List directory contents
dir

#Change Directory
cd <path>

#Delete a file
del <filename>

#Copy a file
copy <source> <destination
```

---

# Powershell

## Basics

|        Command         |         Description         |          Example          |
| :--------------------: | :-------------------------: | :-----------------------: |
|       `Get-Help`       |   Show help for a command   |  `Get-Help Get-Process`   |
|     `Get-Command`      | List all available commands |  `Get-Command *service*`  |
| `Get-Location` / `pwd` |   Show current directory    |      `Get-Location`       |
| `Set-Location` / `cd`  |      Change directory       | `Set-Location C:\Windows` |
|        `whoami`        |      Show current user      |         `whoami`          |
## File & Folder management

|         Command          |          Description           |                        Example                        |
| :----------------------: | :----------------------------: | :---------------------------------------------------: |
|  `Get-ChildItem` / `ls`  |       List files/folders       |               `Get-ChildItem C:\Users`                |
| `Get-ChildItem -Recurse` | Recursively list files/folders |           `Get-ChildItem C:\Users -Recurse`           |
|    `Copy-Item` / `cp`    |       Copy files/folders       |      `Copy-Item C:\file.txt D:\backup\file.txt`       |
|    `Move-Item` / `mv`    |   Move/rename files/folders    |        `Move-Item C:\file.txt D:\file_old.txt`        |
|   `Remove-Item` / `rm`   |      Delete files/folders      |           `Remove-Item C:\temp\* -Recurse`            |
|  `Get-Content` / `cat`   |      Display file content      | `Get-Content C:\Users\htb-student\Desktop\secret.txt` |
| `Select-String` / `grep` |    Search text inside files    |    `Select-String -Path *.txt -Pattern "password"`    |
## Process & Service management

|       Command        |       Description       |               Example               |
| :------------------: | :---------------------: | :---------------------------------: |
| `Get-Process` / `ps` | List running processes  |            `Get-Process`            |
|    `Stop-Process`    |     Kill a process      | `Stop-Process -Name notepad -Force` |
|   `Start-Process`    | Start a program/process |     `Start-Process notepad.exe`     |
|    `Get-Service`     |  List Windows services  |            `Get-Service`            |
|   `Start-Service`    |     Start a service     |   `Start-Service -Name wuauserv`    |
|    `Stop-Service`    |     Stop a service      |    `Stop-Service -Name wuauserv`    |
|  `Restart-Service`   |    Restart a service    |  `Restart-Service -Name wuauserv`   |
## Networking

|       Command       |       Description        |                                  Example                                  |
| :-----------------: | :----------------------: | :-----------------------------------------------------------------------: |
| `Get-NetIPAddress`  |  Show IP configuration   |                            `Get-NetIPAddress`                             |
|  `Test-Connection`  |       Ping a host        |                         `Test-Connection 8.8.8.8`                         |
| `Invoke-WebRequest` | Download a file from web | `Invoke-WebRequest -Uri http://example.com/file.txt -OutFile C:\file.txt` |
|  `Invoke-Command`   | Execute command remotely |       `Invoke-Command -ComputerName TARGET -ScriptBlock { whoami }`       |
## Users & Permissions

|             Command             |          Description           |                    Example                     |
| :-----------------------------: | :----------------------------: | :--------------------------------------------: |
|           `net user`            |           List users           |                   `net user`                   |
| `net localgroup administrators` |        List admin users        |        `net localgroup administrators`         |
|            `Get-ACL`            |  View file/folder permissions  |              `Get-ACL C:\Windows`              |
|            `Set-ACL`            | Modify file/folder permissions | `$acl = Get-ACL C:\test; Set-Acl C:\test $acl` |
|            `icacls`             |    Modify NTFS permissions     |         `icacls C:\Users /grant joe:F`         |
## Event logs

|    Command     |       Description       |                    Example                     |
| :------------: | :---------------------: | :--------------------------------------------: |
| `Get-EventLog` | View classic event logs |   `Get-EventLog -LogName System -Newest 50`    |
| `Get-WinEvent` | View modern event logs  | `Get-WinEvent -LogName Security -MaxEvents 50` |

---

# Execution Policy

Sometimes we will find that we are unable to run scripts on a system. This is due to a security feature called the `execution policy`, which attempts to prevent the execution of malicious scripts. The possible policies are:

|     Policy     |                                                                                                                           Description                                                                                                                            |
| :------------: | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|  `AllSigned`   | All scripts can run, but a trusted publisher must sign scripts and configuration files. This includes both remote and local scripts. We receive a prompt before running scripts signed by publishers that we have not yet listed as either trusted or untrusted. |
|    `Bypass`    |                                                                                   No scripts or configuration files are blocked, and the user receives no warnings or prompts.                                                                                   |
|   `Default`    |                                                                    This sets the default execution policy, `Restricted` for Windows desktop machines and `RemoteSigned` for Windows servers.                                                                     |
| `RemoteSigned` |                                           Scripts can run but requires a digital signature on scripts that are downloaded from the internet. Digital signatures are not required for scripts that are written locally.                                           |
|  `Restricted`  |                       This allows individual commands but does not allow scripts to be run. All script file types, including configuration files (`.ps1xml`), module script files (`.psm1`), and PowerShell profiles (`.ps1`) are blocked.                       |
|  `Undefined`   |                                          No execution policy is set for the current scope. If the execution policy for ALL scopes is set to undefined, then the default execution policy of `Restricted` will be used.                                           |
| `Unrestricted` |                 This is the default execution policy for non-Windows computers, and it cannot be changed. This policy allows for unsigned scripts to be run but warns the user before running scripts that are not from the local intranet zone.                 |

---

# Microsoft Management Console (MMC)

**Overview**

- MMC allows grouping of **snap-ins** (administrative tools) to manage hardware, software, and network components.
- Available on all Windows versions since **Windows Server 2000**.
- Enables creation of **custom consoles** for managing multiple services.
- Supports **local and remote system management**.

**Adding Snap-ins**

- Navigate: `File → Add or Remove Snap-ins`.
- Select desired snap-ins from the list (e.g., ActiveX Control, Services, Event Viewer).
- Choose whether to manage **local computer** or **remote system**.

**Using Snap-ins**

- Added snap-ins appear in the left-hand pane of MMC.
- Allows direct management of selected administrative tasks.
- Custom consoles can be saved as **.msc files** for future use.

**Saving and Loading Consoles**

- Saved .msc files are stored by default in **Windows Administrative Tools** folder under Start menu.
- Open saved consoles anytime by double-clicking the **.msc file**.

**Notes / Best Practices**

- Useful for **centralized system administration**.
- Can create multiple views tailored for specific management tasks.
- Good for managing servers and workstations across a network without repeated setup.

---

# Windows Subsystem for Linux (WSL)

**Definition / Overview**

- WSL is a **compatibility layer for running Linux binaries natively on Windows**.
- It allows Windows users to interact with a **full Linux environment**, including file systems, utilities, and software, without using a virtual machine.  
- WSL 2 introduced a **real Linux kernel** and improved performance, making it closer to a native Linux experience.
- Provides **seamless integration between Windows and Linux**, allowing access to Windows drives and the execution of Linux tools alongside Windows applications.
- Primarily designed for developers and power users who need **Linux functionality on a Windows host**, but also useful for system administration, scripting, and penetration testing workflows.