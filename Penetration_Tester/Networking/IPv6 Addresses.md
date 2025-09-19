---
noteID: 78652c55-3e76-47b8-9f90-5193f9d17359
custom-width: .nan
---
IPv6 is the **successor to IPv4**, designed to solve the problem of IPv4 address exhaustion. It uses **128-bit addresses** (written in hexadecimal, separated by colons) instead of IPv4’s 32-bit addresses. This provides a vastly larger address space and supports modern networking needs.

An **IPv6 address** is **128 bits (16 bytes)** long, written as **8 blocks of 16 bits** (4 hex digits each) separated by colons `:`.

`[Network Prefix] : [Subnet ID] : [Interface ID / Host Part]`

- **Network Prefix:** Specifies the network the address belongs to, usually the first **48–64 bits**.
- **Subnet ID:** Optional, used to divide networks into subnets (part of the first 64 bits).
- **Interface ID / Host Part:** Usually the last **64 bits**, identifies a specific device interface on the network.

**Example:**

Full IPv6: `fe80:0000:0000:0000:dd80:b1a9:6687:2d3b/64`  
Short IPv6: `fe80::dd80:b1a9:6687:2d3b/64`

- `fe80` → Link-local prefix (`fe80::/64`)
- `0000:0000:0000` → Reserved / subnet portion (zeros)
- `dd80:b1a9:6687:2d3b` → Interface ID / host portion

- First **64 bits (blocks 1–4)** = network + subnet
- Last **64 bits (blocks 5–8)** = host/interface ID
- Consecutive zeros can be compressed with `::`
- `/64` prefix length indicates how many bits are for network/subnet

`IPv6` is a protocol with many new features, which also has many other advantages over IPv4:

- Larger address space
- Address self-configuration (SLAAC)
- Multiple IPv6 addresses per interface
- Faster routing
- End-to-end encryption (IPsec)
- Data packages up to 4 GByte

|    **Features**    |   **IPv4**    |           **IPv6**           |
| :----------------: | :-----------: | :--------------------------: |
|     Bit length     |    32-bit     |           128 bit            |
|     OSI layer      | Network Layer |        Network Layer         |
|  Adressing range   | ~ 4.3 billion |      ~ 340 undecillion       |
|   Representation   |    Binary     |         Hexadecimal          |
|  Prefix notation   | 10.10.10.0/24 | fe80::dd80:b1a9:6687:2d3b/64 |
| Dynamic addressing |     DHCP      |        SLAAC / DHCPv6        |
|       IPsec        |   Optional    |          Mandatory           |

There are three different types of IPv6 addresses:

|  **Type**   |                                **Description**                                 |
| :---------: | :----------------------------------------------------------------------------: |
|  `Unicast`  |                       Addresses for a single interface.                        |
|  `Anycast`  | Addresses for multiple interfaces, where only one of them receives the packet. |
| `Multicast` |     Addresses for multiple interfaces, where all receive the same packet.      |


# Hexadecimal System

|**Decimal**|**Hex**|**Binary**|
|---|---|---|
|1|1|0001|
|2|2|0010|
|3|3|0011|
|4|4|0100|
|5|5|0101|
|6|6|0110|
|7|7|0111|
|8|8|1000|
|9|9|1001|
|10|A|1010|
|11|B|1011|
|12|C|1100|
|13|D|1101|
|14|E|1110|
|15|F|1111|

Let's look at an example with an IPv4, at how the IPv4 address (`192.168.12.160`) would look in hexadecimal representation.

|**Representation**|**1st Octet**|**2nd Octet**|**3rd Octet**|**4th Octet**|
|---|---|---|---|---|
|Binary|1100 0000|1010 1000|0000 1100|1010 0000|
|`Hex`|`C0`|`A8`|`0C`|`A0`|
|Decimal|192|168|12|160|

