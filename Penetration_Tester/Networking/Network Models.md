# OSI MODEL

The **OSI (Open Systems Interconnection) model** is a 7-layer reference model published by **ISO** and **ITU** to describe how systems communicate over a network. Each layer has distinct tasks, from the **physical transmission of bits** up to **user-facing applications**, ensuring interoperability and standardization between devices and protocols.

|   Layer (No.)    |               Primary function                |            Protocols / Examples             |                             Pentest / Security angle                             |
| :--------------: | :-------------------------------------------: | :-----------------------------------------: | :------------------------------------------------------------------------------: |
| Application (7)  |     User-facing services; apps talk here      |      HTTP, HTTPS, FTP, DNS, SMTP, SMB       |        Web app attacks (XSS, SQLi), eavesdropping, abuse of app protocols        |
| Presentation (6) |    Data encoding, encryption, compression     |         TLS/SSL, JPEG, ASN.1, MIME          |        TLS interception, cert pinning bypass, malformed-parsing exploits         |
|   Session (5)    | Session management, auth, connection control  | RPC sessions, NetBIOS sessions, SIP dialogs |          Session hijack, replay attacks, weak session re-establishment           |
|  Transport (4)   |    End-to-end delivery, ports, reliability    |  TCP, UDP, SCTP; ports (80, 443, 3389...)   |       Port scanning, TCP/UDP fuzzing, session exhaustion (DoS), TCP hijack       |
|   Network (3)    |         Logical addressing & routing          |   IP, ICMP, routing protocols (OSPF, BGP)   | IP spoofing, routing attacks, ICMP tunneling, subnet misconfig (like /25 gotcha) |
|  Data Link (2)   | MAC addressing, LAN switching, frame delivery |          Ethernet, ARP, VLANs, STP          |            ARP spoofing, VLAN hopping, MAC flooding, switch misconfig            |
|   Physical (1)   |          Physical signals and media           | Cables, fiber, radio (Wi-Fi), switches/PHY  |            Cable tapping, jamming, rogue APs, physical access attacks            |

---
# The **TCP/IP 

(Transmission Control Protocol/Internet Protocol) mode is the foundation of the Internet and represents an entire **protocol family** for data transport and switching across networks. While named after TCP and IP, it also includes protocols like **ICMP**, **UDP**, **SMTP**, **HTTP**, and many others.

The TCP/IP stack ensures data is packaged, addressed, transmitted, routed, and received correctly across private and public networks, forming the practical basis for all Internet communication.

|     TCP/IP Layer      |                   Purpose                    |             Protocol Examples              |               Maps to OSI Layers               |
| :-------------------: | :------------------------------------------: | :----------------------------------------: | :--------------------------------------------: |
|      Application      |     User-facing services, app protocols      |      HTTP, HTTPS, FTP, DNS, SMTP, SSH      | Application (7), Presentation (6), Session (5) |
|       Transport       |   End-to-end delivery, reliability, ports    |               TCP, UDP, SCTP               |                 Transport (4)                  |
|  Internet (Network)   |         Logical addressing, routing          |       IP, ICMP, ARP, RIP, OSPF, BGP        |                  Network (3)                   |
| Network Access (Link) | Physical addressing, data link, media access | Ethernet, Wi-Fi (802.11), PPP, Frame Relay |          Data Link (2), Physical (1)           |

### Important tasks of TCP/IP

|        **ask**         | **Protocol** |                                                                                                                                                            **Description**                                                                                                                                                             |
|:----------------------:|:------------:|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
|  `Logical Addressing`  |     `IP`     | Due to many hosts in different networks, there is a need to structure the network topology and logical addressing. Within TCP/IP, IP takes over the logical addressing of networks and nodes. Data packets only reach the network where they are supposed to be. The methods to do so are `network classes`, `subnetting`, and `CIDR`. |
|       `Routing`        |     `IP`     |                                                              For each data packet, the next node is determined in each node on the way from the sender to the receiver. This way, a data packet is routed to its receiver, even if its location is unknown to the sender.                                                              |
| `Error & Control Flow` |    `TCP`     |                                                                      The sender and receiver are frequently in touch with each other via a virtual connection. Therefore control messages are sent continuously to check if the connection is still established.                                                                       |
| `Application Support`  |    `TCP`     |                                                                                                           TCP and UDP ports form a software abstraction to distinguish specific applications and their communication links.                                                                                                            |
|   `Name Resolution`    |    `DNS`     |                                                                                DNS provides name resolution through Fully Qualified Domain Names (FQDN) in IP addresses, enabling us to reach the desired host with the specified name on the internet.                                                                                |


---

# Packet Transfers

Packet transfer is the process of moving data across a network using a layered model (OSI or TCP/IP). Data from an application is encapsulated into **Protocol Data Units (PDUs)** at each layer, with each layer adding its own headers (and sometimes trailers). These headers provide addressing, error control, and delivery instructions. The data then flows down to the **physical layer**, where it is transmitted as bits. On the receiving side, the process is reversed (decapsulation), stripping off headers at each layer until the original application data is reconstructed and delivered to the destination program.

| Layer (OSI) | PDU Name |               Example                |
| :---------: | :------: | :----------------------------------: |
| Application |   Data   |     HTTP request, email contents     |
|  Transport  | Segment  |    TCP segment with port numbers     |
|   Network   |  Packet  | IP packet with source/destination IP |
|  Data Link  |  Frame   |  Ethernet frame with MAC addresses   |
|  Physical   |   Bits   | 1s and 0s on the wire/fiber/wireless |

- <u>Encapsulation (Sender side)</u>: When sending data (e.g., a web request), the application generates **data**, the transport layer wraps it in a **segment**, the network layer encapsulates it into a **packet**, the data link layer frames it, and the physical layer sends it as **bits**.

- <u>Decapsulation (Receiver side)</u>: The reverse happens. Bits are received, rebuilt into a frame, stripped down into a packet, then a segment, and finally application data — until the web browser (or other app) can use it.

Learn TCP/IP to know _what_ is happening, and use OSI to understand _why_ it happens and _where_ to poke
.Together they make traffic analysis and protocol exploitation much easier to reason about.


---

# Network Layer

Layer 3 (Network Layer) in OSI

This layer is all about making sure data actually knows _where to go_ in a network. Data doesn’t magically teleport to the destination device — it has to hop from router to router until it gets there. Layer 3 handles that journey.

**Core Functions** 

- **Logical Addressing** → Assigns each device a unique address (like IPv4 or IPv6). These addresses let routers know where to send the data.

- **Routing** → Figures out the “best path” for packets to travel across different networks. Routers use routing tables and protocols to decide the next hop.

### Key Protocols at Layer 3

- **IPv4 / IPv6** → Core addressing systems.
- **ICMP** → Error messaging & diagnostics (like `ping`).
- **IGMP** → Multicast group management.
- **RIP / OSPF** → Routing protocols that help routers share network paths.
- **IPsec** → Adds security at the network layer (encryption, authentication).

### How it Works in Practice

When you send data (say, a request to a website):

1. The data packet gets a **source IP** (you) and a **destination IP** (the server).
2. Routers in between look at the **destination IP** and check their routing table.
3. The packet is forwarded hop by hop until it reaches the final network.
4. At each hop, the router only cares about the **Layer 3 info** (IP address). It doesn’t process higher-layer data like the actual web page content.

Think of Layer 3 like a **postal system**: it doesn’t care what’s inside the envelope (that’s handled by higher layers). It just reads the address on the outside and decides the best route to deliver it, even if that means passing through several post offices (routers).