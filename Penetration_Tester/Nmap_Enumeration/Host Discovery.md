When conducting an internal `penetration test` across a company `network`, the first step is to identify which `systems` are online and available. `Nmap` provides multiple `host discovery` techniques to check if `targets` are alive. The most effective method is using `ICMP echo requests`. Every `scan` should be stored for later `comparison`, `documentation`, and `reporting`, since different `tools` can produce varying `results`. This helps distinguish which `tool` generated which `findings`.

---
#### Scan Network Range

```bash
sudo nmap 10.129.2.0/24 -sn -oA tnet | grep for | cut -d" " -f5

10.129.2.4
10.129.2.10
10.129.2.11
10.129.2.18
10.129.2.19
10.129.2.20
10.129.2.28
```

| **Scanning Options** |                         **Description**                          |
| :------------------: | :--------------------------------------------------------------: |
|   `10.129.2.0/24`    |                      Target network range.                       |
|        `-sn`         |                     Disables port scanning.                      |
|      `-oA tnet`      | Stores the results in all formats starting with the name 'tnet'. |

---
#### Scan IP List

During an internal `penetration test`, it is common to be provided with an `IP list` containing the `hosts` that need to be tested. `Nmap` supports working with such `lists`, allowing it to read `hosts` directly from the file instead of manually typing them in.

```bash
cat hosts.lst

10.129.2.4
10.129.2.10
10.129.2.11
10.129.2.18
10.129.2.19
10.129.2.20
10.129.2.28
```

If we use the same `scanning` technique on the predefined list, the command will look like this:

```bash
sudo nmap -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f5

10.129.2.18
10.129.2.19
10.129.2.20
```

| **Scanning Options** |                           **Description**                            |
| :------------------: | :------------------------------------------------------------------: |
|        `-sn`         |                       Disables port scanning.                        |
|      `-oA tnet`      |   Stores the results in all formats starting with the name 'tnet'.   |
|        `-iL`         | Performs defined scans against targets in provided 'hosts.lst' list. |

In this example, only 3 out of 7 `hosts` are active. The other `hosts` may appear inactive because their `firewall` configurations block default `ICMP echo requests`. Since `Nmap` receives no response, it marks those `hosts` as inactive.

---

#### Scan Multiple IPs

It can also happen that only a small part of a `network` needs to be scanned. Instead of scanning a whole range, an alternative method is to specify multiple `IP addresses` directly.

```bash
sudo nmap -sn -oA tnet 10.129.2.18 10.129.2.19 10.129.2.20| grep for | cut -d" " -f5

10.129.2.18
10.129.2.19
10.129.2.20
```

If these IP addresses are next to each other, we can also define the range in the respective octet.

```bash
sudo nmap -sn -oA tnet 10.129.2.18-20| grep for | cut -d" " -f5

10.129.2.18
10.129.2.19
10.129.2.20
```

---

#### Scan Single IP

When we disable port scanning with `-sn`, `Nmap` automatically performs a ping scan using `ICMP Echo Requests` (`-PE`). Once an echo request is sent, the expectation is to receive an `ICMP reply` if the host is alive. In earlier scans, this did not occur because before `Nmap` could send an `ICMP Echo Request`, it first sent an `ARP ping` and received an `ARP reply`. This behavior can be verified using the `--packet-trace` option. To explicitly ensure `ICMP Echo Requests` are sent, we define the `-PE` option.

```bash
sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 00:08 CEST
SENT (0.0074s) ARP who-has 10.129.2.18 tell 10.10.14.2
RCVD (0.0309s) ARP reply 10.129.2.18 is-at DE:AD:00:00:BE:EF
Nmap scan report for 10.129.2.18
Host is up (0.023s latency).
MAC Address: DE:AD:00:00:BE:EF
Nmap done: 1 IP address (1 host up) scanned in 0.05 seconds
```

| **Scanning Options** |                             **Description**                              |
| :------------------: | :----------------------------------------------------------------------: |
|    `10.129.2.18`     |                Performs defined scans against the target.                |
|        `-sn`         |                         Disables port scanning.                          |
|      `-oA host`      |     Stores the results in all formats starting with the name 'host'.     |
|        `-PE`         | Performs the ping scan by using 'ICMP Echo requests' against the target. |
|   `--packet-trace`   |                   Shows all packets sent and received                    |

Another way to determine why Nmap has our target marked as "alive" is with the "`--reason`" option.

```bash
sudo nmap 10.129.2.18 -sn -oA host -PE --reason 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 00:10 CEST
SENT (0.0074s) ARP who-has 10.129.2.18 tell 10.10.14.2
RCVD (0.0309s) ARP reply 10.129.2.18 is-at DE:AD:00:00:BE:EF
Nmap scan report for 10.129.2.18
Host is up, received arp-response (0.028s latency).
MAC Address: DE:AD:00:00:BE:EF
Nmap done: 1 IP address (1 host up) scanned in 0.03 seconds
```

| **Scanning Options** |                             **Description**                              |
| :------------------: | :----------------------------------------------------------------------: |
|    `10.129.2.18`     |                Performs defined scans against the target.                |
|        `-sn`         |                         Disables port scanning.                          |
|      `-oA host`      |     Stores the results in all formats starting with the name 'host'.     |
|        `-PE`         | Performs the ping scan by using 'ICMP Echo requests' against the target. |
|      `--reason`      |                 Displays the reason for specific result.                 |

---

We see that `Nmap` can detect whether a host is alive through `ARP requests` and `ARP replies` alone. To avoid this and force `Nmap` to rely on `ICMP Echo Requests`, we can disable ARP pings using the `--disable-arp-ping` option. After disabling ARP pings, scanning the target again allows us to observe only the `ICMP packets` being sent and received.

```bash
sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace --disable-arp-ping 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 00:12 CEST
SENT (0.0107s) ICMP [10.10.14.2 > 10.129.2.18 Echo request (type=8/code=0) id=13607 seq=0] IP [ttl=255 id=23541 iplen=28 ]
RCVD (0.0152s) ICMP [10.129.2.18 > 10.10.14.2 Echo reply (type=0/code=0) id=13607 seq=0] IP [ttl=128 id=40622 iplen=28 ]
Nmap scan report for 10.129.2.18
Host is up (0.086s latency).
MAC Address: DE:AD:00:00:BE:EF
Nmap done: 1 IP address (1 host up) scanned in 0.11 seconds
```

