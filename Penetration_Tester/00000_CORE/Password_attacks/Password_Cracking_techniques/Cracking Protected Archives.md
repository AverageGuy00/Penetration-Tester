- https://fileinfo.com/filetypes/compressed

Besides standalone files, we will often run across `archives` and `compressed files`—such as ZIP files—which are protected with a password.

There are many types of archive files. Some of the more commonly encountered file extensions include `tar`, `gz`, `rar`, `zip`, `vmdb/vmx`, `cpt`, `truecrypt`, `bitlocker`, `kdbx`, `deb`, `7z`, and `gzip`.

Note that not all archive types support native password protection, and in such cases, additional tools are often used to encrypt the files. For example, TAR files are commonly encrypted using `openssl` or `gpg`.

---
## Cracking ZIP files

The `ZIP` format is often heavily used in `Windows environments` to compress many files into one file. The process of cracking an `encrypted ZIP` file is similar to what we have seen already, except for using a different script to `extract the hashes`.

```bash
OathBornX@htb[/htb]$ zip2john ZIP.zip > zip.hash
OathBornX@htb[/htb]$ cat zip.hash 

ZIP.zip/customers.csv:$pkzip2$1*2*2*0*2a*1e*490e7510*0*42*0*2a*490e*409b*ef1e7feb7c1cf701a6ada7132e6a5c6c84c032401536faf7493df0294b0d5afc3464f14ec081cc0e18cb*$/pkzip2$:customers.csv:ZIP.zip::ZIP.zip
```

Once we have `extracted the hash`, we can use `JtR` to `crack` it with the desired password list.

```bash
OathBornX@htb[/htb]$ john --wordlist=rockyou.txt zip.hash

OathBornX@htb[/htb]$ john zip.hash --show

ZIP.zip/customers.csv:1234:customers.csv:ZIP.zip::ZIP.zip

1 password hash cracked, 0 left
```

---
## Cracking OpenSSL encrypted GZIP files

It’s not always obvious if a file is `password-protected`, especially when the extension masks the real format. `openssl` can be used to `encrypt data` (for example wrapping content and then compressing with `gzip`), so the file extension alone isn’t a reliable indicator of protection.

Determine the true file type with the `file` utility, e.g. `file targetfile`, and inspect the output to see if the file is a `gzip` archive, an Office document, or something else.

```bash
OathBornX@htb[/htb]$ file GZIP.gzip 

GZIP.gzip: openssl enc'd data with salted password
```

Cracking `OpenSSL`-encrypted files often yields `false positives` or outright failures because `openssl` will happily write garbage that looks valid until you verify it. A more reliable approach is to try direct decryption with `openssl` inside a `for`/`while` loop that reads a `wordlist`, checks the `openssl` exit status, and then validates the decrypted output (for example with `file` or `gunzip -t`) before declaring the `password` found.

```bash
OathBornX@htb[/htb]$ for i in $(cat rockyou.txt);do openssl enc -aes-256-cbc -d -in GZIP.gzip -k $i 2>/dev/null| tar xz;done

gzip: stdin: not in gzip format
tar: Child returned status 1
tar: Error is not recoverable: exiting now

gzip: stdin: not in gzip format
tar: Child returned status 1
tar: Error is not recoverable: exiting now
<SNIP>
```

Once the `for` loop has finished, we can check the current directory for a newly extracted file.

```bash
OathBornX@htb[/htb]$ ls

customers.csv  GZIP.gzip  rockyou.txt
```

---
## Cracking BitLocker-Encrypted drives

`BitLocker` is Microsoft’s `full-disk encryption` for Windows, using `AES` with `128-bit` or `256-bit` keys. If the `password` or `PIN` is lost, decryption can still be performed with the `recovery key`, a `48-digit` string produced at setup.

In enterprise setups users sometimes store personal data on `virtual drives` on company machines. To attack a `BitLocker` volume for testing, `bitlocker2john` extracts up to four hash variants: the first two map to the `BitLocker` `password` and the latter two to the `recovery key`. The `recovery key` is long and effectively random, so brute-forcing it is usually impractical without partial knowledge; focus testing on the first hash (`$bitlocker$0$...`) which corresponds to the `password`.

