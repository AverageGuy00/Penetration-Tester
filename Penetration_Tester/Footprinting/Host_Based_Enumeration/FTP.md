The `File Transfer Protocol` (`FTP`) is one of the oldest protocols on the Internet. The FTP runs within the application layer of the `TCP/IP` protocol stack.

A distinction exists between active and passive `FTP`. In the active mode, the `FTP client` establishes the initial connection to the `FTP server` through `TCP port 21`. The client then informs the server which `client-side port` will be used to receive data.

However, when a `firewall` protects the client, the `FTP server` cannot initiate the data connection back to the client because external inbound connections are blocked by default.

To solve this, passive `FTP` mode was developed. In passive mode, the `FTP server` announces a random `port number` that the client can use to initiate the `data channel` connection. Since the `FTP client` initiates both the control and data connections, the `firewall` does not block the transfer.

- Active `FTP`: Server connects to client for data → blocked by firewalls
- Passive `FTP`: Client connects to server for data → firewall-friendly mode

---

# TFTP

`Trivial File Transfer Protocol` (`TFTP`) is simpler than `FTP` and performs file transfers between client and `server processes`. However, it `does not` provide user `authentication` and other valuable features supported by `FTP`.

| **Commands** |                                                            **Description**                                                             |
| :----------: | :------------------------------------------------------------------------------------------------------------------------------------: |
|  `connect`   |                                   Sets the remote host, and optionally the port, for file transfers.                                   |
|    `get`     |                                Transfers a file or set of files from the remote host to the local host.                                |
|    `put`     |                               Transfers a file or set of files from the local host onto the remote host.                               |
|    `quit`    |                                                              Exits tftp.                                                               |
|   `status`   | Shows the current status of tftp, including the current transfer mode (ascii or binary), connection status, time-out value, and so on. |
|  `verbose`   |                       Turns verbose mode, which displays additional information during file transfer, on or off.                       |
Unlike the FTP client, `TFTP` does not have directory listing functionality.

---

# Default Configuration

One of the most used `FTP servers` on `Linux-based` distributions is `vsFTPd`. The default configuration of `vsFTPd` can be found in `/etc/vsftpd.conf`, and some settings are already predefined by default. It is highly recommended to install the `vsFTPd` server on a VM and have a closer look at this configuration.

#### vsFTPd Config File

```bash
cat /etc/vsftpd.conf | grep -v "#"
```

|                          **Setting**                          |                                             **Description**                                              |
| :-----------------------------------------------------------: | :------------------------------------------------------------------------------------------------------: |
|                          `listen=NO`                          |                                Run from inetd or as a standalone daemon?                                 |
|                       `listen_ipv6=YES`                       |                                             Listen on IPv6 ?                                             |
|                     `anonymous_enable=NO`                     |                                         Enable Anonymous access?                                         |
|                      `local_enable=YES`                       |                                       Allow local users to login?                                        |
|                    `dirmessage_enable=YES`                    |                Display active directory messages when users go into certain directories?                 |
|                      `use_localtime=YES`                      |                                             Use local time?                                              |
|                     `xferlog_enable=YES`                      |                                  Activate logging of uploads/downloads?                                  |
|                  `connect_from_port_20=YES`                   |                                          Connect from port 20?                                           |
|           `secure_chroot_dir=/var/run/vsftpd/empty`           |                                        Name of an empty directory                                        |
|                   `pam_service_name=vsftpd`                   |                       This string is the name of the PAM service vsftpd will use.                        |
|     `rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem`      | The last three options specify the location of the RSA certificate to use for SSL encrypted connections. |
| `rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key` |                                                                                                          |
|                        `ssl_enable=NO`                        |                                                                                                          |

In addition, there is a file called `/etc/ftpusers` that we also need to pay attention to, as this file is used to deny certain users access to the FTP service. In the following example, the users `guest`, `john`, and `kevin` are not permitted to log in to the FTP service, even if they exist on the Linux system.

#### FTPUSERS

```bash
$ cat /etc/ftpusers

guest
john
kevin
```

---

## Dangerous Settings

`FTP servers` have many security settings that control how connections and authentication work. Some options are used for testing `firewalls`, checking `network routes`, or validating `authentication`.

One common authentication type is the `anonymous user`. It lets anyone on the internal network access shared files without needing individual credentials.

For `vsFTPd`, this can be enabled by adding optional configuration lines that allow anonymous login.

|          **Setting**           |                                  **Description**                                   |
| :----------------------------: | :--------------------------------------------------------------------------------: |
|     `anonymous_enable=YES`     |                             Allowing anonymous login?                              |
|    `anon_upload_enable=YES`    |                        Allowing anonymous to upload files?                         |
| `anon_mkdir_write_enable=YES`  |                   Allowing anonymous to create new directories?                    |
|     `no_anon_password=YES`     |                         Do not ask anonymous for password?                         |
| `anon_root=/home/username/ftp` |                              Directory for anonymous.                              |
|       `write_enable=YES`       | Allow the usage of FTP commands: STOR, DELE, RNFR, RNTO, MKD, RMD, APPE, and SITE? |

#### vsFTPd Detailed Output

