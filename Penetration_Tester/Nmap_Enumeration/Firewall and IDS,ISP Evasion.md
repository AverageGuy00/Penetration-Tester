# Firewalls

A `firewall` is a `security` measure against unauthorized connection attempts from `external networks`. Every `firewall` `security system` is based on a `software component` that monitors `network traffic` between the `firewall` and incoming `data connections` and decides how to handle the connection based on the `rules` that have been set. It checks whether individual `network packets` are being passed, ignored, or blocked. This mechanism is designed to prevent unwanted `connections` that could be potentially dangerous.

---
# IDP/IPS

Like the `firewall`, the `intrusion detection system (IDS)` and `intrusion prevention system (IPS)` are also `software-based components`. `IDS` scans the `network` for potential `attacks`, analyzes them, and reports any detected `attacks`. `IPS` complements `IDS` by taking specific defensive measures if a potential `attack` should have been detected. The analysis of such `attacks` is based on `pattern matching` and `signatures`. If specific `patterns` are detected, such as a `service detection scan`, `IPS` may prevent the pending `connection attempts`.

---

## Determine Firewalls and Their Rules

When a `port` is shown as `filtered`, it can have several reasons. In most cases, `firewalls` have certain `rules` set to handle specific `connections`. The `packets` can either be `dropped`, or `rejected`. The `dropped packets` are ignored, and no `response` is returned from the `host`.

This is different for `rejected packets`, which elicit an explicit `response`. `TCP packets` are returned with an `RST flag`, while `ICMP` can contain different types of `error codes`.

Such `errors` include:

- Net Unreachable  
- Net Prohibited  
- Host Unreachable  
- Host Prohibited  
- Port Unreachable  
- Proto Unreachable
---

### `TCP ACK Scan (-sA)` Behavior

**What the `firewall` sees**

- Normal scans with `SYN flag`: looks like a new `connection attempt` → usually **blocked**.
- `ACK flag`: looks like traffic from an **already established connection** → often **passed through**, because the firewall can’t confirm if the session started internally.
    

**What the `host` sees**

- Receives an `ACK packet` out of nowhere (no existing session).
- If the `port` is `open` → host replies with `RST flag`.
- If the `port` is `closed` → host still replies with `RST flag`.
- If the `port` is `filtered` → no reply at all (packet dropped by the firewall).

## SYN-Scan

```bash
sudo nmap 10.129.2.28 -p 21,22,25 -sS -Pn -n --disable-arp-ping --packet-trace

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-21 14:56 CEST
SENT (0.0278s) TCP 10.10.14.2:57347 > 10.129.2.28:22 S ttl=53 id=22412 iplen=44  seq=4092255222 win=1024 <mss 1460>
SENT (0.0278s) TCP 10.10.14.2:57347 > 10.129.2.28:25 S ttl=50 id=62291 iplen=44  seq=4092255222 win=1024 <mss 1460>
SENT (0.0278s) TCP 10.10.14.2:57347 > 10.129.2.28:21 S ttl=58 id=38696 iplen=44  seq=4092255222 win=1024 <mss 1460>
RCVD (0.0329s) ICMP [10.129.2.28 > 10.10.14.2 Port 21 unreachable (type=3/code=3) ] IP [ttl=64 id=40884 iplen=72 ]
RCVD (0.0341s) TCP 10.129.2.28:22 > 10.10.14.2:57347 SA ttl=64 id=0 iplen=44  seq=1153454414 win=64240 <mss 1460>
RCVD (1.0386s) TCP 10.129.2.28:22 > 10.10.14.2:57347 SA ttl=64 id=0 iplen=44  seq=1153454414 win=64240 <mss 1460>
SENT (1.1366s) TCP 10.10.14.2:57348 > 10.129.2.28:25 S ttl=44 id=6796 iplen=44  seq=4092320759 win=1024 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up (0.0053s latency).

PORT   STATE    SERVICE
21/tcp filtered ftp
22/tcp open     ssh
25/tcp filtered smtp
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.07 seconds
```

