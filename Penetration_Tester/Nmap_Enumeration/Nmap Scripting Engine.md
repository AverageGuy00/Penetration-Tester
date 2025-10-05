`Nmap` `Scripting` `Engine` (`NSE`) is a powerful extension of `Nmap` that lets you write `Lua` `scripts` to interact with services — the scripts are grouped into 14 categories: `auth`, `broadcast`, `brute`, `default`, `discovery`, `dos`, `exploit`, `external`, `fuzzer`, `intrusive`, `malware`, `safe`, `version`, `vuln`. Use `default` for the common useful checks, `safe` for non-disruptive probes, and `vuln`/`exploit`/`dos` only when you have permission (or you’ll hear the angry sysadmin trumpet).

| **Category** |                                                             **Description**                                                             |
| :----------: | :-------------------------------------------------------------------------------------------------------------------------------------: |
|    `auth`    |                                              Determination of authentication credentials.                                               |
| `broadcast`  | Scripts, which are used for host discovery by broadcasting and the discovered hosts, can be automatically added to the remaining scans. |
|   `brute`    |                    Executes scripts that try to log in to the respective service by brute-forcing with credentials.                     |
|  `default`   |                                           Default scripts executed by using the `-sC` option.                                           |
| `discovery`  |                                                   Evaluation of accessible services.                                                    |
|    `dos`     |       These scripts are used to check services for denial of service vulnerabilities and are used less as it harms the services.        |
|  `exploit`   |                          This category of scripts tries to exploit known vulnerabilities for the scanned port.                          |
|  `external`  |                                       Scripts that use external services for further processing.                                        |
|   `fuzzer`   |   This uses scripts to identify vulnerabilities and unexpected packet handling by sending different fields, which can take much time.   |
| `intrusive`  |                                    Intrusive scripts that could negatively affect the target system.                                    |
|  `malware`   |                                            Checks if some malware infects the target system.                                            |
|    `safe`    |                                 Defensive scripts that do not perform intrusive and destructive access.                                 |
|  `version`   |                                                    Extension for service detection.                                                     |
|    `vuln`    |                                               Identification of specific vulnerabilities.                                               |

We have several ways to define the desired `scripts` in `Nmap`.

#### Default Scripts

```bash
sudo nmap <target> -sC
```

#### Specific Scripts Category

```bash
sudo nmap <target> --script <category>
```

#### Defined Scripts

```bash
sudo nmap <target> --script <script-name>,<script-name>,...
```

For example, let us keep working with the target `SMTP` port and see the results we get with two defined `scripts`.
#### Nmap - Specifying Scripts

```bash
sudo nmap 10.129.2.28 -p 25 --script banner,smtp-commands

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-16 23:21 CEST
Nmap scan report for 10.129.2.28
Host is up (0.050s latency).

PORT   STATE SERVICE
25/tcp open  smtp
|_banner: 220 inlane ESMTP Postfix (Ubuntu)
|_smtp-commands: inlane, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8,
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
```

`Nmap` output shows the host is up and that `25/tcp` is `open` running `smtp`, with the `banner` revealing `220 inlane ESMTP Postfix (Ubuntu)` which exposes the `Linux` distribution as `Ubuntu`. The `smtp-commands` `script` lists supported commands (`PIPELINING`, `SIZE 10240000`, `VRFY`, `ETRN`, `STARTTLS`, `ENHANCEDSTATUSCODES`, `8BITMIME`, `DSN`, `SMTPUTF8`) that can help enumerate `users` or capabilities. The output also reports a `MAC Address` (`DE:AD:00:00:BE:EF`) and vendor (`Intel Corporate`). Use `Nmap` `service detection` (`-sV`) and `Nmap Scripting Engine` (`NSE`/`-sC`) to enrich findings, and run `OS detection` (`-O`) and `traceroute` (`--traceroute`) for a more aggressive probe via `-A` — but only with authorization.

#### Nmap - Aggressive Scan

```bash
sudo nmap 10.129.2.28 -p 80 -A
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-17 01:38 CEST
Nmap scan report for 10.129.2.28
Host is up (0.012s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-generator: WordPress 5.3.4
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: blog.inlanefreight.com
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 2.6.32 (96%), Linux 3.2 - 4.9 (96%), Linux 2.6.32 - 3.10 (96%), Linux 3.4 - 3.10 (95%), Linux 3.1 (95%), Linux 3.2 (95%), 
AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), Synology DiskStation Manager 5.2-5644 (94%), Netgear RAIDiator 4.2.28 (94%), 
Linux 2.6.32 - 2.6.35 (94%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop

TRACEROUTE
HOP RTT      ADDRESS
1   11.91 ms 10.129.2.28

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.36 seconds
```

| **Scanning Options** |                                          **Description**                                           |
| :------------------: | :------------------------------------------------------------------------------------------------: |
|    `10.129.2.28`     |                                    Scans the specified target.                                     |
|       `-p 80`        |                                   Scans only the specified port.                                   |
|         `-A`         | Performs service detection, OS detection, traceroute and uses defaults scripts to scan the target. |

---

# Vulnerability Assessment

For a `Vulnerability Assessment` with `Nmap`, focus on accurate `service` and `version` identification using `version detection` (`-sV`), `OS detection` (`-O`), and targeted `NSE` scripts (for example `--script vuln` or `--script http-vuln*`). Save outputs in machine-readable formats (`-oX`/`.xml`, `-oG`/`.gnmap`, `-oN`/`.nmap`) so you can parse, compare, and generate readable reports (convert `.xml` to `.html` with `xsltproc`). Correlate discovered `CPE` strings and `version` numbers with `CVE` entries, vendor `advisories`, and repositories like `Exploit-DB` to prioritize findings and reduce false positives.

```bash
sudo nmap 10.129.2.28 -p 80 -sV --script vuln 

Nmap scan report for 10.129.2.28
Host is up (0.036s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
| http-enum:
|   /wp-login.php: Possible admin folder
|   /readme.html: Wordpress version: 2
|   /: WordPress version: 5.3.4
|   /wp-includes/images/rss.png: Wordpress version 2.2 found.
|   /wp-includes/js/jquery/suggest.js: Wordpress version 2.5 found.
|   /wp-includes/images/blank.gif: Wordpress version 2.6 found.
|   /wp-includes/js/comment-reply.js: Wordpress version 2.7 found.
|   /wp-login.php: Wordpress login page.
|   /wp-admin/upgrade.php: Wordpress login page.
|_  /readme.html: Interesting, a readme.
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
| http-wordpress-users:
| Username found: admin
|_Search stopped at ID #25. Increase the upper limit if necessary with 'http-wordpress-users.limit'
| vulners:
|   cpe:/a:apache:http_server:2.4.29:
|     	CVE-2019-0211	7.2	https://vulners.com/cve/CVE-2019-0211
|     	CVE-2018-1312	6.8	https://vulners.com/cve/CVE-2018-1312
|     	CVE-2017-15715	6.8	https://vulners.com/cve/CVE-2017-15715
<SNIP>
```

