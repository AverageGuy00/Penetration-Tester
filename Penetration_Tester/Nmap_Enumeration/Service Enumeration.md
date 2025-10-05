For us, it is essential to determine the `application` and its `version` as accurately as possible because this lets us map known `vulnerabilities` to that exact `version` and decide whether to obtain or analyze the `source code` for that release. An exact `version` number lets us search for precise `exploit` code that matches the `service` and the `operating system` of our `target`, reducing false positives and wasted effort.

Combine passive and active `fingerprinting` techniques such as `banner grabbing` and `Nmap` `version detection` to collect `CPE` strings and correlate them with `CVE` identifiers, vendor `advisories`, and public repositories like `Exploit-DB`. When possible, verify findings against vendor `release notes`, patch level, and an `SBOM` or published `build artifacts` to confirm whether a reported `vulnerability` actually affects the deployed `service` on the target.

---

## Service Version Detection

Run a quick, low-noise `port scan` to discover open ports, then run a full background scan `-p-` and use `version detection` `-sV` on discovered ports to identify services and versions.

```bash
sudo nmap 10.129.2.28 -p- -sV

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 19:44 CEST
[Space Bar]
Stats: 0:00:03 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 3.64% done; ETC: 19:45 (0:00:53 remaining)
```

Use `--stats-every=5s` to show scan `status` periodically; specify `s` (seconds) or `m` (minutes).

```bash
sudo nmap 10.129.2.28 -p- -sV --stats-every=5s

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 19:46 CEST
Stats: 0:00:05 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 13.91% done; ETC: 19:49 (0:00:31 remaining)
Stats: 0:00:10 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 39.57% done; ETC: 19:48 (0:00:15 remaining)
```

| **Scanning Options** |                    **Description**                     |
| :------------------: | :----------------------------------------------------: |
|    `10.129.2.28`     |              Scans the specified target.               |
|        `-p-`         |                    Scans all ports.                    |
|        `-sV`         | Performs service version detection on specified ports. |
|  `--stats-every=5s`  |    Shows the progress of the scan every 5 seconds.     |

We can also increase the `verbosity level` (`-v` / `-vv`), which will show us the open ports directly when `Nmap` detects the

```bash
sudo nmap 10.129.2.28 -p- -sV -v 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 20:03 CEST
NSE: Loaded 45 scripts for scanning.
Initiating ARP Ping Scan at 20:03
Scanning 10.129.2.28 [1 port]
Completed ARP Ping Scan at 20:03, 0.03s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 20:03
Completed Parallel DNS resolution of 1 host. at 20:03, 0.02s elapsed
Initiating SYN Stealth Scan at 20:03
Scanning 10.129.2.28 [65535 ports]
Discovered open port 995/tcp on 10.129.2.28
Discovered open port 80/tcp on 10.129.2.28
Discovered open port 993/tcp on 10.129.2.28
Discovered open port 143/tcp on 10.129.2.28
Discovered open port 25/tcp on 10.129.2.28
Discovered open port 110/tcp on 10.129.2.28
Discovered open port 22/tcp on 10.129.2.28
<SNIP>
```

## Banner Grabbing

After the `scan` completes, you'll see all `TCP` `ports` and their `service` `versions` active on the `system`.

```bash
sudo nmap 10.129.2.28 -p- -sV

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 20:00 CEST
Nmap scan report for 10.129.2.28
Host is up (0.013s latency).
Not shown: 65525 closed ports
PORT      STATE    SERVICE      VERSION
22/tcp    open     ssh          OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
25/tcp    open     smtp         Postfix smtpd
80/tcp    open     http         Apache httpd 2.4.29 ((Ubuntu))
110/tcp   open     pop3         Dovecot pop3d
139/tcp   filtered netbios-ssn
143/tcp   open     imap         Dovecot imapd (Ubuntu)
445/tcp   filtered microsoft-ds
993/tcp   open     ssl/imap     Dovecot imapd (Ubuntu)
995/tcp   open     ssl/pop3     Dovecot pop3d
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
Service Info: Host:  inlane; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 91.73 seconds
```

