The `Simple Mail Transfer Protocol (SMTP)` handles `email transmission` over `IP networks`. It operates on a `client-server` model, used both between `mail clients` and `outgoing mail servers` or between multiple `SMTP servers` for message relaying.

By default, `SMTP` listens on `TCP port 25`, though modern configurations often use `TCP port 587` for `mail submission` from `authenticated users`. `Port 587` typically employs the `STARTTLS` command to upgrade a `plaintext connection` to an `encrypted session`, ensuring the `authentication credentials` and `email data` are not exposed in `plaintext traffic`.

During the `authentication phase`, the `SMTP client` confirms its identity using a `username` and `password`. It then transmits the `sender address`, `recipient address`, `message body`, and associated `email headers` to the `SMTP server`. Once transmission completes, the `connection` is closed.

The `SMTP server` then forwards the email to the next `mail transfer agent (MTA)` or `SMTP relay` in the `delivery chain`, continuing until it reaches the final `mail server` for storage or retrieval via `IMAP` or `POP3`.

An essential function of an `SMTP server` is **spam prevention** through `authentication mechanisms` that restrict email submission to `authorized users`. Modern implementations use the `Extended SMTP (ESMTP)` protocol with `SMTP-Auth` for secure verification.

After sending an email, the `SMTP client` or `Mail User Agent (MUA)` structures it into a `header` and `body` before uploading both to the `SMTP server`. The server operates a `Mail Transfer Agent (MTA)`, which is responsible for `sending`, `receiving`, and `routing` emails. The `MTA` performs checks for `message size limits` and `spam detection` before temporarily storing the message.

To reduce the `MTA` workload, a `Mail Submission Agent (MSA)` may act as an intermediary, validating the `origin` and `authenticity` of emails. This component is also known as a `Relay server`. Misconfigured relay servers can create `Open Relay` vulnerabilities, which attackers exploit to send spam or malicious mail anonymously — an issue known as the `Open Relay Attack`.

Once validated, the `MTA` queries the `DNS` for the `MX record` to obtain the `IP address` of the recipient’s mail server. When the email arrives at its destination `SMTP server`, the data packets are reconstructed into the original message. A `Mail Delivery Agent (MDA)` then delivers it to the recipient’s `mailbox`, accessible through `POP3` or `IMAP`.

Data flow:  
`MUA ➞ MSA ➞ MTA (Relay) ➞ MDA ➞ Mailbox (POP3/IMAP)`

---

`SMTP` has two major disadvantages rooted in its original design.

The first limitation is the lack of a reliable `delivery confirmation` mechanism. Although the `SMTP specifications` technically allow delivery notifications, the `format` is undefined by default. As a result, mail servers usually return only a generic English error message with the `header` of the undelivered message.

The second and more critical issue is the absence of `authentication` during the initial connection phase. Because `SMTP` does not inherently verify sender identity, attackers can exploit `open relays` to distribute `spam` or conduct `mail spoofing` by forging `sender addresses`. This makes it difficult to trace the actual origin of a message.

To counter these weaknesses, modern systems employ `security mechanisms` such as `DomainKeys Identified Mail (DKIM)` and the `Sender Policy Framework (SPF)` to verify email legitimacy. Suspicious messages are either `rejected` or `quarantined` in a `spam folder`.

These improvements are incorporated into `Extended SMTP (ESMTP)`, the enhanced version of the protocol. In practice, most references to `SMTP` actually refer to `ESMTP`. This extension introduces `TLS encryption` via the `STARTTLS` command, initiated after the `EHLO` handshake. Once activated, the `SMTP session` becomes encrypted, securing both the `authentication credentials` and the `message content`. With this protection, the `AUTH PLAIN` mechanism can safely handle client authentication over a `SSL/TLS-secured` connection.

---

# Default Configuration

