It is essential to understand how `Nmap` performs its functions and processes results. Without this, the scan output would lack context and meaning. After confirming that a target is alive, the next step is to build a clearer picture of the system. This includes identifying `open ports` and their `services`, determining `service versions`, collecting `information exposed by services`, and attempting to fingerprint the `operating system`.

|     **State**      |                                                                                             **Description**                                                                                             |
| :----------------: | :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|       `open`       |             This indicates that the connection to the scanned port has been established. These connections can be **TCP connections**, **UDP datagrams** as well as **SCTP associations**.              |
|      `closed`      | When the port is shown as closed, the TCP protocol indicates that the packet we received back contains an `RST` flag. This scanning method can also be used to determine if our target is alive or not. |
|     `filtered`     |         Nmap cannot correctly identify whether the scanned port is open or closed because either no response is returned from the target for the port or we get an error code from the target.          |
|    `unfiltered`    |                      This state of a port only occurs during the **TCP-ACK** scan and means that the port is accessible, but it cannot be determined whether it is open or closed.                      |
|  `open\|filtered`  |                        If we do not get a response for a specific port, `Nmap` will set it to that state. This indicates that a firewall or packet filter may protect the port.                         |
| `closed\|filtered` |                      This state only occurs in the **IP ID idle** scans and indicates that it was impossible to determine if the scanned port is closed or filtered by a firewall.                      |

---

# Discovering Open TCP Ports

By default, `Nmap` scans the top 1000 `TCP` ports using a `SYN scan` (`-sS`). This mode is only the default when executed as `root`, since it requires raw socket permissions. Otherwise, `Nmap` falls back to a `TCP connect scan` (`-sT`). If no ports or scanning methods are specified, `Nmap` applies these defaults. Ports can be defined explicitly (`-p 22,25,80,139,445`), by ranges (`-p 22-445`), by `top ports` from the `Nmap` frequency database (`--top-ports=10`), by scanning all ports (`-p-`), or by performing a fast scan against the top 100 ports (`-F`).

```bash
sudo nmap 10.129.2.28 --top-ports=10 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:36 CEST
Nmap scan report for 10.129.2.28
Host is up (0.021s latency).

PORT     STATE    SERVICE
21/tcp   closed   ftp
22/tcp   open     ssh
23/tcp   closed   telnet
25/tcp   open     smtp
80/tcp   open     http
110/tcp  open     pop3
139/tcp  filtered netbios-ssn
443/tcp  closed   https
445/tcp  filtered microsoft-ds
3389/tcp closed   ms-wbt-server
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 1.44 seconds
```

When we scan only the top 10 TCP ports of a target, Nmap shows their state based on the replies it receives. If we trace the packets, we can see the `RST` flag sent back by the target on TCP port 21, which means the port is closed. To focus purely on the behavior of the `SYN scan`, we can disable other discovery features such as `ICMP echo requests (-Pn)`, `DNS resolution (-n)`, and `ARP ping (--disable-arp-ping)`. This ensures that Nmap only performs the raw TCP SYN probing without relying on other methods to determine host availability.

```bash
sudo nmap 10.129.2.28 -p 21 --packet-trace -Pn -n --disable-arp-ping

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:39 CEST
SENT (0.0429s) TCP 10.10.14.2:63090 > 10.129.2.28:21 S ttl=56 id=57322 iplen=44  seq=1699105818 win=1024 <mss 1460>
RCVD (0.0573s) TCP 10.129.2.28:21 > 10.10.14.2:63090 RA ttl=64 id=0 iplen=40  seq=0 win=0
Nmap scan report for 10.129.2.28
Host is up (0.014s latency).

PORT   STATE  SERVICE
21/tcp closed ftp
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.07 seconds
```

We can see from the SENT line that we (`10.10.14.2`) sent a TCP packet with the `SYN` flag (`S`) to our target (`10.129.2.28`). In the next RCVD line, we can see that the target responds with a TCP packet containing the `RST` and `ACK` flags (`RA`). `RST` and `ACK` flags are used to acknowledge receipt of the TCP packet (`ACK`) and to end the TCP session (`RST`).

