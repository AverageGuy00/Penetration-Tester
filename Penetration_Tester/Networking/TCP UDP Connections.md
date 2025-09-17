TCP and UDP are protocols for transmitting data over the Internet. TCP is connection-oriented and reliable, ensuring all data arrives correctly—commonly used for web pages and emails. UDP is connectionless and faster but less reliable, making it ideal for streaming and gaming where speed matters more than accuracy.

# IP Packet

An IP packet is the basic unit of data at the network layer. It has a header (like an envelope with addressing and routing info) and a payload (the actual data being delivered).

The header of an IP packet contains several fields that have important information.

|        **Field**         |                                **Description**                                 |
| :----------------------: | :----------------------------------------------------------------------------: |
|        `Version`         |            Indicates which version of the IP protocol is being used            |
| `Internet Header Length` |                Indicates the size of the header in 32-bit words                |
|    `Class of Service`    |              Means how important the transmission of the data is               |
|      `Total length`      |               Specifies the total length of the packet in bytes                |
|  `Identification (ID)`   | Is used to identify fragments of the packet when fragmented into smaller parts |
|         `Flags`          |                         Used to indicate fragmentation                         |
|    `Fragment Offset`     |          Indicates where the current fragment is placed in the packet          |
|      `Time to Live`      |            Specifies how long the packet may remain on the network             |
|        `Protocol`        |   Specifies which protocol is used to transmit the data, such as TCP or UDP    |
|        `Checksum`        |                     Is used to detect errors in the header                     |
|   `Source/Destination`   |     Indicate where the packet was sent from and where it is being sent to      |
|        `Options`         |                    Contain optional information for routing                    |
|        `Padding`         |                     Pads the packet to a full word length                      |

---

# Blind Spoofing

**Blind spoofing** is an attack where an adversary forges packet headers (spoofed source/destination IPs, ports, and an artificially chosen Initial Sequence Number) and sends packets without seeing the target’s responses. Because the attacker cannot observe replies, they guess sequence/ack values to inject data or disrupt connections. Consequences include connection hijack attempts, integrity disruption, or traffic interception when combined with other weaknesses. This requires careful timing and good guesses — and is illegal on networks you don’t own or test with permission.