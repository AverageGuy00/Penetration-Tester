
# Shell

The most commonly used shell in Linux is the `Bourne-Again Shell` (`BASH`), and is part of the GNU project. Everything we do through the GUI we can do with the shell. The shell gives us many more possibilities to interact with programs and processes to get information faster. Besides, many processes can be easily automated with smaller or larger scripts that make manual work much easier.

Besides Bash, there also exist other shells like [Tcsh/Csh](https://en.wikipedia.org/wiki/Tcsh), [Ksh](https://en.wikipedia.org/wiki/KornShell), [Zsh](https://en.wikipedia.org/wiki/Z_shell), [Fish](https://en.wikipedia.org/wiki/Friendly_interactive_shell) shell and others.

---

| **Special Character** | **Description**                            |
| --------------------- | ------------------------------------------ |
| `\d`                  | Date (Mon Feb 6)                           |
| `\D{%Y-%m-%d}`        | Date (YYYY-MM-DD)                          |
| `\H`                  | Full hostname                              |
| `\j`                  | Number of jobs managed by the shell        |
| `\n`                  | Newline                                    |
| `\r`                  | Carriage return                            |
| `\s`                  | Name of the shell                          |
| `\t`                  | Current time 24-hour (HH:MM:SS)            |
| `\T`                  | Current time 12-hour (HH:MM:SS)            |
| `\@`                  | Current time                               |
| `\u`                  | Current username                           |
| `\w`                  | Full path of the current working directory |

---

# Commands

| Command  |           Description            |           Example            |
| :------: | :------------------------------: | :--------------------------: |
|  `pwd`   |  Show current working directory  |            `pwd`             |
|   `ls`   |    List files and directories    |           `ls -l`            |
|   `cd`   |         Change directory         |          `cd /home`          |
| `touch`  |       Create an empty file       |      `touch notes.txt`       |
|   `cp`   |    Copy files or directories     |   `cp file.txt backup.txt`   |
|   `mv`   | Move or rename files/directories |     `mv old.txt new.txt`     |
|   `rm`   |           Remove files           |        `rm file.txt`         |
| `rmdir`  |      Remove empty directory      |       `rmdir testdir`        |
| `mkdir`  |      Create a new directory      |       `mkdir projects`       |
|  `cat`   |     View contents of a file      |       `cat notes.txt`        |
|  `less`  |     View file with scrolling     |        `less log.txt`        |
|  `head`  |   Show first 10 lines of file    |       `head data.csv`        |
|  `tail`  |    Show last 10 lines of file    |      `tail -f log.txt`       |
|  `find`  |         Search for files         | `find / -name "config.json"` |
|  `grep`  |       Search inside files        |    `grep "error" log.txt`    |
| `chmod`  |     Change file permissions      |    `chmod 755 script.sh`     |
| `chown`  |        Change file owner         |  `chown user:user file.txt`  |
|   `ps`   |      Show running processes      |           `ps aux`           |
|  `kill`  |       Terminate a process        |         `kill 1234`          |
|  `top`   |    Show live system processes    |            `top`             |
|   `df`   |         Show disk usage          |           `df -h`            |
|   `du`   |       Show directory size        |       `du -sh folder`        |
| `uname`  |         Show system info         |          `uname -a`          |
| `whoami` |        Show current user         |           `whoami`           |
|  `man`   |       Display help/manual        |           `man ls`           |

| **Command** |                                                          **Description**                                                           |
| :---------: | :--------------------------------------------------------------------------------------------------------------------------------: |
|  `whoami`   |                                                     Displays current username.                                                     |
|    `id`     |                                                       Returns users identity                                                       |
| `hostname`  |                                          Sets or prints the name of current host system.                                           |
|   `uname`   |                           Prints basic information about the operating system name and system hardware.                            |
|    `pwd`    |                                                  Returns working directory name.                                                   |
| `ifconfig`  | The ifconfig utility is used to assign or to view an address to a network interface and/or configure network interface parameters. |
|    `ip`     |                      Ip is a utility to show or manipulate routing, network devices, interfaces and tunnels.                       |
|  `netstat`  |                                                       Shows network status.                                                        |
|    `ss`     |                                              Another utility to investigate sockets.                                               |
|    `ps`     |                                                       Shows process status.                                                        |
|    `who`    |                                                     Displays who is logged in.                                                     |
|    `env`    |                                          Prints environment or sets and executes command.                                          |
|   `lsblk`   |                                                        Lists block devices.                                                        |
|   `lsusb`   |                                                         Lists USB devices                                                          |
|   `lsof`    |                                                        Lists opened files.                                                         |
|   `lspci`   |                                                         Lists PCI devices.                                                         |

---



```bash
find / -type f -name *.conf -user root -size +20k -newermt 2020-03-03 -exec ls -al {} \; 2>/dev/null
```

|      **Option**       |                                                                                                                                **Description**                                                                                                                                 |
| :-------------------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|       `-type f`       |                                                                                          Hereby, we define the type of the searched object. In this case, '`f`' stands for '`file`'.                                                                                           |
|    `-name *.conf`     |                                                                  With '`-name`', we indicate the name of the file we are looking for. The asterisk (`*`) stands for 'all' files with the '`.conf`' extension.                                                                  |
|     `-user root`      |                                                                                                          This option filters all files whose owner is the root user.                                                                                                           |
|     `-size +20k`      |                                                                              We can then filter all the located files and specify that we only want to see the files that are larger than 20 KiB.                                                                              |
| `-newermt 2020-03-03` |                                                                                         With this option, we set the date. Only files newer than the specified date will be presented.                                                                                         |
| `-exec ls -al {} \;`  | This option executes the specified command, using the curly brackets as placeholders for each result. The backslash escapes the next character from being interpreted by the shell because otherwise, the semicolon would terminate the command and not reach the redirection. |
|     `2>/dev/null`     |                 This is a `STDERR` redirection to the '`null device`', which we will come back to in the next section. This redirection ensures that no errors are displayed in the terminal. This redirection must `not` be an option of the 'find' command.                  |

---

# FILTER CONTENT

| Command                 | Description                                                            | Example                                       |
| ----------------------- | ---------------------------------------------------------------------- | --------------------------------------------- |
| `cat file \| more`      | Shows file content page by page, output remains in terminal after quit | `cat /etc/passwd \| more`                     |
| `less file`             | Similar to more but with more features, output disappears after quit   | `less /etc/passwd`                            |
| `head file`             | Shows first 10 lines of a file (default)                               | `head /etc/passwd`                            |
| `tail file`             | Shows last 10 lines of a file (default)                                | `tail /etc/passwd`                            |
| `sort`                  | Sorts output alphabetically or numerically                             | `cat /etc/passwd \| sort`                     |
| `grep pattern`          | Searches for matching patterns                                         | `cat /etc/passwd \| grep "/bin/bash"`         |
| `grep -v pattern`       | Excludes matching patterns                                             | `cat /etc/passwd \| grep -v "nologin\|false"` |
| `cut -d":" -f1`         | Cuts specific fields using delimiter                                   | `cat /etc/passwd \| cut -d":" -f1`            |
| `tr ":" " "`            | Translates/replaces characters                                         | `cat /etc/passwd \| tr ":" " "`               |
| `column -t`             | Formats output into a table                                            | `cat /etc/passwd \| tr ":" " " \| column -t`  |
| `awk '{print $1, $NF}'` | Prints specific columns (first & last)                                 | `cat /etc/passwd \| awk '{print $1, $NF}'`    |
| `sed 's/bin/HTB/g'`     | Substitutes text using regex                                           | `cat /etc/passwd \| sed 's/bin/HTB/g'`        |
| `wc -l`                 | Counts lines in output                                                 | `cat /etc/passwd \| wc -l`                    |


---

# File Permissions

Permission bits: r=4, w=2, x=1

| Octal | Binary | Permission | Use Case Example                    |
|-------|--------|------------|-------------------------------------|
| 7     | 111    | rwx        | Full access                        |
| 6     | 110    | rw-        | Read & write                       |
| 5     | 101    | r-x        | Read & execute                     |
| 4     | 100    | r--        | Read only                          |
| 0     | 000    | ---        | No permissions                     |


Example:  
`chmod 754 file` → rwx r-x r--
# Common permission combos

| Octal | Permission  | Typical Use Case        |
| ----- | ----------- | ----------------------- |
| 644   | rw- r-- r-- | Text files, configs     |
| 600   | rw- --- --- | Private files, SSH keys |
| 755   | rwx r-x r-x | Executables, scripts    |
| 700   | rwx --- --- | Owner-only executables  |

---

# User Management

| **Command** |                                                                      **Description**                                                                       |
| :---------: | :--------------------------------------------------------------------------------------------------------------------------------------------------------: |
|   `sudo`    |                                                            Execute command as a different user.                                                            |
|    `su`     | The `su` utility requests appropriate user credentials via PAM and switches to that user ID (the default user is the superuser). A shell is then executed. |
|  `useradd`  |                                                 Creates a new user or update default new user information.                                                 |
|  `userdel`  |                                                         Deletes a user account and related files.                                                          |
|  `usermod`  |                                                                  Modifies a user account.                                                                  |
| `addgroup`  |                                                                Adds a group to the system.                                                                 |
| `delgroup`  |                                                              Removes a group from the system.                                                              |
|  `passwd`   |                                                                   Changes user password.                                                                   |

---

# Systemd

### Basic Service Management
`systemctl status service` → Show status  
`systemctl start service` → Start service  
`systemctl stop service` → Stop service  
`systemctl restart service` → Restart service  
`systemctl enable service` → Enable at boot  
`systemctl disable service` → Disable at boot  
### Unit Management
`systemctl list-units` → List loaded units  
`systemctl list-unit-files` → List all unit files  
`systemctl cat service` → Show unit file content  
`systemctl edit service` → Override or extend unit  
### Timers
`systemctl list-timers` → Show active timers  
`systemctl start job.timer` → Start a timer  
`systemctl enable job.timer` → Enable at boot  

**Timer example (`backup.timer`)**  
```
[Unit]
Description=Run backup daily

[Timer]
OnCalendar=daily
Persistent=true

[Install]
WantedBy=timers.target
```
### Logs
`journalctl -u service` → Logs for a service  
`journalctl -xe` → Detailed logs (with errors)  
`journalctl -f` → Follow live logs  
### Useful Shortcuts
`systemctl daemon-reload` → Reload units after edits  
`systemctl is-enabled service` → Check if enabled  
`systemctl isolate target` → Switch runlevel/target  

---

# Cron Cheatsheet

### Crontab Basics
`crontab -e` → Edit current user’s crontab  
`crontab -l` → List current user’s jobs  
`crontab -r` → Remove current user’s crontab  
`sudo crontab -e -u user` → Edit another user’s crontab  
### Crontab Format
```
*  *  *  *  *  command
|  |  |  |  |
|  |  |  |  +--- Day of week (0-7, Sun=0/7)
|  |  |  +------ Month (1-12)
|  |  +--------- Day of month (1-31)
|  +------------ Hour (0-23)
+--------------- Minute (0-59)
```

### Time Shorthands

`@reboot` → Run once at startup  
`@hourly` → Run once per hour  
`@daily`  → Run once per day  
`@weekly` → Run once per week  
`@monthly` → Run once per month  
`@yearly` or `@annually` → Run once per year  
### Examples
`0 */6 * * * /path/to/update.sh` → Every 6 hours  
`0 0 1 * * /path/to/script.sh` → 1st of month at midnight  
`0 0 * * 0 /path/to/cleanup.sh` → Every Sunday at midnight  
`0 0 * * 7 /path/to/backup.sh` → Every Sunday at midnight  
### Tips
- Logs usually stored in `/var/log/syslog` or via `journalctl`.  
- Use `MAILTO="user@example.com"` at top of crontab for email notifications.  
- Use absolute paths in scripts to avoid `$PATH` issues.  

---

