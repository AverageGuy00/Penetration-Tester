
Subnetting is the process of **dividing a larger IP network into smaller, more manageable subnetworks** (subnets). It separates the IP address into a **network portion** and a **host portion**, allowing efficient use of IP addresses, improved network performance, and better organization of devices within a network.

**Subnet Mask Basics**

- **Subnet mask** divides an IP address into **network** and **host** parts.
- Binary `1` bits = network, binary `0` bits = host.
- Example: 255.255.255.192 → binary: `11111111.11111111.11111111.11000000`
  - First 26 bits = network
  - Last 6 bits = host


**Why not 255.255.255.255?**

- 255.255.255.255 → all 32 bits are network bits → single host, no room for other devices.
- Subnets need host bits to allow multiple devices to communicate.

**Valid Subnet Masks**

- Must have **contiguous 1s followed by contiguous 0s**. 
- Last octet valid sums of powers of 2: 128, 192, 224, 240, 248, 252, 254, 255.
- Invalid example: 255.255.255.64 → binary: `11111111.11111111.11111111.01000000` (non-contiguous 1s).

**Common Subnet Masks Tables**

```nginx
Subnet Mask      | CIDR | Binary                              | Usable Hosts
255.255.255.0    | /24  | 11111111.11111111.11111111.00000000 | 254
255.255.255.128  | /25  | 11111111.11111111.11111111.10000000 | 126
255.255.255.192  | /26  | 11111111.11111111.11111111.11000000 | 62
255.255.255.224  | /27  | 11111111.11111111.11111111.11100000 | 30
255.255.255.240  | /28  | 11111111.11111111.11111111.11110000 | 14
255.255.255.248  | /29  | 11111111.11111111.11111111.11111000 | 6
255.255.255.252  | /30  | 11111111.11111111.11111111.11111100 | 2
255.255.255.254  | /31  | 11111111.11111111.11111111.11111110 | 2 (point-to-point)
255.255.255.255  | /32  | 11111111.11111111.11111111.11111111 | 1
```

**Usable Hosts Formula**

- Usable hosts = 2^(host bits) – 2 (subtract network & broadcast)
- Special cases:
  - /31 → 2 usable hosts (point-to-point link)  
  - /32 → 1 usable host

**Binary Powers of 2 in Octet**
```yaml
Bit position:  1   2   3   4   5   6   7   8
Binary value: 128  64  32  16  8   4   2   1
```

- Subnet mask values are sums of leftmost bits: 128, 192, 224, 240, 248, 252, 254, 255.

**Example /28 Subnet**

- 192.168.1.16/28 → 16 total IPs, 14 usable hosts
- Network: 192.168.1.16  
- Usable: 192.168.1.17 → 192.168.1.30 
- Broadcast: 192.168.1.31

---

We already know that an IP address is divided into the `network part` and the `host part`.

Breaking it by Octets:

|       Detail        |             Octet 1             |             Octet 2             |             Octet 3             |                       Octet 4                       |
| :-----------------: | :-----------------------------: | :-----------------------------: | :-----------------------------: | :-------------------------------------------------: |
|     IPv4 Binary     |            11000000             |            10101000             |            00001100             |                      10100000                       |
|    IPv4 Decimal     |               192               |               168               |               12                |                         160                         |
| Subnet Mask Binary  |            11111111             |            11111111             |            11111111             |                      11000000                       |
| Subnet Mask Decimal |               255               |               255               |               255               |                         192                         |
|        Notes        | All bits fixed, part of network | All bits fixed, part of network | All bits fixed, part of network | First 2 bits fixed (network), last 6 bits for hosts |

- **Network part:** First 26 bits (all 1s in mask) → determines the subnet
- **Host part:** Last 6 bits (0s in mask) → can vary for devices within this subnet


`Network Part` - The portion of the IP address defined by the `1`s in the subnet mask. These bits are fixed and identify the subnet (the "main network"). <u>Example: 192.168.12 (first three octets)</u>

`Host Part` - The portion of the IP address defined by the `0`s in the subnet mask. These bits can change and are used to assign unique addresses to devices within the subnet. <u>Example: Last octet 0 → 255</u>

`Network Address` - The first address in the subnet, found by setting all host bits to `0`. It identifies the subnet itself and cannot be assigned to a host. <u>Example: 192.168.12.0</u>

`Broadcast Address` - The last address in the subnet, found by setting all host bits to `1`. It is reserved for sending data to all hosts in the subnet and cannot be assigned to a single device. 
<u>Example: 192.168.12.255</u>