#### Request

|                         **Message**                         |                                         **Description**                                          |
| :---------------------------------------------------------: | :----------------------------------------------------------------------------------------------: |
|                      `SENT (0.0429s)`                       |            Indicates the SENT operation of Nmap, which sends a packet to the target.             |
|                            `TCP`                            |             Shows the protocol that is being used to interact with the target port.              |
|                    `10.10.14.2:63090 >`                     | Represents our IPv4 address and the source port, which will be used by Nmap to send the packets. |
|                      `10.129.2.28:21`                       |                        Shows the target IPv4 address and the target port.                        |
|                             `S`                             |                                 SYN flag of the sent TCP packet.                                 |
| `ttl=56 id=57322 iplen=44 seq=1699105818 win=1024 mss 1460` |                                Additional TCP Header parameters.                                 |

#### Response

|            **Message**             |                                  **Description**                                  |
| :--------------------------------: | :-------------------------------------------------------------------------------: |
|          `RCVD (0.0573s)`          |                   Indicates a received packet from the target.                    |
|               `TCP`                |                      Shows the protocol that is being used.                       |
|         `10.129.2.28:21 >`         | Represents targets IPv4 address and the source port, which will be used to reply. |
|         `10.10.14.2:63090`         |           Shows our IPv4 address and the port that will be replied to.            |
|                `RA`                |                     RST and ACK flags of the sent TCP packet.                     |
| `ttl=64 id=0 iplen=40 seq=0 win=0` |                         Additional TCP Header parameters.                         |

---

The `TCP Connect Scan (-sT)` completes the full `three-way handshake` instead of stopping mid-way like a `SYN scan (-sS)`.

Nmap sends an `SYN` to the target port. If it receives an `SYN-ACK`, it replies with an `ACK` to finish the handshake. At that point, Nmap knows the port is `open`. If instead it gets an `RST`, the port is `closed`.

Because it relies on the system’s standard `socket API`, `-sT` doesn’t need `raw packet` privileges, so it’s the default scan type when not running as `root`. The trade-off is that it’s noisier since the connection is fully established, and `logs` on the target will show real `connection attempts`.

#### Connect Scan on TCP Port 443

```bash
udo nmap 10.129.2.28 -p 443 --packet-trace --disable-arp-ping -Pn -n --reason -sT 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:26 CET
CONN (0.0385s) TCP localhost > 10.129.2.28:443 => Operation now in progress
CONN (0.0396s) TCP localhost > 10.129.2.28:443 => Connected
Nmap scan report for 10.129.2.28
Host is up, received user-set (0.013s latency).

PORT    STATE SERVICE REASON
443/tcp open  https   syn-ack

Nmap done: 1 IP address (1 host up) scanned in 0.04 seconds
```

---

#### Filtered Ports

When a port is marked as `filtered`, it usually means a `firewall` or some `filtering mechanism` is interfering.

If packets are `dropped`, `Nmap` sees no reply at all and can’t tell if the port is really closed or just being silently blocked. If packets are `rejected`, the target may send back an `ICMP unreachable` or a `TCP RST`.

```bash
sudo nmap 10.129.2.28 -p 139 --packet-trace -n --disable-arp-ping -Pn

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:45 CEST
SENT (0.0381s) TCP 10.10.14.2:60277 > 10.129.2.28:139 S ttl=47 id=14523 iplen=44  seq=4175236769 win=1024 <mss 1460>
SENT (1.0411s) TCP 10.10.14.2:60278 > 10.129.2.28:139 S ttl=45 id=7372 iplen=44  seq=4175171232 win=1024 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up.

PORT    STATE    SERVICE
139/tcp filtered netbios-ssn
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 2.06 seconds
```

| **Scanning Options** |           **Description**            |
| :------------------: | :----------------------------------: |
|    `10.129.2.28`     |     Scans the specified target.      |
|       `-p 139`       |    Scans only the specified port.    |
|   `--packet-trace`   | Shows all packets sent and received. |
|         `-n`         |       Disables DNS resolution.       |
| `--disable-arp-ping` |          Disables ARP ping.          |
|        `-Pn`         |     Disables ICMP Echo requests.     |

