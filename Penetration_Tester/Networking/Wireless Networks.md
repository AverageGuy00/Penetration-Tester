
Wireless networks allow devices to communicate without cables using **radio frequency (RF) technology**. Each device has a **wireless adapter** that converts data to RF signals and back.

Types of wireless networks:

- **WiFi (LAN)**: Small area coverage, e.g., home or office
- **WWAN (cellular)**: Large area coverage, e.g., cities or regions

Devices must be **within range** and configured with the correct settings to connect. Communication occurs over **2.4 GHz or 5 GHz bands**, managed by a **Wireless Access Point (WAP)** that controls access and connects to wired networks.

**Signal strength and range** are affected by obstacles, interference, and transmitter power. Techniques like **spread spectrum transmission** and **error correction** improve reliability.

---
# WiFi Connection

To connect to a WiFi network, a device must have the correct **SSID (network name)** and **password**. It uses the **IEEE 802.11 protocol** to communicate with the **Wireless Access Point (WAP)**.

The device sends a **connection request (association request)** containing key information:

- **MAC address**: Device identifier
- **SSID**: Network name
- **Supported data rates**: Communication speeds
- **Supported channels**: Frequencies the device can use
- **Supported security protocols**: e.g., WPA2/WPA3

Once connected, the device can communicate with the WAP, other devices, and access the Internet. The **SSID can be hidden**, but it can still be discovered in authentication packets.

WiFi networks also use protocols like **TCP/IP, DHCP, and WPA2** for addressing, traffic routing, and security.

---

# WEP Challenge-Response Handshake

The **challenge-response handshake** authenticates a client device to a **WAP** in a WEP-secured wireless network:

|Step|Who|Description|
|---|---|---|
|1|Client|Sends an **association request** to the WAP|
|2|WAP|Responds with an **association response** containing a **challenge string**|
|3|Client|Calculates a **response** using the challenge and shared secret key, sends it back|
|4|WAP|Verifies the response using the shared secret and sends **authentication response**|

**CRC (Cyclic Redundancy Check)** is used to detect packet errors. Each packet includes a CRC value calculated from its data. The receiver recalculates CRC to verify integrity.

**WEP flaw:** CRC is based on **plaintext data**, not encrypted data. This allows an attacker to **decrypt a single packet** without knowing the WEP key by analyzing the CRC value.

---

# Security Features

WiFi networks have several security features to protect against unauthorized access and ensure the privacy and integrity of data transmitted over the network. Some of the leading security features include but are not limited to:

- Encryption
- Access Control
- Firewall

#### Encryption

We can use various encryption algorithms to protect the confidentiality of data transmitted over wireless networks. The most common encryption algorithms in WiFi networks are [Wired Equivalent Privacy](https://en.wikipedia.org/wiki/Wired_Equivalent_Privacy) (`WEP`), [WiFi Protected Access 2](https://en.wikipedia.org/wiki/Wi-Fi_Protected_Access#WPA2) (`WPA2`), and [WiFi Protected Access 3](https://en.wikipedia.org/wiki/Wi-Fi_Protected_Access#WPA3) (`WPA3`).

#### Access Control

WiFi networks are configured by default to allow authorized devices to join the network using specific authentication methods. However, these methods can be changed by requiring a password or a unique identifier (such as a MAC address) to identify authorized devices.

#### Firewall

A firewall is a security system that controls incoming and outgoing network traffic based on predetermined security rules. For example, WiFi routers often have built-in firewalls that can block incoming traffic from the Internet and protect against various types of cyber threats.

---

# Encryption

