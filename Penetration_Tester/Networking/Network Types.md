|          **Network Type**          |                **Definition**                |
| :--------------------------------: | :------------------------------------------: |
|      Wide Area Network (WAN)       |                   Internet                   |
|      Local Area Network (LAN)      |    Internal Networks (Ex: Home or Office)    |
| Wireless Local Area Network (WLAN) |   Internal Networks accessible over Wi-Fi    |
|   Virtual Private Network (VPN)    | Connects multiple network sites to one `LAN` |

# **WAN (Wide Area Network)**

A WAN is a network made up of multiple LANs connected together, with the Internet being the most common example. WAN addresses are usually public IPs that fall outside of the RFC 1918 private ranges. Large organizations may also build private WANs, sometimes called intranets or air-gapped networks. WANs typically use the Border Gateway Protocol (BGP) to handle routing between different networks.

---

# **LAN / WLAN**

A LAN (Local Area Network) is a network within a limited area, usually assigning private IP addresses from the RFC 1918 ranges (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16). A WLAN (Wireless LAN) is the same concept but transmits data without cables, making it more of a security distinction. While most LANs use private IPs, some environments like hotels or universities may assign public, routable IPs directly, though this is less common.

---

# VPN

A **Virtual Private Network (VPN)** makes your device behave as if it’s plugged into a different network — secure tunnel, different network feeling, same coffee.

## **Site-to-Site VPN**  

A tunnel between two network devices (usually routers or firewalls) that shares whole network ranges so multiple locations communicate as if local. Common for linking office sites over the Internet.

## **Remote-Access VPN**  

A user’s device creates a virtual interface (e.g., a TUN adapter) that places the device on a remote network. Check the routing table: if only specific networks are routed through the VPN (e.g., `10.10.10.0/24`), it’s a **split-tunnel** VPN; split-tunnel keeps general Internet traffic off the VPN (good for privacy, risky for corporate detection).

## **SSL VPN**  

A VPN delivered through a web browser that streams apps or full desktop sessions to the client (example: browser-based workstations like Pwnbox).

---

|             Network Type              |            Definition            |
| :-----------------------------------: | :------------------------------: |
|       Global Area Network (GAN)       |  Global network (the Internet)   |
|    Metropolitan Area Network (MAN)    | Regional network (multiple LANs) |
| Wireless Personal Area Network (WPAN) |   Personal network (Bluetooth)   |

# GAN (Global Area Network)

A GAN is a worldwide network connecting multiple WANs, with the Internet as the largest example. International companies may also operate private GANs, using undersea fiber cables or satellites to connect offices worldwide.

---

# MAN (Metropolitan Area Network)

A MAN connects several LANs within a city or region, often linking company branches via leased fiber lines. It provides high data throughput comparable to LAN speeds. MANs can integrate into WANs and GANs for broader connectivity.

---

# PAN (Personal Area Network)

A PAN is a short-range network connecting personal devices like smartphones, laptops, and tablets, usually via cables.

---
# **WPAN (Wireless Personal Area Network)**

A WPAN is a wireless PAN using technologies such as Bluetooth or Wireless USB. A Bluetooth-based WPAN is called a _Piconet_. WPANs are common in IoT for smart homes, using protocols like ZigBee, Z-Wave, or Insteon.

---

