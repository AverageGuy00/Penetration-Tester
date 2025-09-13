
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

---

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