```bash
OathBornX@htb[/htb]$ cat /etc/postfix/main.cf | grep -v "#" | sed -r "/^\s*$/d"

smtpd_banner = ESMTP Server 
biff = no
append_dot_mydomain = no
readme_directory = no
compatibility_level = 2
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
myhostname = mail1.inlanefreight.htb
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
smtp_generic_maps = hash:/etc/postfix/generic
mydestination = $myhostname, localhost 
masquerade_domains = $myhostname
mynetworks = 127.0.0.0/8 10.129.0.0/16
mailbox_size_limit = 0
recipient_delimiter = +
smtp_bind_address = 0.0.0.0
inet_protocols = ipv4
smtpd_helo_restrictions = reject_invalid_hostname
home_mailbox = /home/postfix
```

The sending and communication are also done by special commands that cause the SMTP server to do what the user requires.

|**Command**|**Description**|
|---|---|
|`AUTH PLAIN`|AUTH is a service extension used to authenticate the client.|
|`HELO`|The client logs in with its computer name and thus starts the session.|
|`MAIL FROM`|The client names the email sender.|
|`RCPT TO`|The client names the email recipient.|
|`DATA`|The client initiates the transmission of the email.|
|`RSET`|The client aborts the initiated transmission but keeps the connection between client and server.|
|`VRFY`|The client checks if a mailbox is available for message transfer.|
|`EXPN`|The client also checks if a mailbox is available for messaging with this command.|
|`NOOP`|The client requests a response from the server to prevent disconnection due to time-out.|
|`QUIT`|The client terminates the session.|

To interact with the `SMTP` server, we can use the `telnet` tool to initialize a TCP connection with the SMTP server. The actual initialization of the session is done with the command mentioned above, `HELO` or `EHLO`.
#### Telnet - HELO/EHLO

```bash
OathBornX@htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server 


HELO mail1.inlanefreight.htb

250 mail1.inlanefreight.htb


EHLO mail1

250-mail1.inlanefreight.htb
250-PIPELINING
250-SIZE 10240000
250-ETRN
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250-SMTPUTF8
250 CHUNKING
```

