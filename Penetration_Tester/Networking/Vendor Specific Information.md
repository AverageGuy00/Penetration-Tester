[Cisco IOS](https://www.cisco.com/c/en/us/products/ios-nx-os-software/ios-technologies/index.html) is the operating system of Cisco network devices such as routers and switches. It provides features and services required to manage and operate network devices. This operating system comes in different versions and releases that vary in features, support, and performance. It offers several features required for the operation of modern networks, such as, but not limited to:

- Support for IPv6
- Quality of Service (QoS)
- Security features such as encryption and authentication
- Virtualization features such as Virtual Private LAN Service (VPLS)
- Virtual Routing and Forwarding (VRF)

Cisco IOS can be managed in several ways, depending on the network device and hardware used. The most commonly used method is the command line interface (`CLI`), which can also be managed in the graphical user interface (`GUI`). In addition, it supports various network protocols and services required for network operations. These include:

|   **Protocol Type**   |                                                                                                                 **Description**                                                                                                                  |
| :-------------------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|  `Routing protocols`  |                               Such as [OSPF](https://en.wikipedia.org/wiki/Open_Shortest_Path_First) and [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) are used to route data packets on a network.                               |
| `Switching protocols` | Such as [VLAN Trunking Protocol](https://en.wikipedia.org/wiki/VLAN_Trunking_Protocol) (`VTP`) and [Spanning Tree Protocol](https://en.wikipedia.org/wiki/Spanning_Tree_Protocol) (`STP`) is used to configure and manage switches on a network. |
|  `Network services`   |      Such as [Dynamic Host Configuration Protocol](https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol) (`DHCP`) are used to automatically provide clients on the network with IP addresses and other network configurations.      |
|  `Security features`  |                                 Such as [Access Control Lists](https://en.wikipedia.org/wiki/Access-control_list) (`ACLs`), which are used to control access to network resources and prevent security threats.                                  |

In Cisco IOS, different types of passwords are used for various purposes, for example:

| **Password Type** |                                                                                                                            **Description**                                                                                                                            |
| :---------------: | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|      `User`       |               The [user](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/security/s1/sec-s1-cr-book/sec-cr-t2.html#wp2992613898) password is used for logging in to Cisco IOS. It is used to restrict access to the network device and its features.                |
| `Enable Password` |        The [enable password](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/security/d1/sec-d1-cr-book/sec-cr-e1.html#wp3884449514) is used to enter "enable" mode. The "enable" mode is the mode where you have access to advanced functions and settings.        |
|     `Secret`      | The [secret](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/security/s1/sec-s1-cr-book/sec-cr-s1.html#wp2622423174) is a password to secure access to certain functions and services. It is often used to restrict access to remote management tools and services. |
|  `Enable Secret`  |   The [enable secret](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/security/d1/sec-d1-cr-book/sec-cr-e1.html#wp3438133060) is an extra-secure password used to secure access to "enable" mode, and they are stored encrypted to provide additional protection.   |

---

# VLANs

A **VLAN (Virtual Local Area Network)** is a logical segmentation of a switch into multiple broadcast domains. It allows grouping endpoints by department, role, or function without depending on their physical location. Each VLAN must have its own subnet.

|**Department**|**VLAN ID**|**Subnet**|
|:-:|:-:|:-:|
|`Servers`|`VLAN 10`|`192.168.1.0/24`|
|`C-Level`|`VLAN 20`|`192.168.2.0/24`|
|`Finance`|`VLAN 30`|`192.168.3.0/24`|
|`HR`|`VLAN 40`|`192.168.4.0/24`|
|`Marketing`|`VLAN 50`|`192.168.5.0/24`|
|`Support`|`VLAN 60`|`192.168.6.0/24`|
A myriad of benefits is attained when using `VLANs`, including:

- `Better Organization`: Network administrators can group endpoints based on any common attribute they share.
- `Increased Security`: Network segmentation disallows unauthorized members from sniffing network packets in other `VLANs`.
- `Simplified Administration`: Network administrators do not have to worry about the physical locations of an endpoint.
- `Increased Performance`: With reduced broadcast traffic for all endpoints, more bandwidth is made available for use by the network.

**Access vs Trunk**  

On a VLAN-enabled switch, every port is either **access** or **trunk**.

- **Access ports** carry traffic for a single VLAN (sometimes two, if one is reserved for voice). Any frame entering an access port is assumed to belong to that VLAN.

- **Trunk ports** carry traffic for multiple VLANs simultaneously. They link switches (or a switch and a router), making sure VLAN information is preserved as frames move between devices.

## VLAN Identification

Normal 802.3 Ethernet frames don’t carry VLAN data. To track VLAN membership across devices, switches use **trunking protocols** that add VLAN information into frames.

## **ISL (Inter-Switch Link)**  

Cisco-proprietary trunking method (older, now deprecated). ISL encapsulated the entire Ethernet frame, including its original header, and added:

- **26-byte header**
- **4-byte trailer** 

## **IEEE 802.1Q**  

The industry standard trunking protocol (introduced in 1998). Instead of encapsulation, it modifies the Ethernet frame by inserting a **4-byte header** between the source MAC and the length/type field.

The new fields are:

- **TPID** (Tag Protocol Identifier, always `0x8100`) → marks the frame as VLAN-tagged
- **TCI** (Tag Control Information, 16 bits), split into:
  - **PCP** → Priority Code Point 
  - **DEI** → Drop Eligible Indicator
  - **VID** → VLAN ID (12 bits, allows 4094 usable VLANs; IDs 0 and 4095 reserved)

This way, VLANs can be identified and preserved across multiple devices and links.

---

# VLAN-Capable NICs

Some network interface cards (NICs) support **VLAN tagging**, allowing assignment of VLAN IDs directly to NICs. In Linux, this is done by creating a VLAN interface on top of a **parent interface**. The VLAN interface tags outgoing packets with the VLAN ID, while incoming packets are untagged.

### Assigning NICs a VLAN in Linux

```bash
# Load the 802.1Q kernel module
sudo modprobe 8021q

# Verify the module is loaded
lsmod | grep 8021

# Identify the parent interface (e.g., eth0)
ip a

# Create a VLAN interface on the parent
Deprecated: sudo vconfig add eth0 20
Preferred: sudo ip link add link eth0 name eth0.20 type vlan id 20

# Assign an IP address to the VLAN interface
sudo ip addr add 192.168.1.1/24 dev eth0.20

# Bring the VLAN interface up
sudo ip link set up eth0.20

# Verify the VLAN interface is active
ip a | grep eth0.20
```

### Windows VLAN Assignment

```pgsql
# Open Device Manager (devmgmt.msc)

# Expand "Network adapters" and locate your NIC

# Right-click the NIC → Properties

# Go to the Advanced tab

# Find "VLAN ID" or "Priority & VLAN" (depends on vendor driver)

# Set the VLAN ID (e.g., 20) and click OK

# Verify VLAN settings
Command Prompt: ipconfig /all
PowerShell: Get-NetAdapterAdvancedProperty -Name "Ethernet" | Where-Object DisplayName -Match "VLAN"
```
