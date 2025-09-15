A proxy is a server that acts as an intermediary between a client and another server, forwarding requests and responses between them.

# Dedicated Proxy / Forward Proxy

A forward proxy sits between a client and the wider internet. It receives requests from clients, evaluates them, and forwards them to the destination server. It can provide anonymity, enforce security policies, or control access. The server only sees the proxy’s IP address, not the client’s real one.
Examples:
		- Burpsuite
		- Tor

# Reverse Proxy

A reverse proxy sits in front of one or more backend servers and handles requests from clients on their behalf. It can balance loads across multiple servers, cache responses, terminate SSL/TLS, and hide the identity or structure of backend systems. Clients only interact with the reverse proxy, never directly with the servers.
Examples:
		- Cloudflare
		- NGINX

# **Transparent Proxy**  

A transparent proxy intercepts traffic without requiring any client-side configuration. Clients are often unaware of its presence. It is commonly used by organizations and ISPs to filter traffic, monitor activity, or enforce access policies. Unlike a forward proxy, it does not explicitly mask the client’s identity, but it still sits in the middle of the communication path.
Examples:
		-Blue Coat ProxySG
		-WCCP - Enabled Cisco

# **Non-Transparent Proxy**  

A non-transparent proxy requires explicit client configuration (for example, setting the proxy server’s IP/port in a browser or system). The client knows it is using a proxy, and the proxy can modify requests, mask client IP addresses, and enforce access rules. Unlike transparent proxies, traffic is not intercepted automatically — the proxy is deliberately chosen and used by the client.
Examples:
		-Privoxy
		-Shadowsocks