The command `VRFY` can be used to enumerate existing users on the system. However, this does not always work. Depending on how the SMTP server is configured, the `SMTP` server may issue `code 252` and confirm the existence of a user that does not exist on the system. A list of all SMTP response codes can be found [here](https://serversmtp.com/smtp-error/).
#### Telnet - VRFY

```bash
OathBornX@htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server 

VRFY root

252 2.0.0 root


VRFY cry0l1t3

252 2.0.0 cry0l1t3


VRFY testuser

252 2.0.0 testuser


VRFY aaaaaaaaaaaaaaaaaaaaaaaaaaaa

252 2.0.0 aaaaaaaaaaaaaaaaaaaaaaaaaaaa
```

Sometimes we may have to work through a web proxy. We can also make this web proxy connect to the SMTP server. The command that we would send would then look something like this: `CONNECT 10.129.14.128:25 HTTP/1.0`

---

#### Send an Email

```bash
OathBornX@htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server


EHLO inlanefreight.htb

250-mail1.inlanefreight.htb
250-PIPELINING
250-SIZE 10240000
250-ETRN
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250-SMTPUTF8
250 CHUNKING


MAIL FROM: <cry0l1t3@inlanefreight.htb>

250 2.1.0 Ok


RCPT TO: <mrb3n@inlanefreight.htb> NOTIFY=success,failure

250 2.1.5 Ok


DATA

354 End data with <CR><LF>.<CR><LF>

From: <cry0l1t3@inlanefreight.htb>
To: <mrb3n@inlanefreight.htb>
Subject: DB
Date: Tue, 28 Sept 2021 16:32:51 +0200
Hey man, I am trying to access our XY-DB but the creds don't work. 
Did you make any changes there?
.

250 2.0.0 Ok: queued as 6E1CF1681AB


QUIT

221 2.0.0 Bye
Connection closed by foreign host.
```

The `mail header` contains a significant amount of valuable `metadata` about an `email message`. It reveals details such as the `sender` and `recipient` addresses, `timestamps` for sending and receipt, the `servers` the email passed through, the `message content type`, and various `formatting parameters`.

Certain header fields are **mandatory**, including `From`, `To`, and `Date`, which identify the sender, recipient, and creation time of the email. Other fields, like `Reply-To` or `Cc`, are **optional** but often present for routing or organizational purposes.

Importantly, the `email header` does **not** contain the technical delivery data used by the `SMTP protocol`; those are transmitted at the `transport layer` during the exchange between `mail servers`.

Both the `sender` and `recipient` can access the `email header`, though it is typically hidden in normal email clients and must be viewed manually. The `structure` and `syntax` of all email headers are formally defined in `RFC 5322`, which specifies how each field must be formatted and interpreted for proper message handling and forensic analysis.

---

# Dangerous Settings

To avoid having messages blocked by spam filters, senders often use a trusted `relay server` — an `SMTP server` trusted and verified by other mail servers. The sender must `authenticate` to that `relay server` before submission.

Administrators who lack a clear inventory of allowed `IP ranges` sometimes misconfigure the server to accept everything, e.g. `mynetworks = 0.0.0.0/0`, which creates an `open relay`. An `open relay` lets attackers send `spam` with forged `sender addresses`, perform `email spoofing`, and enable downstream abuse or interception.

This is a critical `SMTP` misconfiguration. Restrict `mynetworks` to known ranges and enforce `SMTP-Auth` and `STARTTLS` to prevent exploitation.

---
# Footprinting the Service

The default Nmap scripts include `smtp-commands`, which uses the `EHLO` command to list all possible commands that can be executed on the target SMTP server.

```bash
OathBornX@htb[/htb]$ sudo nmap 10.129.14.128 -sC -sV -p25

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-27 17:56 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00025s latency).

PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd
|_smtp-commands: mail1.inlanefreight.htb, PIPELINING, SIZE 10240000, VRFY, ETRN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING, 
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.09 seconds
```

#### Nmap - Open Relay

```bash
OathBornX@htb[/htb]$ sudo nmap 10.129.14.128 -p25 --script smtp-open-relay -v

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-30 02:29 CEST
NSE: Loaded 1 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 02:29
Completed NSE at 02:29, 0.00s elapsed
Initiating ARP Ping Scan at 02:29
Scanning 10.129.14.128 [1 port]
Completed ARP Ping Scan at 02:29, 0.06s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 02:29
Completed Parallel DNS resolution of 1 host. at 02:29, 0.03s elapsed
Initiating SYN Stealth Scan at 02:29
Scanning 10.129.14.128 [1 port]
Discovered open port 25/tcp on 10.129.14.128
Completed SYN Stealth Scan at 02:29, 0.06s elapsed (1 total ports)
NSE: Script scanning 10.129.14.128.
Initiating NSE at 02:29
Completed NSE at 02:29, 0.07s elapsed
Nmap scan report for 10.129.14.128
Host is up (0.00020s latency).

PORT   STATE SERVICE
25/tcp open  smtp
| smtp-open-relay: Server is an open relay (16/16 tests)
|  MAIL FROM:<> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@nmap.scanme.org> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@ESMTP> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest%nmap.scanme.org@[10.129.14.128]>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest%nmap.scanme.org@ESMTP>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<"relaytest@nmap.scanme.org">
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<"relaytest%nmap.scanme.org">
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest@nmap.scanme.org@[10.129.14.128]>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<"relaytest@nmap.scanme.org"@[10.129.14.128]>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest@nmap.scanme.org@ESMTP>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<@[10.129.14.128]:relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<@ESMTP:relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<nmap.scanme.org!relaytest>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<nmap.scanme.org!relaytest@[10.129.14.128]>
|_ MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<nmap.scanme.org!relaytest@ESMTP>
MAC Address: 00:00:00:00:00:00 (VMware)

NSE: Script Post-scanning.
Initiating NSE at 02:29
Completed NSE at 02:29, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.48 seconds
           Raw packets sent: 2 (72B) | Rcvd: 2 (72B)
```
