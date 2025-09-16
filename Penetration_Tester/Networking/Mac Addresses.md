A MAC address is 6 bytes (48 bits) long. It is split into two parts.

The **first 3 bytes (24 bits)** are the <u>Organizationally Unique Identifier (OUI)</u>. This section is assigned by the IEEE to each manufacturer. It ensures that devices from the same vendor share a common prefix.

The **last 3 bytes (24 bits)** are the **<u>NIC (Network Interface Controller)</u> specific part**. This section is assigned by the manufacturer to make the full MAC unique for each device.

Example: `DE:AD:BE:EF:13:37`  
Binary representation: `11011110 10101101 10111110 11101111 00010011 00110111`  
Hex representation: `DE AD BE EF 13 37`

In this case:  
OUI (first half) = `DE:AD:BE`  
NIC-specific part (second half) = `EF:13:37`

This separation ensures that every MAC address worldwide is unique, combining the manufacturer ID with the individual device ID.

---

# **IP-to-MAC Delivery and ARP**

When a host wants to send data, it first checks if the **target IP address** is in the same subnet. If it is, the data is sent directly to the target computer’s **MAC address**.

If the target IP is in a **different subnet**, the data is sent to the **MAC address of the router (default gateway)** instead. The router then processes the Ethernet frame and forwards the data toward the destination.

The **Address Resolution Protocol (ARP)** in IPv4 is responsible for mapping an IP address to its corresponding MAC address so that proper delivery at the Data Link layer can occur.

---

# **Reserved MAC Address Ranges**

Just like IPv4 addresses have reserved ranges, MAC addresses also have reserved areas. The most important is the **local range**, which allows MAC addresses to be assigned manually instead of using a manufacturer-assigned one.

**Local Range Examples**  

`02:00:00:00:00:00`  
`06:00:00:00:00:00`  
`0A:00:00:00:00:00`  
`0E:00:00:00:00:00`

**Uses**  

Local MACs are not globally unique and can be used for customization. They are common in virtual machines, containers, test environments, and privacy features like Wi-Fi MAC randomization.

---

# The First Octet and Its Special Bits

A MAC address is 6 bytes (48 bits). The **first octet** (the first 8 bits) has two important bits at the end that define behavior:

**1. The last bit (LSB — Least Significant Bit of the first octet): Unicast vs. Multicast**

- If the last bit is **0** → the MAC address is a **Unicast**. Packets sent to this address go to exactly **one device**.
- If the last bit is **1** → the MAC address is a **Multicast**. Packets sent to this address can be received by **multiple devices** on the local network (but only those that are configured to listen).

Broadcast is a **special case of multicast**, where the address is all `FF:FF:FF:FF:FF:FF`. This means **every device** on the local network must accept the packet.

**2. The second-to-last bit: Global vs. Local**

- If this bit is **0** → the MAC address is **globally unique**, assigned by the manufacturer using an **OUI** (Organizationally Unique Identifier) from IEEE.
- If this bit is **1** → the MAC address is **locally administered**, meaning it was set manually by software or the operating system. Examples: VM adapters, MAC randomization on Wi-Fi, etc.

### Putting It Together

The first octet tells you **two things at once**:￼￼


- Is this a **unicast or multicast** address? (last bit)
- Is it **global or locally administered**? (second last bit)

So, with just those two bits, you can classify how the MAC behaves.


### Example Recap

- **Unicast (global OUI):** `DC:AD:BE:EF:13:37` → Only one device, manufacturer-assigned.
- **Unicast (locally administered):** `DE:AD:BE:EF:13:37` → Only one device, but manually set.
- **Multicast:** `01:00:5E:EF:13:37` → Delivered to multiple devices that opted in.
- **Broadcast:** `FF:FF:FF:FF:FF:FF` → Delivered to everyone on the network.

---

# MAC Addresses Attack Vectors

MAC addresses can be changed/manipulated or spoofed, and as such, they should not be relied upon as the sole means of security or identification. Network administrators should implement additional security measures, such as network segmentation and strong authentication protocols, to protect against potential attacks.

There exist several attack vectors that can potentially be exploited through the use of MAC addresses:

- `MAC spoofing`: This involves altering the MAC address of a device to match that of another device, typically to gain unauthorized access to a network.

- `MAC flooding`: This involves sending many packets with different MAC addresses to a network switch, causing it to reach its MAC address table capacity and effectively preventing it from functioning correctly.

- `MAC address filtering`: Some networks may be configured only to allow access to devices with specific MAC addresses that we could potentially exploit by attempting to gain access to the network using a spoofed MAC address.

---

# Address Resolution Protocol (ARP)

Address Resolution Protocol is a **network protocol**. It is used to resolve a **network layer (Layer 3) IP address** into a **link layer (Layer 2) MAC address**. This mapping allows devices on a LAN to communicate directly using their MAC addresses.

When a device on a LAN wants to communicate with another device, it broadcasts a message containing the destination IP address and its own MAC address. The device with the matching IP address responds with its MAC address, enabling communication. This process is known as **ARP resolution**.

ARP is important because it allows communication to happen using MAC addresses rather than IP addresses, which is more efficient.

**ARP Request**  
A device sends an ARP request when it wants to resolve the destination device’s IP address to a MAC address. The request is broadcast to all devices on the LAN. The device with the matching IP replies with its MAC address.

**ARP Reply**  
When a device receives an ARP request, it responds with an ARP reply containing both the IP and MAC addresses of the responding and requesting devices.

## Tshark Capture of ARP Requests

```shell-session
1   0.000000 10.129.12.100 -> 10.129.12.255 ARP 60  Who has 10.129.12.101?  Tell 10.129.12.100
2   0.000015 10.129.12.101 -> 10.129.12.100 ARP 60  10.129.12.101 is at AA:AA:AA:AA:AA:AA

3   0.000030 10.129.12.102 -> 10.129.12.255 ARP 60  Who has 10.129.12.103?  Tell 10.129.12.102
4   0.000045 10.129.12.103 -> 10.129.12.102 ARP 60  10.129.12.103 is at BB:BB:BB:BB:BB:BB
```

- Packet 1: Host **10.129.12.100** broadcasts an **ARP request** to `10.129.12.255` (broadcast) asking _“Who has 10.129.12.101? Tell me (10.129.12.100).”_
  
- Packet 2: Host **10.129.12.101** replies directly to **10.129.12.100**, saying _“10.129.12.101 is at MAC AA:AA:AA:AA:AA:AA.”_

- Packet 3: Host **10.129.12.102** broadcasts an **ARP request** asking _“Who has 10.129.12.103? Tell me (10.129.12.102).”_

- Packet 4: Host **10.129.12.103** replies directly to **10.129.12.102**, saying _“10.129.12.103 is at MAC BB:BB:BB:BB:BB:BB.”