|        **Setting**        |                            **Description**                             |
| :-----------------------: | :--------------------------------------------------------------------: |
|  `dirmessage_enable=YES`  |         Show a message when they first enter a new directory?          |
|    `chown_uploads=YES`    |            Change ownership of anonymously uploaded files?             |
| `chown_username=username` |       User who is given ownership of anonymously uploaded files.       |
|    `local_enable=YES`     |                      Enable local users to login?                      |
|  `chroot_local_user=YES`  |              Place local users into their home directory?              |
| `chroot_list_enable=YES`  | Use a list of local users that will be placed in their home directory? |

|       **Setting**       |                                 **Description**                                  |
| :---------------------: | :------------------------------------------------------------------------------: |
|     `hide_ids=YES`      | All user and group information in directory listings will be displayed as "ftp". |
| `ls_recurse_enable=YES` |                       Allows the use of recurse listings.                        |

```bash
#### Recursive Listing
$ ls -R

#### Download a File
$ ls

#### Download All Available Files
$ wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136

#### Upload a File
$ put testupload.txt
```

---

# Footprinting the Service

`Nmap` is a network `scanner` for host discovery and service/port enumeration. The `Nmap Scripting Engine (NSE)` is a framework of small, task-focused scripts that probe and automate checks for specific protocols and services. Use `NSE` to detect banners, misconfigurations, default creds, vulns, and to automate reconnaissance tasks.
#### Nmap FTP Scripts

```bash
$ sudo nmap --script-updatedb

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 13:49 CEST
NSE: Updating rule database.
NSE: Script Database updated successfully.
Nmap done: 0 IP addresses (0 hosts up) scanned in 0.28 seconds
```

All the NSE scripts are located on the Pwnbox in `/usr/share/nmap/scripts/`, but on our systems, we can find them using a simple command

```bash
$ find / -type f -name ftp* 2>/dev/null | grep scripts

/usr/share/nmap/scripts/ftp-syst.nse
/usr/share/nmap/scripts/ftp-vsftpd-backdoor.nse
/usr/share/nmap/scripts/ftp-vuln-cve2010-4221.nse
/usr/share/nmap/scripts/ftp-proftpd-backdoor.nse
/usr/share/nmap/scripts/ftp-bounce.nse
/usr/share/nmap/scripts/ftp-libopie.nse
/usr/share/nmap/scripts/ftp-anon.nse
/usr/share/nmap/scripts/ftp-brute.nse
```

As we already know, the `FTP` server usually runs on the standard `TCP port 21`, which we can scan using Nmap. We also use the version scan (`-sV`), aggressive scan (`-A`), and the default script scan (`-sC`) against our target `10.129.14.136`.

```bash
$ sudo nmap -sV -p21 -sC -A 10.129.14.136

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-16 18:12 CEST
Nmap scan report for 10.129.14.136
Host is up (0.00013s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rwxrwxrwx    1 ftp      ftp       8138592 Sep 16 17:24 Calendar.pptx [NSE: writeable]
| drwxrwxrwx    4 ftp      ftp          4096 Sep 16 17:57 Clients [NSE: writeable]
| drwxrwxrwx    2 ftp      ftp          4096 Sep 16 18:05 Documents [NSE: writeable]
| drwxrwxrwx    2 ftp      ftp          4096 Sep 16 17:24 Employees [NSE: writeable]
| -rwxrwxrwx    1 ftp      ftp            41 Sep 16 17:24 Important Notes.txt [NSE: writeable]
|_-rwxrwxrwx    1 ftp      ftp             0 Sep 15 14:57 testupload.txt [NSE: writeable]
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.4
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
```

#### Nmap Script Trace

The `ftp-syst` script issues the `STAT` command to retrieve the server’s status string (often exposing the `system type` and `server version`), which can reveal implementation details and feature flags that guide follow-ups. The `STAT` response is informational but still generates protocol traffic that will be logged by the target.

```bash
$ sudo nmap -sV -p21 -sC -A 10.129.14.136 --script-trace

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 13:54 CEST                                                                                                                                                   
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 8 [10.129.14.136:21]                                   
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 16 [10.129.14.136:21]             
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 24 [10.129.14.136:21]
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 32 [10.129.14.136:21]
NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #1 [10.129.14.136:21] (timeout: 7000ms) EID 42
NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #2 [10.129.14.136:21] (timeout: 9000ms) EID 50
NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #3 [10.129.14.136:21] (timeout: 7000ms) EID 58
NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #4 [10.129.14.136:21] (timeout: 11000ms) EID 66
NSE: TCP 10.10.14.4:54226 > 10.129.14.136:21 | CONNECT
NSE: TCP 10.10.14.4:54228 > 10.129.14.136:21 | CONNECT
NSE: TCP 10.10.14.4:54230 > 10.129.14.136:21 | CONNECT
NSE: TCP 10.10.14.4:54232 > 10.129.14.136:21 | CONNECT
NSOCK INFO [11.4660s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 50 [10.129.14.136:21] (41 bytes): 220 Welcome to HTB-Academy FTP service...
NSOCK INFO [11.4660s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 58 [10.129.14.136:21] (41 bytes): 220 Welcome to HTB-Academy FTP service...
NSE: TCP 10.10.14.4:54228 < 10.129.14.136:21 | 220 Welcome to HTB-Academy FTP service.
```