### `SYN scan (-sS)` results

- Port `21/tcp` → **filtered** (firewall dropped the packet, no SYN/ACK or RST back, only ICMP unreachable)
- Port `22/tcp` → **open** (host replied with SYN/ACK)
- Port `25/tcp` → **filtered** (same as port 21, firewall interference)

## ACK-Scan

```bash
sudo nmap 10.129.2.28 -p 21,22,25 -sA -Pn -n --disable-arp-ping --packet-trace

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-21 14:57 CEST
SENT (0.0422s) TCP 10.10.14.2:49343 > 10.129.2.28:21 A ttl=49 id=12381 iplen=40  seq=0 win=1024
SENT (0.0423s) TCP 10.10.14.2:49343 > 10.129.2.28:22 A ttl=41 id=5146 iplen=40  seq=0 win=1024
SENT (0.0423s) TCP 10.10.14.2:49343 > 10.129.2.28:25 A ttl=49 id=5800 iplen=40  seq=0 win=1024
RCVD (0.1252s) ICMP [10.129.2.28 > 10.10.14.2 Port 21 unreachable (type=3/code=3) ] IP [ttl=64 id=55628 iplen=68 ]
RCVD (0.1268s) TCP 10.129.2.28:22 > 10.10.14.2:49343 R ttl=64 id=0 iplen=40  seq=1660784500 win=0
SENT (1.3837s) TCP 10.10.14.2:49344 > 10.129.2.28:25 A ttl=59 id=21915 iplen=40  seq=0 win=1024
Nmap scan report for 10.129.2.28
Host is up (0.083s latency).

PORT   STATE      SERVICE
21/tcp filtered   ftp
22/tcp unfiltered ssh
25/tcp filtered   smtp
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.15 seconds
```

### `ACK scan (-sA)` results

- Port `21/tcp` → **filtered** (no valid RST back, ICMP unreachable seen)
- Port `22/tcp` → **unfiltered** (host sent an RST — meaning the packet got through the firewall, regardless of port state)
- Port `25/tcp` → **filtered** (again, no RST, only ICMP unreachable)

| **Scanning Options** |            **Description**            |
| :------------------: | :-----------------------------------: |
|    `10.129.2.28`     |      Scans the specified target.      |
|    `-p 21,22,25`     |    Scans only the specified ports.    |
|        `-sS`         | Performs SYN scan on specified ports. |
|        `-sA`         | Performs ACK scan on specified ports. |
|        `-Pn`         |     Disables ICMP Echo requests.      |
|         `-n`         |       Disables DNS resolution.        |
| `--disable-arp-ping` |          Disables ARP ping.           |
|   `--packet-trace`   | Shows all packets sent and received.  |

---

# Detect IDS/IPS

`IDS` are passive monitoring systems that examine `connections` and alert `administrators` when suspicious `packets` are detected. `IPS` actively prevents potential `attacks` by taking automated actions. `IPS` complements `IDS`.

During a `penetration test`, multiple `VPS` with different `IP` addresses are useful to test for `IDS/IPS`. If an `IP` gets blocked, it indicates `monitoring` and active countermeasures.

`IDS` only detects and alerts; `IPS` blocks `connections`. Aggressive `scanning` can trigger responses, revealing the presence of `monitoring systems`. If one `VPS` is blocked, switching to another confirms `IPS` activity.

Quiet, disguised `scanning` is necessary to avoid `detection` and `blocking`.

---

# Decoys

`Administrators` may block entire `subnets` to prevent access. `IPS` can also block us directly. To bypass this, `Nmap` supports `Decoy scanning (-D)`.

This method forges multiple fake `IP addresses` in the `IP header` so the real source is hidden. For example, if 5 `decoys` are generated, our real `IP` might be placed in the 2nd slot among them.

The `decoy IPs` must be alive; otherwise, `SYN-flooding protections` could block the `service` and make the `target` unreachable.