In this case, `Nmap` behavior shows the difference between `dropped` and `rejected` packets.

When the firewall `drops` the SYN packets, `Nmap` waits, retries, and the scan takes longer (2.06s). This delay happens because no response comes back, so `Nmap` assumes possible packet loss and keeps probing.

When the firewall `rejects` connections (like on TCP port `445`), the target immediately responds with an error (for example `TCP RST` or `ICMP unreachable`). `Nmap` can instantly classify the port as `filtered` without retries, making that scan faster (~0.05s).

```bash
sudo nmap 10.129.2.28 -p 445 --packet-trace -n --disable-arp-ping -Pn

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:55 CEST
SENT (0.0388s) TCP 10.129.2.28:52472 > 10.129.2.28:445 S ttl=49 id=21763 iplen=44  seq=1418633433 win=1024 <mss 1460>
RCVD (0.0487s) ICMP [10.129.2.28 > 10.129.2.28 Port 445 unreachable (type=3/code=3) ] IP [ttl=64 id=20998 iplen=72 ]
Nmap scan report for 10.129.2.28
Host is up (0.0099s latency).

PORT    STATE    SERVICE
445/tcp filtered microsoft-ds
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.05 seconds
```

As a response, we receive an `ICMP` reply with `type 3` and `error code 3`, which indicates that the desired port is unreachable.

---

# Discovering Open UDP Ports

`UDP` scans (`-sU`) are often overlooked by administrators since they mainly focus on `TCP` filtering. But `UDP` is `stateless` and does not use a three-way handshake, meaning there’s no direct acknowledgment. Because of this, `Nmap` must wait longer for a response (or lack of one), which increases the timeout and makes `UDP` scanning significantly slower compared to `TCP SYN` scans (`-sS`).

#### UDP Port Scan

```bash
sudo nmap 10.129.2.28 -F -sU

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:01 CEST
Nmap scan report for 10.129.2.28
Host is up (0.059s latency).
Not shown: 95 closed ports
PORT     STATE         SERVICE
68/udp   open|filtered dhcpc
137/udp  open          netbios-ns
138/udp  open|filtered netbios-dgm
631/udp  open|filtered ipp
5353/udp open          zeroconf
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 98.07 seconds
```

| **Scanning Options** |       **Description**       |
| :------------------: | :-------------------------: |
|    `10.129.2.28`     | Scans the specified target. |
|         `-F`         |    Scans top 100 ports.     |
|        `-sU`         |    Performs a UDP scan.     |
`UDP` scans suffer from the disadvantage that most of the time no response is received. `Nmap` sends empty datagrams, and unless the service on the `UDP` port is configured to respond, there is no indication that the packet even arrived. This makes it difficult to distinguish between an `open` port and a packet that was simply dropped, often resulting in an `open|filtered` state.

```bash
sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 137 --reason 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:15 CEST
SENT (0.0367s) UDP 10.10.14.2:55478 > 10.129.2.28:137 ttl=57 id=9122 iplen=78
RCVD (0.0398s) UDP 10.129.2.28:137 > 10.10.14.2:55478 ttl=64 id=13222 iplen=257
Nmap scan report for 10.129.2.28
Host is up, received user-set (0.0031s latency).

PORT    STATE SERVICE    REASON
137/udp open  netbios-ns udp-response ttl 64
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.04 seconds
```

If we get an ICMP response with `error code 3` (port unreachable), we know that the port is indeed `closed`.

```bash
sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 100 --reason 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:25 CEST
SENT (0.0445s) UDP 10.10.14.2:63825 > 10.129.2.28:100 ttl=57 id=29925 iplen=28
RCVD (0.1498s) ICMP [10.129.2.28 > 10.10.14.2 Port unreachable (type=3/code=3) ] IP [ttl=64 id=11903 iplen=56 ]
Nmap scan report for 10.129.2.28
Host is up, received user-set (0.11s latency).

PORT    STATE  SERVICE REASON
100/udp closed unknown port-unreach ttl 64
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in  0.15 seconds
```


