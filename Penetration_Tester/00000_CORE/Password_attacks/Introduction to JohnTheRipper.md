`John the Ripper` (`JtR` or `john`) is a popular open-source `password cracking` tool originally developed for `UNIX` systems in 1996. It supports multiple attack methods, including `brute-force` and `dictionary` attacks, making it a staple in penetration testing. The `jumbo` variant is preferred due to performance optimizations, support for `64-bit` architectures, and additional features like `multilingual wordlists`. It also includes utilities to convert various file and hash formats into ones compatible with `JtR`. The tool is actively maintained to stay current with emerging `security` trends and technologies.

---
## Cracking modes

#### Single crack mode

`Single crack mode` is a rule-based cracking technique that is most useful when targeting Linux credentials. It generates password candidates based on the victim's username, home directory name, and [GECOS](https://en.wikipedia.org/wiki/Gecos_field) values (full name, room number, phone number, etc.). These strings are run against a large set of rules that apply common string modifications seen in passwords (e.g. a user whose real name is `Bob Smith` might use `Smith1` as their `password`).

```bash
r0lf:$6$ues25dIanlctrWxg$nZHVz2z4kCy1760Ee28M1xtHdGoy0C2cYzZ8l2sVa1kIa8K9gAcdBP.GI6ng/qA4oaMrgElZ1Cb9OeXO4Fvy3/:0:0:Rolf Sebastian:/home/r0lf:/bin/bash
```

Based on the `contents of the file`, it can be inferred that the victim has the username `r0lf`, the real name `Rolf Sebastian`, and the home directory `/home/r0lf`. `Single crack` mode will use this information to `generate candidate passwords` and test them against the hash. We can run the attack with the following command:

```bash
OathBornX@htb[/htb]$ john --single passwd

Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
[...SNIP...]        (r0lf)     
1g 0:00:00:00 DONE 1/3 (2025-04-10 07:47) 12.50g/s 5400p/s 5400c/s 5400C/s NAITSABESFL0R..rSebastiannaitsabeSr
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

---
#### Wordlist mode

`Wordlist mode` is used to crack passwords with a dictionary attack, meaning it attempts all passwords in a supplied wordlist against the password hash. The basic syntax for the command is as follows:

```bash
OathBornX@htb[/htb]$ john --wordlist=<wordlist_file> <hash_file>
```

The `wordlist file` (or files) used for `cracking password hashes` must be in plain text format, with one word per line. Multiple wordlists can be specified by separating them with a comma. Rules, either custom or built-in, can be specified by using the `--rules` argument.

---
#### Incremental Mode 

`Incremental mode` is a brute-force cracking method that generates password guesses dynamically using a `statistical model` based on `Markov chains`. It tests all character combinations within a defined `charset`, prioritizing more probable passwords learned from `training data`.

Unlike `wordlist mode`, it doesn’t rely on fixed lists but produces guesses on-the-fly, making it more exhaustive yet time-consuming. Compared to naive brute-force, `incremental mode` uses educated, probability-driven guesses to speed up cracking while covering the full search space.

```bash
OathBornX@htb[/htb]$ john --incremental <hash_file>
```

By default, `JtR` uses predefined `incremental modes` specified in its `configuration file `(`john.conf`), which define character sets and `password lengths`. You can `customize` these or define your own to `target passwords` that use special characters or specific patterns.

```bash
OathBornX@htb[/htb]$ grep '# Incremental modes' -A 100 /etc/john/john.conf

# Incremental modes

# This is for one-off uses (make your own custom.chr).
# A charset can now also be named directly from command-line, so no config
# entry needed: --incremental=whatever.chr
[Incremental:Custom]
File = $JOHN/custom.chr
MinLen = 0

# The theoretical CharCount is 211, we've got 196.
[Incremental:UTF8]
File = $JOHN/utf8.chr
MinLen = 0
CharCount = 196

# This is CP1252, a super-set of ISO-8859-1.
# The theoretical CharCount is 219, we've got 203.
[Incremental:Latin1]
File = $JOHN/latin1.chr
MinLen = 0
CharCount = 203

[Incremental:ASCII]
File = $JOHN/ascii.chr
MinLen = 0
MaxLen = 13
CharCount = 95

...SNIP...
```

---
#### Identifying Hash Formats

- https://openwall.info/wiki/john/sample-hashes
- https://pentestmonkey.net/cheat-sheet/john-the-ripper-hash-formats

To identify `hash types` for use with `John the Ripper` (`JtR`), consult resources like `JtR’s` sample hash documentation or `PentestMonkey’s hash list`, which map example hashes to their `JtR format strings`. Alternatively, use tools like `hashID` to analyze a` hash` and suggest possible formats. Adding the `-j` flag with `hashID` outputs the matching JtR format along with the hash type, streamlining module selection for cracking.

```bash
OathBornX@htb[/htb]$ hashid -j 193069ceb0461e1d40d216e32c79c704

Analyzing '193069ceb0461e1d40d216e32c79c704'
[+] MD2 [JtR Format: md2]
[+] MD5 [JtR Format: raw-md5]
[+] MD4 [JtR Format: raw-md4]
[+] Double MD5 
[+] LM [JtR Format: lm]
[+] RIPEMD-128 [JtR Format: ripemd-128]
[+] Haval-128 [JtR Format: haval-128-4]
[+] Tiger-128 
[+] Skein-256(128) 
[+] Skein-512(128) 
[+] Lotus Notes/Domino 5 [JtR Format: lotus5]
[+] Skype 
[+] Snefru-128 [JtR Format: snefru-128]
[+] NTLM [JtR Format: nt]
[+] Domain Cached Credentials [JtR Format: mscach]
[+] Domain Cached Credentials 2 [JtR Format: mscach2]
[+] DNSSEC(NSEC3) 
[+] RAdmin v2.x [JtR Format: radmin]
```

`JtR `supports hundreds of `hash formats`, some of which are listed in the table below. The `--format` argument can be supplied to instruct `JtR` which format `target hashe`s have.

---
## Cracking Files

It is also possible to `crack password-protected` or `encrypted files` with JtR. Multiple `"2john"` tools come with `JtR` that can be used to `process files` and `produce hashes compatible with JtR`. The generalized syntax for these tools is:

```bash
OathBornX@htb[/htb]$ <tool> <file_to_crack> > file.hash
```

|**Tool**|**Description**|
|---|---|
|`pdf2john`|Converts PDF documents for John|
|`ssh2john`|Converts SSH private keys for John|
|`mscash2john`|Converts MS Cash hashes for John|
|`keychain2john`|Converts OS X keychain files for John|
|`rar2john`|Converts RAR archives for John|
|`pfx2john`|Converts PKCS#12 files for John|
|`truecrypt_volume2john`|Converts TrueCrypt volumes for John|
|`keepass2john`|Converts KeePass databases for John|
|`vncpcap2john`|Converts VNC PCAP files for John|
|`putty2john`|Converts PuTTY private keys for John|
|`zip2john`|Converts ZIP archives for John|
|`hccap2john`|Converts WPA/WPA2 handshake captures for John|
|`office2john`|Converts MS Office documents for John|
|`wpa2john`|Converts WPA/WPA2 handshakes for John|
