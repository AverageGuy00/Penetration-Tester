The diversity of `Linux` distributions means remote management tooling is ubiquitous across servers. When a field technician encounters an error, effective troubleshooting often requires direct access to the remote host rather than a phone call. Remote management preserves the working environment and speeds incident resolution.

Common `protocols`, `servers`, and `applications` for remote administration are widely exposed on public-facing hosts and therefore represent high-value targets during assessments. Misconfiguration of these services frequently yields straightforward access for `penetration testers`. Familiarity with the primary remote-management stack and common deployment mistakes is essential for efficient testing and rapid exploitation when authorized.

---

# SSH

`Secure Shell (SSH)` establishes encrypted communication between two systems over insecure networks using the default port `TCP/22`. This prevents interception and exposure of sensitive data. SSH servers can restrict access to approved clients only. The protocol is cross-platform, running natively on `Linux` and `macOS`, and available on `Windows` through compatible clients.

The widely deployed `OpenSSH` implementation—originating from `OpenBSD`—is an open-source fork of the original commercial `SSH` from SSH Communication Security. Two protocol versions exist: `SSH-1` and `SSH-2`. The latter improves encryption, stability, and overall security, addressing flaws like `man-in-the-middle (MITM)` vulnerabilities present in `SSH-1`.

When managing a remote host, access can be established through the command line or a graphical interface. The `SSH` protocol supports command execution, file transfer, and `port forwarding` across an encrypted channel. Connection requires successful authentication, and `OpenSSH` supports six primary methods:

- Password authentication  
- Public-key authentication  
- Host-based authentication  
- Keyboard authentication  
- Challenge-response authentication  
- GSSAPI authentication
---

# Public Key Authentication

In a first step, the `SSH server` and `SSH client` authenticate themselves to each other. The `server` sends its `public host key` to the `client`, which the client uses to verify the server’s identity. Only when contact is first established is there a risk of a third party performing a `man-in-the-middle` attack and intercepting the connection. A `host key` cannot be imitated because it is a unique `public-private key pair`, and an attacker cannot forge the `private key` signature without access to it—assuming the client properly verifies the `public key` against a trusted source.

After `server authentication`, the `client` must also prove to the `server` that it has access authorization. The `SSH server` already holds the `encrypted hash` of the user’s password. Because of this, users must enter their password every time they log on to another `server` within the same session. To solve this inconvenience, `public-key authentication` is used as an alternative method for `client-side authentication`.

The `private key` is created locally on the user’s machine and secured with a `passphrase` that is longer than a typical password. The `private key` remains stored only on the user’s system and is never shared. When initiating an `SSH connection`, the user enters the `passphrase` to unlock access to the `private key`.

`Public keys` are stored on the `server`. The `server` generates a cryptographic challenge using the `client’s public key` and sends it to the `client`. The `client` then decrypts the challenge using its `private key`, sends back the correct response, and thereby proves its legitimacy.

Once authenticated, users only need to enter the `passphrase` once to connect to multiple `servers` during the same session. When the session ends, logging out from the `local machine` ensures that no unauthorized party can use the unlocked `private key` to connect to any `SSH server`.

## Default Configuration

