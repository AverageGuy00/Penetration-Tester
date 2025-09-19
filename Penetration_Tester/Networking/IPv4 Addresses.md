---
noteID: 95c19cf1-00c1-45c5-b2aa-6fe6d286aae5
---
Each host in a network is identified by a **Media Access Control (MAC) address**, which allows devices to exchange data within the same local network. However, a MAC address alone isn’t enough when communicating with a host on a different network. For connections across networks (like the Internet), devices rely on **IP addressing**. An **IPv4** or **IPv6 address** contains two parts: the **network portion** (which identifies the network) and the **host portion** (which identifies the specific device within that network).

It does not matter whether the network is small (like a home computer network) or as large as the Internet — the **IP address** ensures that data is delivered to the correct receiver.

We can think of addressing like this:

- **IPv4 / IPv6** → Like the **postal address and district** of a building. It tells the network where the destination is located.
- **MAC address** → Like the **exact floor and apartment number** inside that building. It identifies the specific device within the local network.

Additional details:

- A single IP address can target multiple receivers through **broadcasting**.    
- A single device may also respond to multiple IP addresses (multi-homing or virtual interfaces).
- But within a given network, each **IP address must be unique**, otherwise conflicts occur and communication breaks down.

---

# IPv4 Structure

- **IPv4 Address:** 32-bit number divided into 4 octets (8 bits each). 
- **Format:** `A.B.C.D` (decimal), e.g., `192.168.10.25` 
- **Binary:** Each octet is 8 bits → `11000000.10101000.00001010.00011001`

**Subnet / CIDR:**

- **/8:** First 8 bits = network, last 24 bits = host → subnet mask: `255.0.0.0` 
- **/16:** First 16 bits = network, last 16 bits = host → subnet mask: `255.255.0.0`
- **/24:** First 24 bits = network, last 8 bits = host → subnet mask: `255.255.255.0`
- **/32:** All 32 bits = network, host = 0 → subnet mask: `255.255.255.255`

**Parts of an IP Address in Subnetting:**

- **Network IP:** Identifies the network → calculated by ANDing the IP with subnet mask.
   - Example: `192.168.10.25/24` → Network: `192.168.10.0` 
- **Host IP:** Identifies devices within the network → the remaining bits.
   - Example: `192.168.10.25` → Host ID: `25`

Host Range and Broadcast:

- **Usable Hosts:** All IPs between first host (`network+1`) and last host (`broadcast-1`).
- **Broadcast Address:** Last IP in the network → used to send packets to all hosts.
   - Example `/24`: `192.168.10.0` → `192.168.10.255` 
   - First usable host: `192.168.10.1`  
   - Last usable host: `192.168.10.254`

| CIDR |   Subnet Mask   | Network Bits | Host Bits | Usable Hosts | First Address |  Last Address  |          Typical Network Use          |
| :--: | :-------------: | :----------: | :-------: | :----------: | :-----------: | :------------: | :-----------------------------------: |
|  /8  |    255.0.0.0    |      8       |    24     |  16,777,214  |    1.0.0.1    | 1.255.255.254  |          Very large networks          |
| /16  |   255.255.0.0   |      16      |    16     |    65,534    |  172.16.0.1   | 172.16.255.254 |   Medium networks, enterprise LANs    |
| /24  |  255.255.255.0  |      24      |     8     |     254      |  192.168.1.1  | 192.168.1.254  |         Small networks, LANs          |
| /25  | 255.255.255.128 |      25      |     7     |     126      |  192.168.1.1  | 192.168.1.126  |     Splitting /24 into 2 subnets      |
| /26  | 255.255.255.192 |      26      |     6     |      62      |  192.168.1.1  |  192.168.1.62  |   Small subnets, segmented networks   |
| /30  | 255.255.255.252 |      30      |     2     |      2       |  192.168.1.1  |  192.168.1.2   |    Point-to-point links (routers)     |
| /32  | 255.255.255.255 |      32      |     0     |      0       |  192.168.1.1  |  192.168.1.1   | Single host, loopback, firewall rules |

---
# Binary System in IPv4

- **Binary system:** Number system using **two states**, represented as `0` and `1`.
- **Decimal system:** Uses digits `0–9`.

# IPv4 Address Structure

- IPv4 address = 32 bits, divided into **4 octets** (8 bits each).
- Each bit position in an octet has a **specific decimal value**:
   - Bit values (left to right): 128, 64, 32, 16, 8, 4, 2, 1

**Example:** IPv4 Address `192.168.10.39`

| Octet | Binary   | Bit Values                       | Sum |
| ----- | -------- | -------------------------------- | --- |
| 1st   | 11000000 | 128 + 64 + 0 + 0 + 0 + 0 + 0 + 0 | 192 |
| 2nd   | 10101000 | 128 + 0 + 32 + 0 + 8 + 0 + 0 + 0 | 168 |
| 3rd   | 00001010 | 0 + 0 + 0 + 0 + 8 + 0 + 2 + 0    | 10  |
| 4th   | 00100111 | 0 + 0 + 32 + 0 + 0 + 4 + 2 + 1   | 39  |

Key Points:
- Each octet = 8 bits → total 32 bits per IPv4 address.
- Decimal value of an octet = sum of bit values that are `1`.
- Understanding binary helps with **subnetting, CIDR, and network calculations**.

---

# **CIDR (Classless Inter-Domain Routing)**

- **Definition:** CIDR is a method of representing IP addresses that **replaces the fixed class-based system (A, B, C, D, E)**.
- It allows **flexible subnetting** by dividing the IPv4 address space into subnets of any size. 
- The **CIDR suffix** (`/n`) indicates **how many bits from the start of the IP address belong to the network portion**.