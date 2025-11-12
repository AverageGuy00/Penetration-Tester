## Introduction to Password Cracking

`Passwords` are commonly `hashed` when stored, in order to provide some protection in the event they fall into the hands of an attacker. `Hashing` is a mathematical function which transforms an arbitrary number of input bytes into a (typically) fixed-size output; common examples of hash functions are `MD5`, and `SHA-256`.

```bash
bmdyy@htb:~$ echo -n Soccer06! | md5sum
40291c1d19ee11a7df8495c4cccefdfa  -

bmdyy@htb:~$ echo -n Soccer06! | sha256sum
a025dc6fabb09c2b8bfe23b5944635f9b68433ebd9a1a09453dd4fee00766d93  -
```

## Rainbow tables

`Rainbow tables` are large `pre-compiled` maps of input and output values for a given `hash function.` These can be used to very quickly identify the `password` if its corresponding hash has already been mapped.

|Password|MD5 Hash|
|---|---|
|123456|e10adc3949ba59abbe56e057f20f883e|
|12345|827ccb0eea8a706c4c34a16891f84e7b|
|123456789|25f9e794323b453885f5181f1b624d0b|
|password|5f4dcc3b5aa765d61d8327deb882cf99|
|iloveyou|f25a2fc72690b780b2a14e140ef6a9e0|

`Rainbow tables` are a powerful offline attack, so `salting` is used to defend against them. A `salt` is a random sequence of `bytes` (a per-password `nonce`) that is added to the `password` before computing its `hash`. Salts must be `unique` (not reused across the database) and ideally stored alongside the resulting `hash`. For example, if the salt `Th1sIsTh3S@lt_` is **prepended** to a password and you compute the `MD5` of the concatenation, you get the hash of `MD5(Th1sIsTh3S@lt_ || password)` — which produces a totally different digest than `MD5(password)` and defeats precomputed `rainbow table` lookups.

---

## Brute Force Attacks

A `Brute-force` attack systematically tries every possible combination across the target `charset` until the correct password is found; it is guaranteed to work given unlimited time because it exhausts the `keyspace`, but runtime grows exponentially with password length and charset size, so short passwords (commonly `<9 characters`) remain practical targets on consumer `GPU` rigs; defenders mitigate this with `rate-limiting`, long passphrases, and slow `KDF`s, while attackers typically prefer more efficient approaches like `mask attack` or `dictionary+rules` to reduce the search space and speed up cracking.

| Brute-force attempt | MD5 Hash                         |
| ------------------- | -------------------------------- |
| ...SNIP...          | ...SNIP...                       |
| Sxejd               | 2cdc813ef26e6d20c854adb107279338 |
| Sxeje               | 7703349a1f943f9da6d1dfcda51f3b63 |
| Sxejf               | db914f10854b97946046eabab2287178 |
| Sxejg               | c0ceb70c0e0f2c3da94e75ae946f29dc |
| Sxejh               | 4dca0d2b706e9344985d48f95e646ce8 |

---
## Dictionary Attack

A `dictionary attack`, also called a `wordlist attack`, efficiently cracks passwords by testing entries from a list of common or likely passwords instead of every possible character combination. This method saves time and is favored by penetration testers under tight time constraints. Popular `wordlists` include `rockyou.txt` and those found in `SecLists`.

```bash
OathBornX@htb[/htb]$ head --lines=20 /usr/share/wordlists/rockyou.txt 

123456
12345
123456789
password
iloveyou
princess
1234567
rockyou
12345678
```