The [sshd_config](https://www.ssh.com/academy/ssh/sshd_config) file, responsible for the OpenSSH server, has only a few of the settings configured by default. However, the default configuration includes X11 forwarding, which contained a command injection vulnerability in version 7.2p1 of OpenSSH in 2016. Nevertheless, we do not need a GUI to manage our servers.

```bash
OathBornX@htb[/htb]$ cat /etc/ssh/sshd_config  | grep -v "#" | sed -r '/^\s*$/d'

Include /etc/ssh/sshd_config.d/*.conf
ChallengeResponseAuthentication no
UsePAM yes
X11Forwarding yes
PrintMotd no
AcceptEnv LANG LC_*
Subsystem       sftp    /usr/lib/openssh/sftp-server
```

---

## Dangerous Settings

Despite the `SSH protocol` being one of the most secure `protocols` available today, some misconfigurations can still make the `SSH server vulnerable` to easy-to-execute attacks. Let us take a look at the following settings:

|**Setting**|**Description**|
|---|---|
|`PasswordAuthentication yes`|Allows password-based authentication.|
|`PermitEmptyPasswords yes`|Allows the use of empty passwords.|
|`PermitRootLogin yes`|Allows to log in as the root user.|
|`Protocol 1`|Uses an outdated version of encryption.|
|`X11Forwarding yes`|Allows X11 forwarding for GUI applications.|
|`AllowTcpForwarding yes`|Allows forwarding of TCP ports.|
|`PermitTunnel`|Allows tunneling.|
|`DebianBanner yes`|Displays a specific banner when logging in.|
Allowing `password authentication` enables online `brute-force` attempts against known usernames. Attackers use multiple approaches: `credential stuffing` with leaked username/password pairs, `dictionary attacks` with common passwords, `password spray` to avoid lockouts, and hybrid or `rule-based mutation` to transform base words into likely variants. Advanced guessing can use `Markov` or `probabilistic` models and targeted wordlists derived from reconnaissance. These mutation rules and patterns dramatically increase success rates by generating realistic password variants from common bases.

---

## Footprinting the Service

One useful tool for fingerprinting an `SSH` server is `ssh-audit`; it inspects both `client-side` and `server-side` configuration and reports supported `encryption algorithms`, `key exchange` methods, and `host/key` metadata. The output reveals weak or deprecated algorithms that can be targeted later at the `cryptographic` level to exploit the `server` or `client`.
#### SSH-Audit

```bash
OathBornX@htb[/htb]$ git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit
OathBornX@htb[/htb]$ ./ssh-audit.py 10.129.14.132

# general
(gen) banner: SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.3
(gen) software: OpenSSH 8.2p1
(gen) compatibility: OpenSSH 7.4+, Dropbear SSH 2018.76+
(gen) compression: enabled (zlib@openssh.com)                                   

# key exchange algorithms
(kex) curve25519-sha256                     -- [info] available since OpenSSH 7.4, Dropbear SSH 2018.76                            
(kex) curve25519-sha256@libssh.org          -- [info] available since OpenSSH 6.5, Dropbear SSH 2013.62
(kex) ecdh-sha2-nistp256                    -- [fail] using weak elliptic curves
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(kex) ecdh-sha2-nistp384                    -- [fail] using weak elliptic curves
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(kex) ecdh-sha2-nistp521                    -- [fail] using weak elliptic curves
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(kex) diffie-hellman-group-exchange-sha256 (2048-bit) -- [info] available since OpenSSH 4.4
(kex) diffie-hellman-group16-sha512         -- [info] available since OpenSSH 7.3, Dropbear SSH 2016.73
(kex) diffie-hellman-group18-sha512         -- [info] available since OpenSSH 7.3
(kex) diffie-hellman-group14-sha256         -- [info] available since OpenSSH 7.3, Dropbear SSH 2016.73

# host-key algorithms
(key) rsa-sha2-512 (3072-bit)               -- [info] available since OpenSSH 7.2
(key) rsa-sha2-256 (3072-bit)               -- [info] available since OpenSSH 7.2
(key) ssh-rsa (3072-bit)                    -- [fail] using weak hashing algorithm
                                            `- [info] available since OpenSSH 2.5.0, Dropbear SSH 0.28
                                            `- [info] a future deprecation notice has been issued in OpenSSH 8.2: https://www.openssh.com/txt/release-8.2
(key) ecdsa-sha2-nistp256                   -- [fail] using weak elliptic curves
                                            `- [warn] using weak random number generator could reveal the key
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(key) ssh-ed25519                           -- [info] available since OpenSSH 6.5
...SNIP...
```

The first lines of the `ssh-audit` output reveal the `banner` and the `OpenSSH` version, which can indicate known issues such as `CVE-2020-14145` that enable `Man-In-The-Middle` attacks during initial connection setup. The detailed connection setup section exposes supported `key-exchange` algorithms, `ciphers`, and `MACs`, and it lists permitted `authentication methods` the server advertises. These details let an assessor identify deprecated algorithms or misconfigurations to target at the cryptographic level or to tailor authentication-focused attacks.

---

#### Change Authentication Method

```bash
OathBornX@htb[/htb]$ ssh -v cry0l1t3@10.129.14.132

OpenSSH_8.2p1 Ubuntu-4ubuntu0.3, OpenSSL 1.1.1f  31 Mar 2020
debug1: Reading configuration data /etc/ssh/ssh_config 
...SNIP...
debug1: Authentications that can continue: publickey,password,keyboard-interactive
```

For potential brute-force attacks, we can specify the authentication method with the SSH client option `PreferredAuthentications`.

```bash
OathBornX@htb[/htb]$ ssh -v cry0l1t3@10.129.14.132 -o PreferredAuthentications=password

OpenSSH_8.2p1 Ubuntu-4ubuntu0.3, OpenSSL 1.1.1f  31 Mar 2020
debug1: Reading configuration data /etc/ssh/ssh_config
...SNIP...
debug1: Authentications that can continue: publickey,password,keyboard-interactive
debug1: Next authentication method: password

cry0l1t3@10.129.14.132's password:
```

---

# Rsync

**`Rsync` is a fast and efficient file transfer tool used for both local and remote copying. It supports synchronization between hosts using its `delta-transfer algorithm`, which minimizes bandwidth by sending only file differences rather than full copies. This makes it highly effective for `backups` and `mirroring` operations.

The tool determines which files to transfer by comparing `file size` and `last modified timestamps`. By default, it listens on `TCP port 873` but can also operate securely by tunneling through `SSH`, leveraging the encryption and authentication mechanisms of an existing `SSH server` connection.
#### Scanning for Rsync

```bash
OathBornX@htb[/htb]$ sudo nmap -sV -p 873 127.0.0.1

Starting Nmap 7.92 ( https://nmap.org ) at 2022-09-19 09:31 EDT
Nmap scan report for localhost (127.0.0.1)
Host is up (0.0058s latency).

PORT    STATE SERVICE VERSION
873/tcp open  rsync   (protocol version 31)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1.13 seconds
```

#### Probing for Accessible Shares

We can next probe the service a bit to see what we can gain access to.

```bash
OathBornX@htb[/htb]$ nc -nv 127.0.0.1 873

(UNKNOWN) [127.0.0.1] 873 (rsync) open
@RSYNCD: 31.0
@RSYNCD: 31.0
#list
dev            	Dev Tools
@RSYNCD: EXIT
```

#### Enumerating an Open Share

Here we can see a share called `dev`, and we can enumerate it further.

```bash
OathBornX@htb[/htb]$ rsync -av --list-only rsync://127.0.0.1/dev

receiving incremental file list
drwxr-xr-x             48 2022/09/19 09:43:10 .
-rw-r--r--              0 2022/09/19 09:34:50 build.sh
-rw-r--r--              0 2022/09/19 09:36:02 secrets.yaml
drwx------             54 2022/09/19 09:43:10 .ssh

sent 25 bytes  received 221 bytes  492.00 bytes/sec
total size is 0  speedup is 0.00
```

From the output we can identify files worth downloading and a directory that likely contains `SSH` keys. We can synchronize those files to our host using `rsync` with a command like `rsync -av rsync://127.0.0.1/dev`. If `rsync` is configured to use `SSH` for transport, append the remote-shell option `-e ssh` or specify a non-standard port with `-e "ssh -p2222"`. The above syntax demonstrates how to run `Rsync` over an `SSH` tunnel and how to handle non-standard `SSH` ports.

---

# R-Services

`R-Services` are a suite of legacy remote-access services for `Unix` hosts that operate over `TCP/IP`. Originally developed by the `Computer Systems Research Group (CSRG)` at the University of California, Berkeley, `R-Services` were once the de facto standard for remote Unix access before being superseded by `SSH` due to fundamental security weaknesses. Like `telnet`, `R-Services` transmit data, including credentials, in cleartext and are therefore susceptible to `man-in-the-middle` (`MITM`) interception.

`R-Services` use ports `512`, `513`, and `514` and are accessed via the `r-commands` suite. They still appear on some enterprise systems such as `Solaris`, `HP-UX`, and `AIX`. Although uncommon today, encountering `R-Services` during internal assessments is not rare; treat any exposure as a high-risk finding and investigate for credential leakage or active session interception.

The [R-commands](https://en.wikipedia.org/wiki/Berkeley_r-commands) suite consists of the following programs:

- rcp (`remote copy`)
- rexec (`remote execution`)
- rlogin (`remote login`)
- rsh (`remote shell`)
- rstat
- ruptime
- rwho (`remote who`)

Each command has its intended functionality; however, we will only cover the most commonly abused `r-commands`. The table below will provide a quick overview of the most frequently abused commands, including the service daemon they interact with, over what port and transport method to which they can be accessed, and a brief description of each.

|**Command**|**Service Daemon**|**Port**|**Transport Protocol**|**Description**|
|---|---|---|---|---|
|`rcp`|`rshd`|514|TCP|Copy a file or directory bidirectionally from the local system to the remote system (or vice versa) or from one remote system to another. It works like the `cp` command on Linux but provides `no warning to the user for overwriting existing files on a system`.|
|`rsh`|`rshd`|514|TCP|Opens a shell on a remote machine without a login procedure. Relies upon the trusted entries in the `/etc/hosts.equiv` and `.rhosts` files for validation.|
|`rexec`|`rexecd`|512|TCP|Enables a user to run shell commands on a remote machine. Requires authentication through the use of a `username` and `password` through an unencrypted network socket. Authentication is overridden by the trusted entries in the `/etc/hosts.equiv` and `.rhosts` files.|
|`rlogin`|`rlogind`|513|TCP|Enables a user to log in to a remote host over the network. It works similarly to `telnet` but can only connect to Unix-like hosts. Authentication is overridden by the trusted entries in the `/etc/hosts.equiv` and `.rhosts` files.|
The `/etc/hosts.equiv` file contains a list of `trusted hosts` and is used to grant access to other systems on the network. When `users` on one of these hosts attempt to access the system, they are automatically `granted access without further authentication`.
#### /etc/hosts.equiv

```bash
OathBornX@htb[/htb]$ cat /etc/hosts.equiv

# <hostname> <local username>
pwnbox cry0l1t3
```

Now that we have a basic understanding of `r-commands`, let's do some quick footprinting using `Nmap` to determine if all necessary ports are open.

#### Scanning for R-Services

```bash
OathBornX@htb[/htb]$ sudo nmap -sV -p 512,513,514 10.0.17.2

Starting Nmap 7.80 ( https://nmap.org ) at 2022-12-02 15:02 EST
Nmap scan report for 10.0.17.2
Host is up (0.11s latency).

PORT    STATE SERVICE    VERSION
512/tcp open  exec?
513/tcp open  login?
514/tcp open  tcpwrapped

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 145.54 seconds
```

#### Access Control & Trusted Relationships

`Access control` and `trusted relationships` are the core problems with legacy `r-services`, and the primary reason `SSH` replaced them. `R-services` trust client-supplied identity data; that trust is enforced not only via `Pluggable Authentication Modules` (`PAM`) but also by bypassing PAM when entries exist in `/etc/hosts.equiv` or `.rhosts` on the target.

The `hosts.equiv` and `.rhosts` files list trusted `hosts` (IP or hostname) and `users` that the local system will accept without additional authentication for `r-commands` sessions. Because trust is granted based on those file entries and on client-supplied identifiers, an attacker who can spoof a trusted host or control an account on one can gain access without a password.

Treat any exposed `r-services` or writable `hosts.equiv`/`.rhosts` entries as a high-risk finding; they effectively allow lateral trust-based authentication that modern systems avoid by using `SSH` and properly configured `PAM`.
#### Sample .rhosts File

```bash
OathBornX@htb[/htb]$ cat .rhosts

htb-student     10.0.17.5
+               10.0.17.10
+               +
```

As we can see from this example, both files follow the specific syntax of `<username> <ip address>` or `<username> <hostname>` pairs. Additionally, the `+` modifier can be used within these files as a wildcard to specify anything. In this example, the `+` modifier allows any external user to access r-commands from the `htb-student` user account via the host with the IP address `10.0.17.10`.

#### Logging in Using Rlogin

```bash
OathBornX@htb[/htb]$ rlogin 10.0.17.2 -l htb-student

Last login: Fri Dec  2 16:11:21 from localhost

[htb-student@localhost ~]$
```

We have successfully logged in under the `htb-student` account on the remote host due to the misconfigurations in the `.rhosts` file. Once successfully logged in, we can also abuse the `rwho` command to list all interactive sessions on the local network by sending requests to the UDP port 513.
#### Listing Authenticated Users Using Rwho

```bash
OathBornX@htb[/htb]$ rwho

root     web01:pts/0 Dec  2 21:34
htb-student     workstn01:tty1  Dec  2 19:57  2:25       
```

