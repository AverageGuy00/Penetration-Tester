
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

# Encryption Protocols


Wired Equivalent Privacy (WEP) vs. WiFi Protected Access (WPA)

WEP and WPA are encryption protocols that secure data transmitted over WiFi networks. WPA can use stronger encryption algorithms, such as Advanced Encryption Standard (AES).

## WEP (Wired Equivalent Privacy)

WEP uses either a 40-bit or 104-bit key, while WPA with AES uses a 128-bit key. Longer keys provide stronger encryption and better resistance against attacks. WEP is considered insecure today because it is vulnerable to multiple attacks that can allow attackers to decrypt network traffic. It also uses the RC4 cipher, which has known weaknesses, and is incompatible with most modern devices.

WEP uses a **shared key** for both encryption and authentication. There are two main versions:

- **WEP-40/WEP-64**: Uses a 40-bit secret key  
- **WEP-104**: Uses an 80-bit secret key 

Both versions use a 24-bit **Initialization Vector (IV)** included in each packet. The IV combines with the secret key to encrypt data and ensure key uniqueness. The table below summarizes this:

|Protocol|IV|Secret Key|
|---|---|---|
|WEP-40/WEP-64|24-bit|40-bit|
|WEP-104|24-bit|80-bit|
 The small size of the IV makes it susceptible to brute-force attacks. An attacker can try every possible IV combination to decrypt packets and gain access to transmitted data, compromising network security.

## WPA (WiFi Protected Access)

**WPA (Wi-Fi Protected Access, 2003)**

- Replaced WEP.
- Uses **TKIP** (Temporal Key Integrity Protocol) for encryption.
- Vulnerable to certain attacks; now mostly obsolete.

**WPA2 (2004–present)**

- Replaced WPA. 
- Uses **AES-CCMP** encryption, which is much stronger than TKIP.
- Supports **Personal** (pre-shared key, PSK) and **Enterprise** (RADIUS authentication) modes.
- Vulnerable to **KRACK attack** (key reinstallation), but otherwise secure.

**WPA3 (2018–present)**

- Replaces WPA2, designed to fix its weaknesses.
- Uses **SAE (Simultaneous Authentication of Equals)** instead of PSK to prevent offline dictionary attacks.
- Mandatory **AES encryption**; improves encryption on open networks via **Opportunistic Wireless Encryption (OWE)**.
- Better forward secrecy: even if one session key is leaked, previous sessions remain secure.
- Offers **Enterprise 192-bit mode** for high-security environments.

|       Feature       |     WPA2     |                 WPA3                 |
| :-----------------: | :----------: | :----------------------------------: |
|     Encryption      |   AES-CCMP   |               AES-CCMP               |
|    Key Exchange     | PSK / 802.1X |          SAE (more secure)           |
|    Open Networks    | Unencrypted  | Opportunistic Wi-Fi Encryption (OWE) |
|   Forward Secrecy   |      No      |                 Yes                  |
| Enterprise Security |   128-bit    |               192-bit                |

---

# **Authentication Protocols: LEAP vs PEAP**

LEAP and PEAP are **authentication protocols** used to secure wireless networks, often in combination with WEP or WPA, providing an extra layer of security. Both are based on **Extensible Authentication Protocol (EAP)**, a framework for network authentication.

**LEAP**

- Uses a **shared key** for both encryption and authentication.
- Vulnerable if the key is compromised, allowing relatively easy network access. 

**PEAP**

- Uses **tunneled Transport Layer Security (TLS)** for authentication.
- Establishes a secure connection between the device and access point using **digital certificates**.
- Encrypts the authentication process, providing stronger protection against unauthorized access and attacks.
 
---

# **TACACS+ in Wireless Networks**

TACACS+ is a protocol used to **authenticate and authorize users** accessing network devices such as routers and switches.

When a **wireless access point (WAP)** sends an authentication request to a TACACS+ server, the **entire request packet is encrypted**. This packet includes user credentials and session details.

Encryption ensures:

- **Confidentiality** – sensitive credentials aren’t exposed if intercepted.
- **Integrity** – prevents tampering or replacing the request with a malicious one.

Encryption methods like **SSL/TLS** or **IPSec** may be used, depending on the TACACS+ server configuration and the WAP’s capabilities.

---

# Disassociation attack

A **Disassociation attack** forces a client to drop its connection to a **WAP** by sending forged 802.11 **disassociation** (or deauthentication) management frames. Attackers exploit historically unauthenticated management frames to disrupt connectivity or force reconnections for follow-on attacks.

**Why it matters**

