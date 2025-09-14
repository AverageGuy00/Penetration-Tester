
# Kernel logs

- File: `/var/log/kern.log`
- Contains: Kernel events, drivers, hardware issues.
- Commands:
 ```bash
  # View kernel logs
sudo cat /var/log/kern.log

# Tail live updates
sudo tail -f /var/log/kern.log

# Search for specific errors
sudo grep "error" /var/log/kern.log
  ```
# System logs

- File: `/var/log/syslog`
- Contains: Service start/stop, login attempts, system events. 
- Commands:
```bash
# View syslog
sudo less /var/log/syslog

# Follow log live
sudo tail -f /var/log/syslog

# Search for reboots
sudo grep "reboot" /var/log/syslog
```
# Authentication logs

- File: `/var/log/auth.log`
- Contains: SSH logins, sudo usage, PAM authentication.
- Commands:
```bash
# Check failed SSH attempts
sudo grep "Failed password" /var/log/auth.log

# Check successful SSH logins
sudo grep "Accepted" /var/log/auth.log

# Monitor auth logs live
sudo tail -f /var/log/auth.log
```
# Application logs

```swift
Apache: /var/log/apache2/access.log /var/log/apache2/error.log
Nginx:  /var/log/nginx/access.log /var/log/nginx/error.log
MySQL:  /var/log/mysql/error.log
PostgreSQL: /var/log/postgresql/postgresql-version-main.log
```

# Security logs

```lua
Fail2ban: /var/log/fail2ban.log
UFW: /var/log/ufw.log
General: /var/log/syslog /var/log/auth.log
```

- Live monitoring & analysis
```perl
# Follow multiple logs
sudo tail -f /var/log/syslog /var/log/auth.log

# Search logs by date
sudo grep "Feb 28" /var/log/syslog

# Filter by keyword
sudo grep "sshd" /var/log/auth.log

# Count failed logins
sudo grep "Failed password" /var/log/auth.log | wc -l
```