#### Scan by Using Decoys

```bash
sudo nmap 10.129.2.28 -p 80 -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-21 16:14 CEST
SENT (0.0378s) TCP 102.52.161.59:59289 > 10.129.2.28:80 S ttl=42 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0378s) TCP 10.10.14.2:59289 > 10.129.2.28:80 S ttl=59 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0379s) TCP 210.120.38.29:59289 > 10.129.2.28:80 S ttl=37 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0379s) TCP 191.6.64.171:59289 > 10.129.2.28:80 S ttl=38 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0379s) TCP 184.178.194.209:59289 > 10.129.2.28:80 S ttl=39 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
SENT (0.0379s) TCP 43.21.121.33:59289 > 10.129.2.28:80 S ttl=55 id=29822 iplen=44  seq=3687542010 win=1024 <mss 1460>
RCVD (0.1370s) TCP 10.129.2.28:80 > 10.10.14.2:59289 SA ttl=64 id=0 iplen=44  seq=4056111701 win=64240 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up (0.099s latency).

PORT   STATE SERVICE
80/tcp open  http
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.15 seconds
```

| **Scanning Options** |                                      **Description**                                       |
| :------------------: | :----------------------------------------------------------------------------------------: |
|    `10.129.2.28`     |                                Scans the specified target.                                 |
|       `-p 80`        |                              Scans only the specified ports.                               |
|        `-sS`         |                           Performs SYN scan on specified ports.                            |
|        `-Pn`         |                                Disables ICMP Echo requests.                                |
|         `-n`         |                                  Disables DNS resolution.                                  |
| `--disable-arp-ping` |                                     Disables ARP ping.                                     |
|   `--packet-trace`   |                            Shows all packets sent and received.                            |
|      `-D RND:5`      | Generates five random IP addresses that indicates the source IP the connection comes from. |

`Spoofed packets` are usually filtered by `ISPs` and `routers`. To bypass this, real `VPS` IPs can be used together with `IP ID manipulation` in the `IP header` to scan the target.

If only specific `subnets` are blocked from accessing services, a different `source IP (-S)` can be set to test for better results.

`Decoys` can be applied to `SYN scans`, `ACK scans`, `ICMP scans`, and `OS detection scans` to disguise the true source of traffic.

#### Testing Firewall Rule

```bash
sudo nmap 10.129.2.28 -n -Pn -p445 -O

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-22 01:23 CEST
Nmap scan report for 10.129.2.28
Host is up (0.032s latency).

PORT    STATE    SERVICE
445/tcp filtered microsoft-ds
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
Too many fingerprints match this host to give specific OS details
Network Distance: 1 hop

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 3.14 seconds
```

#### Scan by Using Different Source IP

```bash
sudo nmap 10.129.2.28 -n -Pn -p 445 -O -S 10.129.2.200 -e tun0

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-22 01:16 CEST
Nmap scan report for 10.129.2.28
Host is up (0.010s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 2.6.32 (96%), Linux 3.2 - 4.9 (96%), Linux 2.6.32 - 3.10 (96%), Linux 3.4 - 3.10 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), Synology DiskStation Manager 5.2-5644 (94%), Linux 2.6.32 - 2.6.35 (94%), Linux 2.6.32 - 3.5 (94%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 4.11 seconds
```

| **Scanning Options** |                    **Description**                     |
| :------------------: | :----------------------------------------------------: |
|    `10.129.2.28`     |              Scans the specified target.               |
|         `-n`         |                Disables DNS resolution.                |
|        `-Pn`         |              Disables ICMP Echo requests.              |
|       `-p 445`       |            Scans only the specified ports.             |
|         `-O`         |       Performs operation system detection scan.        |
|         `-S`         | Scans the target by using different source IP address. |
|    `10.129.2.200`    |            Specifies the source IP address.            |
|      `-e tun0`       |  Sends all requests through the specified interface.   |

---

# DNS Proxying