```bash
OathBornX@htb[/htb]$ bitlocker2john -i Backup.vhd > backup.hashes
OathBornX@htb[/htb]$ grep "bitlocker\$0" backup.hashes > backup.hash
OathBornX@htb[/htb]$ cat backup.hash

$bitlocker$0$16$02b329c0453b9273f2fc1b927443b5fe$1048576$12$00b0a67f961dd80103000000$60$d59f37e70696f7eab6b8f95ae93bd53f3f7067d5e33c0394b3d8e2d1fdb885cb86c1b978f6cc12ed26de0889cd2196b0510bbcd2a8c89187ba8ec54f
```

After extracting the `BitLocker` `hash`, use `hashcat` to attack the `password` hash. The `hashcat` mode for the `$bitlocker$0$...` format is `-m 22100`. Supply the hash file and a `wordlist`, and optionally add `rules` or combinator inputs to improve coverage.

```bash
OathBornX@htb[/htb]$ hashcat -a 0 -m 22100 '$bitlocker$0$16$02b329c0453b9273f2fc1b927443b5fe$1048576$12$00b0a67f961dd80103000000$60$d59f37e70696f7eab6b8f95ae93bd53f3f7067d5e33c0394b3d8e2d1fdb885cb86c1b978f6cc12ed26de0889cd2196b0510bbcd2a8c89187ba8ec54f' /usr/share/wordlists/rockyou.txt

<SNIP>

$bitlocker$0$16$02b329c0453b9273f2fc1b927443b5fe$1048576$12$00b0a67f961dd80103000000$60$d59f37e70696f7eab6b8f95ae93bd53f3f7067d5e33c0394b3d8e2d1fdb885cb86c1b978f6cc12ed26de0889cd2196b0510bbcd2a8c89187ba8ec54f:1234qwer
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 22100 (BitLocker)
Hash.Target......: $bitlocker$0$16$02b329c0453b9273f2fc1b927443b5fe$10...8ec54f
Time.Started.....: Sat Apr 19 17:49:25 2025 (1 min, 56 secs)
Time.Estimated...: Sat Apr 19 17:51:21 2025 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:       25 H/s (9.28ms) @ Accel:64 Loops:4096 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 2880/14344385 (0.02%)
Rejected.........: 0/2880 (0.00%)
Restore.Point....: 2816/14344385 (0.02%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:1044480-1048576
Candidate.Engine.: Device Generator
Candidates.#1....: pirate -> soccer9
Hardware.Mon.#1..: Util:100%

Started: Sat Apr 19 17:49:05 2025
Stopped: Sat Apr 19 17:51:22 2025
```

After successfully `cracking the password`, we can access the `encrypted drive`.
#### Mounting BitLocker-encrypted drives in Windows

Double-click the `.vhd` file. Since it is `encrypted`, Windows will initially show an error. After `mounting`, simply double-click the `BitLocker volume` to be prompted for the password.

#### Mounting BitLocker-encrypted drives in Linux (or macOS)

It is also possible to `mount` BitLocker-encrypted drives in `Linux (or macOS)`. To do this, we can use a tool called `dislocker`. 

```bash
OathBornX@htb[/htb]$ sudo apt-get install dislocker
```

Next, we create two folders which we will use to mount the VHD.

```bash
OathBornX@htb[/htb]$ sudo mkdir -p /media/bitlocker
OathBornX@htb[/htb]$ sudo mkdir -p /media/bitlockermount
```

We then use `losetup` to configure the VHD as [loop device](https://en.wikipedia.org/wiki/Loop_device), decrypt the drive using `dislocker`, and finally mount the decrypted volume:

```bash
OathBornX@htb[/htb]$ sudo losetup -f -P Backup.vhd
OathBornX@htb[/htb]$ sudo dislocker /dev/loop0p2 -u1234qwer -- /media/bitlocker
OathBornX@htb[/htb]$ sudo mount -o loop /media/bitlocker/dislocker-file /media/bitlockermount
```

If everything was done correctly, we can now browse the files:

```bash
OathBornX@htb[/htb]$ cd /media/bitlockermount/
OathBornX@htb[/htb]$ ls -la
```

Once we have analyzed the files on the mounted drive, we can unmount it using the following commands:

```bash
OathBornX@htb[/htb]$ sudo umount /media/bitlockermount
OathBornX@htb[/htb]$ sudo umount /media/bitlocker
```

