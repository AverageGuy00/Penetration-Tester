
# IP configuration

|        Task        |                                 Windows                                  |                     Linux                     |
| :----------------: | :----------------------------------------------------------------------: | :-------------------------------------------: |
|   Show IP config   |                                `ipconfig`                                |            `ip addr` or `ifconfig`            |
|  Release DHCP IP   |                           `ipconfig /release`                            |              `sudo dhclient -r`               |
|   Renew DHCP IP    |                            `ipconfig /renew`                             |                `sudo dhclient`                |
|   Set static IP    | `netsh interface ip set address "Ethernet" static [IP] [Mask] [Gateway]` | Edit `/etc/network/interfaces` or use `nmcli` |
| Show routing table |                              `route print`                               |                  `ip route`                   |

# DNS configuration

|       Task       |                         Windows                         |               Linux                |
| :--------------: | :-----------------------------------------------------: | :--------------------------------: |
| Show DNS servers |              `nslookup` or `ipconfig /all`              |       `cat /etc/resolv.conf`       |
|     Set DNS      | `netsh interface ip set dns "Ethernet" static [DNS_IP]` | Edit `/etc/resolv.conf` or `nmcli` |

# Ping and Connectivity Testing

|      Task      |                            Command                             |         Description         |
| :------------: | :------------------------------------------------------------: | :-------------------------: |
|   Ping host    |                        `ping [host/IP]`                        | Test connectivity & latency |
|   Traceroute   | `tracert [host/IP]` (Windows) / `traceroute [host/IP]` (Linux) |     Trace route to host     |
| Test open port |        `telnet [host] [port]` / `nc -zv [host] [port]`         |    Check if port is open    |

# Network Services / Ports

|          Task           |         Windows          |             Linux              |
| :---------------------: | :----------------------: | :----------------------------: |
| List active connections |      `netstat -ano`      | `netstat -tulnp` or `ss -tuln` |
|  Show listening ports   |      `netstat -an`       |        `find "LISTEN"`         |
| Kill process using port | `taskkill /PID [pid] /F` |       `sudo kill [pid]`        |

# Wi-Fi / Wireless

|        Task         |                  Windows                  |                       Linux                        |
| :-----------------: | :---------------------------------------: | :------------------------------------------------: |
| Show Wi-Fi networks |        `netsh wlan show networks`         |              `nmcli device wifi list`              |
|  Connect to Wi-Fi   |    `netsh wlan connect name="[SSID]"`     | `nmcli device wifi connect [SSID] password [PASS]` |
|    Forget Wi-Fi     | `netsh wlan delete profile name="[SSID]"` |          `nmcli connection delete [SSID]`          |

# Misc / Troubleshooting

|        Task         |                               Windows                                |                                  Linux                                  |
| :-----------------: | :------------------------------------------------------------------: | :---------------------------------------------------------------------: |
|      Flush DNS      |                         `ipconfig /flushdns`                         | `sudo systemd-resolve --flush-caches` or `sudo resolvectl flush-caches` |
|   Restart network   | `netsh interface set interface "Ethernet" admin=disable` then enable |                 `sudo systemctl restart NetworkManager`                 |
| Display MAC address |                               `getmac`                               |                             `ip link show`                              |