- **Availability impact:** causes service disruption and user inconvenience.
- **Attack staging:** often used as a precursor to MITM (rogue AP) or credential-capture attacks. 
- **Operational noise:** generates spikes in management-frame traffic that can affect monitoring and performance.

**How it works (high-level, non-actionable)**

- Attacker crafts spoofed disassociation/deauth frames that appear to originate from the AP or client.
- Target client receives the frame, drops the association, and must reauthenticate to reconnect.
- Because management frames were historically unauthenticated, spoofing was trivial on many networks.

---

# # Wireless Hardening

Wireless networks can be hardened using multiple defensive measures. These measures help prevent unauthorized access, protect sensitive data, and strengthen authentication mechanisms.

### **Disabling broadcasting**  

Disabling the broadcasting of the **SSID** makes the network less visible to outsiders. Normally, the SSID is included in **beacon frames** transmitted by the WAP. When broadcasting is disabled, the SSID is hidden and only known clients can connect. While not a strong security control on its own, it reduces casual discovery.

### **WPA (Wi-Fi Protected Access)**  

WPA provides **encryption and authentication** to secure wireless communication. Two main deployment modes exist:

- **WPA-Personal**: Uses a pre-shared key (PSK), designed for home or small business use.
- **WPA-Enterprise**: Uses a centralized authentication server (such as **RADIUS** or **TACACS+**) to verify client identity. Better suited for large organizations.

WPA protects against **unauthorized access** and **data interception**. WPA2 and WPA3 are modern, stronger implementations.

### **MAC filtering**  

MAC filtering restricts access by allowing only specific **MAC addresses** to connect. The WAP maintains a list of approved devices and denies all others. While this helps against casual intrusion, it is weak against attackers who can **spoof MAC addresses**.

### **Deploying EAP-TLS** 

**EAP-TLS** provides strong wireless authentication using **digital certificates** and **PKI**. Each client presents a certificate for validation, establishing an encrypted session with the WAP. This method ensures robust authentication and encryption, significantly raising the security of enterprise wireless networks.

###  IPsec

**IPsec (Internet Protocol Security)** is a suite of protocols that secures communication at the network layer by encrypting and authenticating IP packets. It ensures confidentiality, integrity, and authenticity of data traveling across untrusted networks like the internet.

IPsec uses a combination of two protocols to provide encryption and authentication:

1. [Authentication Header](https://www.ibm.com/docs/en/i/7.1?topic=protocols-authentication-header) (`AH`): This protocol provides integrity and authenticity for IP packets but does not provide encryption. It adds an authentication header to each IP packet, which contains a cryptographic checksum that can be used to verify that the packet has not been tampered with.

2. [Encapsulating Security Payload](https://www.ibm.com/docs/en/i/7.4?topic=protocols-encapsulating-security-payload) (`ESP`): This protocol provides encryption and optional authentication for IP packets. It encrypts the data payload of each IP packet and optionally adds an authentication header, similar to AH.

IPsec can be used in two modes.

|     **Mode**     |                                                                                          **Description**                                                                                           |
| :--------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| `Transport Mode` | In this mode, IPsec encrypts and authenticates the data payload of each IP packet but does not encrypt the IP header. This is typically used to secure end-to-end communication between two hosts. |
|  `Tunnel Mode`   |                With this mode, IPsec encrypts and authenticates the entire IP packet, including the IP header. This is typically used to create a VPN tunnel between two networks.                 |

For example, an administrator could place a firewall in between. In order to facilitate IPsec VPN traffic from a VPN client outside a firewall to a VPN server inside, the firewall would need to allow the following protocols:

|               **Protocol**               |  **Port**   |                                                                                                                                             **Description**                                                                                                                                              |
| :--------------------------------------: | :---------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|        `Internet Protocol` (`IP`)        | `UDP/50-51` |                                                                 This is the primary protocol that provides the foundation for all internet communication. It is used to route packets of data between the VPN client and the VPN server.                                                                 |
|     `Internet Key Exchange` (`IKE`)      |  `UDP/500`  | IKE is a protocol that is used to establish and maintain secure communication between the VPN client and the VPN server. It is based on the Diffie-Hellman key exchange algorithm, and it is used to negotiate and establish shared secret keys that can be used to encrypt and decrypt the VPN traffic. |
| `Encapsulating Security Payload` (`ESP`) | `UDP/4500`  |                                           ESP is also a protocol that provides encryption and authentication for IP datagrams. It is used to encrypt the VPN traffic between the VPN client and the VPN server, using the keys that were negotiated with IKE.                                            |

These protocols are necessary for facilitating IPsec VPN traffic because they provide the security and encryption that are required for secure communication over the public internet. Without these protocols, the VPN traffic would be vulnerable to interception and tampering.