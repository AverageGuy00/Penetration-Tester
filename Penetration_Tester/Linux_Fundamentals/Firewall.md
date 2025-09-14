- Firewalls filter and monitor network traffic to protect resources.
- Linux firewalls are implemented via **Netfilter**, managed through tools like `iptables`, `nftables`, `ufw`, and `firewalld`. 
- Core concepts: **Tables, Chains, Rules, Matches, Targets**.

# Table & Chains

| Table  | Purpose                    | Built-in Chains                                 |
| ------ | -------------------------- | ----------------------------------------------- |
| filter | Traffic filtering          | INPUT, OUTPUT, FORWARD                          |
| nat    | Address translation        | PREROUTING, POSTROUTING                         |
| mangle | Packet header modification | PREROUTING, OUTPUT, INPUT, FORWARD, POSTROUTING |
| raw    | Special packet processing  | PREROUTING, OUTPUT                              |

# Common targets

|   Target   |             Description              |
| :--------: | :----------------------------------: |
|   ACCEPT   |         Allow packet through         |
|    DROP    |       Silently discard packet        |
|   REJECT   |       Block and notify sender        |
|    LOG     |          Log packet details          |
|    SNAT    |        Modify source IP (NAT)        |
|    DNAT    |     Modify destination IP (NAT)      |
| MASQUERADE | Dynamic source NAT (for dynamic IPs) |
|  REDIRECT  |     Redirect to another port/IP      |
|    MARK    | Set Netfilter mark value for routing |

# Common matches

|       Match        |                 Description                  |
| :----------------: | :------------------------------------------: |
|  -p / --protocol   |          Protocol (tcp, udp, icmp)           |
|      --dport       |               Destination port               |
|      --sport       |                 Source port                  |
|   -s / --source    |                  Source IP                   |
| -d / --destination |                Destination IP                |
|      -m state      | Connection state (NEW, ESTABLISHED, RELATED) |
|    -m multiport    |            Multiple ports/ranges             |
|  -m tcp / -m udp   |           TCP/UDP specific options           |
|     -m string      |            Match packet contents             |
|      -m limit      |              Rate limit packets              |
|    -m conntrack    |           Connection tracking info           |
|      -m mark       |          Match Netfilter mark value          |
|       -m mac       |           Match source MAC address           |
|     -m iprange     |                Match IP range                |

# Basic IP tables command

|                           Command                           |             Description              |
| :---------------------------------------------------------: | :----------------------------------: |
|                  `sudo iptables -L -v -n`                   | List rules with packet/byte counters |
|    `sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT`     |          Allow incoming SSH          |
|    `sudo iptables -A OUTPUT -p tcp --dport 80 -j ACCEPT`    |         Allow outgoing HTTP          |
|      `sudo iptables -A INPUT -s 192.168.1.100 -j DROP`      |          Block specific IP           |
|           `sudo iptables -D INPUT [rule-number]`            |        Delete rule by number         |
|   `sudo iptables -I INPUT 1 -p tcp --dport 443 -j ACCEPT`   |          Insert rule at top          |
|                     `sudo iptables -F`                      |      Flush all rules in a table      |
| `sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE` |   Enable NAT for outgoing traffic    |
|        `sudo iptables-save > /etc/iptables/rules.v4`        |       Save rules persistently        |
|      `sudo iptables-restore < /etc/iptables/rules.v4`       |         Restore saved rules          |

# Nftables Basic Commands

|                                    Command                                    |       Description        |
| :---------------------------------------------------------------------------: | :----------------------: |
|                            `sudo nft list ruleset`                            |   Show nftables rules    |
|                       `sudo nft add table inet filter`                        |       Create table       |
| `sudo nft add chain inet filter input { type filter hook input priority 0; }` |       Create chain       |
|           `sudo nft add rule inet filter input tcp dport 22 accept`           |        Allow SSH         |
|             `sudo nft delete rule inet filter input handle [num]`             |  Delete rule by handle   |
|                           `sudo nft flush ruleset`                            | Flush all nftables rules |

# UFW (Uncomplicated Firewall) Commands

|                       Command                       |      Description       |
| :-------------------------------------------------: | :--------------------: |
|                  `sudo ufw status`                  |    Show UFW status     |
|                  `sudo ufw enable`                  |       Enable UFW       |
|                 `sudo ufw disable`                  |      Disable UFW       |
|               `sudo ufw allow 22/tcp`               |       Allow SSH        |
|               `sudo ufw deny 23/tcp`                |      Block Telnet      |
| `sudo ufw allow from 192.168.1.0/24 to any port 80` | Allow HTTP from subnet |
|           `sudo ufw delete allow 22/tcp`            |      Remove rule       |