# Subnetting Into Smaller Networks

We start with subnet **192.168.12.128/26**

- `/26 = 26 bits fixed for the network.`  
   From the CIDR `/26`. That means:  
   32 total bits − 26 fixed = 6 host bits.
- `Subnet mask = 255.255.255.192.`  
   Take 26 ones in binary:  
   `11111111.11111111.11111111.11000000` = 255.255.255.192.
- `Total addresses = 64 (2^6).`  
   Those 6 host bits can form 2^6 = 64 combinations.  
  So there are 64 total addresses in this subnet.
- `Usable = 62 (64 − 2).`  
  From the 64 total addresses:  
  1 reserved as network address,  
  1 reserved as broadcast address.  
  Remaining usable = 64 − 2 = 62.

We need **4 subnets**.

- Borrow 1 bit → 2 subnets
- Borrow 2 bits → 4 subnets

So we borrow **2 bits** from the host part.

Original mask = `/26` → `11111111.11111111.11111111.11000000`  
After borrowing = `/28` → `11111111.11111111.11111111.11110000`  
Decimal mask = **255.255.255.240**

New host bits = 32 − 28 = **4**  
Total addresses per subnet = `2^4 = 16`  
Usable hosts = 16 − 2 = **14**  
Number of subnets = `2^2 = 4`

Each subnet has a **block size of 16** (because 2^4 = 16).
We add 16 step by step, starting from 192.168.12.128.

| Subnet | Network Address | First Host     | Last Host      | Broadcast Address | CIDR |
| ------ | --------------- | -------------- | -------------- | ----------------- | ---- |
| 1      | 192.168.12.128  | 192.168.12.129 | 192.168.12.142 | 192.168.12.143    | /28  |
| 2      | 192.168.12.144  | 192.168.12.145 | 192.168.12.158 | 192.168.12.159    | /28  |
| 3      | 192.168.12.160  | 192.168.12.161 | 192.168.12.174 | 192.168.12.175    | /28  |
| 4      | 192.168.12.176  | 192.168.12.177 | 192.168.12.190 | 192.168.12.191    | /28  |
# Mental Subnetting

Subnetting works in powers of two. Instead of heavy math, you only need to remember where the subnet mask ends and how many host bits are left.

**Octet boundaries**

/8 → 1st octet can change  
/16 → 2nd octet can change  
/24 → 3rd octet can change  
/32 → no change (single host)

**Remainder method**

Divide the CIDR by 8. The remainder tells you how many **bits are borrowed** in the next octet.
- Example: /25 → 25 ÷ 8 = 3 remainder 1 → 1 bit borrowed in the 4th octet.

**Subnet size**

Each octet has 8 bits.  
Formula: `2^(8 – remainder)` = block size.
a
- Remainder 0 → 256
- Remainder 1 → 128 
- Remainder 2 → 64 
- Remainder 3 → 32
- Remainder 4 → 16
- Remainder 5 → 8
- Remainder 6 → 4
- Remainder 7 → 2

**IP ranges**

- First address = **network address** (not usable)
- Last address = **broadcast address** (not usable)
- Usable = everything in between.

**Example /25**

- Block size = 128 → ranges go in jumps of 128 
- 192.168.1.0 – 192.168.1.127 → network = .0, broadcast = .127, usable = .1–.126
- 192.168.1.128 – 192.168.1.255 → network = .128, broadcast = .255, usable = .129–.254





---

# Gateway

A **gateway (router)** is the device that connects different networks (subnets).

- If your computer thinks an IP is **outside its own subnet**, it sends the traffic to the gateway.
- The gateway then forwards it to the correct subnet.

Example:

- Your IP: `10.20.0.50/25` (first half)
- Target IP: `10.20.0.200` (second half)
- Without routing, your PC won’t reach it.
- With routing: your PC sends traffic to its gateway (`10.20.0.1`), which knows how to reach the second half (`10.20.0.128/25`)

---

**Extra Points – Network Segmentation**

**Web Server (DMZ)**  
Isolate in a DMZ since it’s internet-facing and more likely to be compromised.

**Workstations**  
Should be on their own network, ideally with host firewalls to block workstation-to-workstation traffic.

**Switches & Routers**  
Use a dedicated admin network to stop snooping or OSPF abuse.

**IP Phones**  
Separate network for security (stop eavesdropping) and performance (low latency).

**Printers**  
Own network, since they’re insecure, can leak NTLMv2 credentials, and often hold sensitive data.bas

---