`Nmap` primarily inspects `banners` of scanned `ports` to identify `services`. If banners are insufficient, it uses a `signature-based` matching system, which increases `scan` time. A limitation is that automatic `Nmap` scans can miss information when it cannot handle certain `services` or `versions`.

```bash
sudo nmap 10.129.2.28 -p- -sV -Pn -n --disable-arp-ping --packet-trace

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-16 20:10 CEST
<SNIP>
NSOCK INFO [0.4200s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 18 [10.129.2.28:25] (35 bytes): 220 inlane ESMTP Postfix (Ubuntu)..
Service scan match (Probe NULL matched with NULL line 3104): 10.129.2.28:25 is smtp.  Version: |Postfix smtpd|||
NSOCK INFO [0.4200s] nsock_iod_delete(): nsock_iod_delete (IOD #1)
Nmap scan report for 10.129.2.28
Host is up (0.076s latency).

PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
Service Info: Host:  inlane

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.47 seconds
```

If we look at the results from `Nmap`, we can see the port's status, service name, and hostname. Nevertheless, let us look at this line here:

```
NSOCK INFO [0.4200s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 18 [10.129.2.28:25] (35 bytes): 220 inlane ESMTP Postfix (Ubuntu)..
```

Here the `SMTP` `server` revealed a richer `banner` than `Nmap` displayed — it even included the `Linux` distribution (`Ubuntu`). After a successful `three-way handshake`, many `services` send an identifying `banner` (a TCP payload often delivered with the `PSH` flag in the `TCP` header) so the `client` knows what `service` it’s talking to. Some `services` don’t immediately send banners or have them `removed`/`manipulated`, so automated tools like `Nmap` can miss details. Manually connecting with `nc` to grab the `banner` while capturing packets with `tcpdump` can reveal what `Nmap` missed, showing raw `network` traffic and exact `port` responses for deeper analysis.

---

### Tcpdump

```bash
sudo tcpdump -i eth0 host 10.10.14.2 and 10.129.2.28

tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
```

```bash
18:28:07.128564 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [S], seq 1798872233, win 65535, options [mss 1460,nop,wscale 6,nop,nop,TS val 331260178 ecr 0,sackOK,eol], length 0
18:28:07.255151 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [S.], seq 1130574379, ack 1798872234, win 65160, options [mss 1460,sackOK,TS val 1800383922 ecr 331260178,nop,wscale 7], length 0
18:28:07.255281 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [.], ack 1, win 2058, options [nop,nop,TS val 331260304 ecr 1800383922], length 0
18:28:07.319306 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [P.], seq 1:36, ack 1, win 510, options [nop,nop,TS val 1800383985 ecr 331260304], length 35: SMTP: 220 inlane ESMTP Postfix (Ubuntu)
18:28:07.319426 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [.], ack 36, win 2058, options [nop,nop,TS val 331260368 ecr 1800383985], length 0
```

The first three lines show us the `three-way handshake`.

| 1.  | `[SYN]`     | `18:28:07.128564 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [S], <SNIP>`  |
| --- | ----------- | ---------------------------------------------------------------------------- |
| 2.  | `[SYN-ACK]` | `18:28:07.255151 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [S.], <SNIP>` |
| 3.  | `[ACK]`     | `18:28:07.255281 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [.], <SNIP>`  |

After that, the target `SMTP` server sends us a `TCP` packet with the `PSH` and `ACK` flags, where `PSH` states that the target server is sending data to us and with `ACK` simultaneously informs us that all required data has been sent.

| 4.  | `[PSH-ACK]` | `18:28:07.319306 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [P.], <SNIP>` |
| --- | ----------- | ---------------------------------------------------------------------------- |

The last TCP packet that we sent confirms the receipt of the data with an `ACK`.

| 5.  | `[ACK]` | `18:28:07.319426 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [.], <SNIP>` |
| --- | ------- | --------------------------------------------------------------------------- |