By default, `Nmap` does reverse `DNS resolution` over `UDP 53`. `TCP 53` was mainly for `zone transfers` or large responses, but with `IPv6` and `DNSSEC` many queries now use `TCP 53` too.

We can set custom `DNS servers` with `--dns-server <ns>`. In a `DMZ`, internal `DNS` servers are often more trusted, so they can be used to reach internal hosts.

We can also run scans using `--source-port 53`. If a `firewall` trusts `DNS traffic` and `IDS/IPS` is weak, `TCP packets` disguised as DNS will likely pass through.

#### SYN-Scan of a Filtered Port

```bash
sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-21 22:50 CEST
SENT (0.0417s) TCP 10.10.14.2:33436 > 10.129.2.28:50000 S ttl=41 id=21939 iplen=44  seq=736533153 win=1024 <mss 1460>
SENT (1.0481s) TCP 10.10.14.2:33437 > 10.129.2.28:50000 S ttl=46 id=6446 iplen=44  seq=736598688 win=1024 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up.

PORT      STATE    SERVICE
50000/tcp filtered ibm-db2

Nmap done: 1 IP address (1 host up) scanned in 2.06 seconds
```

#### SYN-Scan From DNS Port

```bash
sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace --source-port 53

SENT (0.0482s) TCP 10.10.14.2:53 > 10.129.2.28:50000 S ttl=58 id=27470 iplen=44  seq=4003923435 win=1024 <mss 1460>
RCVD (0.0608s) TCP 10.129.2.28:50000 > 10.10.14.2:53 SA ttl=64 id=0 iplen=44  seq=540635485 win=64240 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up (0.013s latency).

PORT      STATE SERVICE
50000/tcp open  ibm-db2
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.08 seconds
```

Using `Netcat` with `--source-port 53`, you test whether you can actually make a full connection through the `firewall` using this trick. If it works, you’ve basically bypassed the `firewall` and possibly the `IDS/IPS`, gaining access to a service (like `ProFTPd` in the example) that would normally be blocked.

#### Connect To The Filtered Port

```bash
ncat -nv --source-port 53 10.129.2.28 50000

Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Connected to 10.129.2.28:50000.
220 ProFTPd
```

---

# Evasion methodology

- Start with a normal `Nmap` `-sV` `-O` scan to attempt `OS fingerprinting`. If successful, stop here. If blocked, it means `firewall` / `IDS` / `IPS` interference.
 
- Adjust scan `timing` and stealth. Use lower `-T` values (`-T2` / `-T3`) and options like `--scan-delay` or `--max-retries` to slip past detection.
 
- Try `source port manipulation`. Use `--source-port 53` (`DNS`) or `--source-port 443` (`HTTPS`). These are often trusted and may bypass `firewall` rules.

- Use `packet fragmentation` (`-f` or `--mtu`) so `IDS/IPS` fails to reassemble properly. Combine with `decoy scanning` (`-D`) to confuse attribution.

- If `OS detection` (`-O`) still fails, rely on `service version detection` (`-sV`). Look at `service banners` for hints (e.g., `IIS` = `Windows`, `Apache` = `Linux/Unix`).
 
- Check `TTL values` and `TCP window sizes` in responses. `TTL 64` usually means `Linux`, `TTL 128` `Windows`, `TTL 255` network devices.

- Correlate findings from `ports`, `banners`, and `packet behavior` to submit the `operating system`.

---

# Challenge 1

```bash
sudo nmap 10.129.54.210 -sA -sV -p 22,80,10001 -Pn -n --disable-arp-ping --packet-trace --source-port 53
```

# Challenge 2

```bash
sudo nmap 10.129.2.48 -p53 -sV -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5 --source-port 53
```

# Challenge 3

```bash
sudo nmap 10.129.2.48 -sSV -p50000 -n -Pn --disable-arp-ping --packet-trace --source-port 53

sudo nc -nv -p53 10.129.205.90 50000   
```

