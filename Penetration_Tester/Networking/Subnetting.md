# Subnetting /24 vs /25

**IP Address Structure**

- An IP address has 4 octets (e.g., `10.20.0.1`), each 8 bits.
- A subnet mask defines which part is the **network** and which part is the **host**.

**/24 Subnet**

- Subnet mask: `255.255.255.0`
- First 24 bits = network, last 8 bits = host
- Network example: `10.20.0.0/24`
- Hosts: `10.20.0.1 – 10.20.0.254`
- All hosts can communicate directly without a router

**/25 Subnet**

- Subnet mask: `255.255.255.128`
- Splits a `/24` network in **two halves**:
    - First half: `10.20.0.0 – 10.20.0.127`
    - Second half: `10.20.0.128 – 10.20.0.255`
- Hosts can only communicate directly **within the same half**

**Example Network**

- Server Gateway: `10.20.0.1/25` (first half)
- Domain Controller: `10.20.0.10/25` (first half)
- Client Gateway: `10.20.0.129/25` (second half)
- Client Workstation: `10.20.0.200/25` (second half)
- Pentester IP: `10.20.0.252/24` (assumed full /24)

**Observation**

- Pentester could access client machines in second half because they assumed `/24`.
- Pentester could **not reach Domain Controller** in first half without routing.
- Misunderstanding the subnet can make hosts appear “offline”

**Key Takeaways**

- Subnet mask determines which addresses are reachable directly.
- /25 halves a /24 network, restricting direct communication.  
- Traffic between different halves **must go through a router/gateway**.

---

# Gateway

A **gateway (router)** is the device that connects different networks (subnets).

- If your computer thinks an IP is **outside its own subnet**, it sends the traffic to the gateway.
- The gateway then forwards it to the correct subnet.

Example:

- Your IP: `10.20.0.50/25` (first half)
- Target IP: `10.20.0.200` (second half)
- Without routing, your PC won’t reach it.
- With routing: your PC sends traffic to its gateway (`10.20.0.1`), which knows how to reach the second half (`10.20.0.128/25`)

---

**Extra Points – Network Segmentation**

**Web Server (DMZ)**  
Isolate in a DMZ since it’s internet-facing and more likely to be compromised.

**Workstations**  
Should be on their own network, ideally with host firewalls to block workstation-to-workstation traffic.

**Switches & Routers**  
Use a dedicated admin network to stop snooping or OSPF abuse.

**IP Phones**  
Separate network for security (stop eavesdropping) and performance (low latency).

**Printers**  
Own network, since they’re insecure, can leak NTLMv2 credentials, and often hold sensitive data.

---

