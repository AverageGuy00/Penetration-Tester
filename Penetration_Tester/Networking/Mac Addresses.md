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

The first octet tells you **two things at once**:

- Is this a **unicast or multicast** address? (last bit)
- Is it **global or locally administered**? (second last bit)

So, with just those two bits, you can classify how the MAC behaves.


### Example Recap

- **Unicast (global OUI):** `DC:AD:BE:EF:13:37` → Only one device, manufacturer-assigned.
- **Unicast (locally administered):** `DE:AD:BE:EF:13:37` → Only one device, but manually set.
- **Multicast:** `01:00:5E:EF:13:37` → Delivered to multiple devices that opted in.
- **Broadcast:** `FF:FF:FF:FF:FF:FF` → Delivered to everyone on the network.