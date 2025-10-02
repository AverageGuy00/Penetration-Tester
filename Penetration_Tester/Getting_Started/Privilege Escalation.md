Initial access on a target system is usually with a `low-privileged user`. This means limited access to files, processes, and configurations.

To gain full control, we need `privilege escalation` — exploiting internal/local weaknesses to elevate permissions. On Linux this means reaching `root`, and on Windows it means reaching `Administrator` or `SYSTEM`.

# PrivEsc Checklists

`Privilege Escalation Checklists` are used after gaining initial access to a system to identify potential paths to higher privileges.

We must perform `thorough enumeration` of the target to discover misconfigurations, vulnerabilities, or weak points.

- `HackTricks` → detailed Linux and Windows PrivEsc checklists
- `PayloadsAllTheThings` → curated commands and techniques for PrivEsc

---

# Enumeration Scripts

`Automated privilege escalation enumeration` can be performed using scripts that collect system information and highlight possible weaknesses. On Linux, common tools include `LinEnum` and `linuxprivchecker`. On Windows, tools like `Seatbelt` and `JAWS` serve the same purpose. Another widely used option is `PEASS (Privilege Escalation Awesome Scripts SUITE)`, which provides up-to-date scripts for both Linux and Windows, making it a reliable choice for enumeration across different environments.

---

# Kernel Exploits

`Privilege escalation` often starts by checking for `kernel exploits` when the system runs an outdated operating system. Old kernels, like Linux 3.9.0 vulnerable to `DirtyCow (CVE-2016-5195)`, may allow attackers to gain `root` if unpatched. The same principle applies to older versions of Windows that contain unpatched `kernel vulnerabilities`.

---

# Vulnerable Software

Checking for `installed software` is another key part of `privilege escalation`. On Linux, we can list installed packages with `dpkg -l`, and on Windows we can inspect `C:\Program Files` or `Programs and Features`. Once identified, we should research whether any of the applications, especially older versions, have `public exploits` or `unpatched vulnerabilities`. Outdated software is often an easy path to escalate privileges if it contains known weaknesses.

---

# User Privileges

After initial access, analyzing `user privileges` is crucial for `privilege escalation`. If the compromised `user` can run specific `commands` as `root` or another `privileged user`, this can be leveraged to gain higher `access`. Common privilege vectors include `sudo` on `Linux`, `SUID` `binaries`, and `Windows Token Privileges`.

For example, `sudo` lets a `low-privileged user` execute `commands` as another `user`, typically `root`. This is often used to run restricted `commands` like `tcpdump` or access `directories` limited to `administrators`. To enumerate available `sudo` rights, we can use `sudo -l`. The results can reveal `commands` we’re allowed to run, with or without a `password`, which may provide a direct path to `root` or `system-level access`.

```bash
OathBornX@htb[/htb]$ sudo -l

[sudo] password for user1:
...SNIP...

User user1 may run the following commands on ExampleServer:
    (ALL : ALL) ALL
```

```bash
OathBornX@htb[/htb]$ sudo su -

[sudo] password for user1:
whoami
root
```

The `NOPASSWD` entry shows that the `/bin/echo` command can be executed without a password. This would be useful if we gained access to the server through a vulnerability and did not have the user's password. As it says `user`, we can run `sudo` as that user and not as root. To do so, we can specify the user with `-u user`:

If we find an `application` we can run with `sudo`, we can check `GTFOBins` for ways to exploit it and gain a `root shell`. For `Windows`, `LOLBAS` lists `applications` that can be abused to `execute commands` or `download files` with higher privileges. Both resources are key for `privilege escalation` during `post-exploitation`.

---

# Scheduled Task

`Scheduled tasks` exist in both `Linux` and `Windows` to run `scripts` at set intervals, such as `anti-virus scans` or `backup jobs`. These can be abused for `privilege escalation` in two main ways: adding new `scheduled tasks` or `cron jobs`, or tricking existing ones into executing `malicious software`.

On `Linux`, `cron jobs` are common. If we have `write permissions` over key files or directories like `/etc/crontab`, `/etc/cron.d`, or `/var/spool/cron/crontabs/root`, we can insert a `bash script` with a `reverse shell command`. When the job runs, it will give us a `reverse shell` with elevated privileges.

---

# Exposed Credentials

`Exposed credentials` are another common path for `privilege escalation`. They often appear in `configuration files`, `log files`, or `user history files` such as `.bash_history` in `Linux` or `PSReadLine` in `Windows`. Many `enumeration scripts` automatically scan for these `passwords` or `tokens` and present any findings, giving us potential access to higher-privileged `accounts` or `services`.

---

# SSH Keys

`SSH keys` can also provide a path to `privilege escalation`. If we have read access to a user's `.ssh` directory, we may find their private key in locations like `/home/user/.ssh/id_rsa` or `/root/.ssh/id_rsa`. Copying this `private key` to our own machine allows us to authenticate with `SSH` using the `-i` flag, giving us direct access as that user or even `root` if the key belongs to them.

```zsh
OathBornX@htb[/htb]$ vim id_rsa
OathBornX@htb[/htb]$ chmod 600 id_rsa
OathBornX@htb[/htb]$ ssh root@10.10.10.10 -i id_rsa

root@10.10.10.10#
```

If we have `write access` to a user's `.ssh` directory, we can insert our `public key` into `/home/user/.ssh/authorized_keys`. This allows persistent `SSH access` as that user. Since `SSH` will only trust keys written by the account owner, this works only after we already control that user. To prepare, we generate a new key with `ssh-keygen` and use the `-f` flag to set the output file before placing the `public key` in the target's `authorized_keys`.

```bash
OathBornX@htb[/htb]$ ssh-keygen -f key

Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase): *******
Enter same passphrase again: *******

Your identification has been saved in key
Your public key has been saved in key.pub
The key fingerprint is:
SHA256:...SNIP... user@parrot
The key's randomart image is:
+---[RSA 3072]----+
|   ..o.++.+      |
...SNIP...
|     . ..oo+.    |
+----[SHA256]-----+
```







