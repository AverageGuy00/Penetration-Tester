## Password Spraying

`Password spraying` is a `brute-force` attack where an attacker tries a single `password` across many `user` accounts. This technique is especially effective in environments where `users` are initialized with a default or standard `password`. For example, if `administrators` commonly use `ChangeMe123!` when setting up new accounts, it’s worthwhile to spray this `password` across all `user` accounts to identify any that haven’t been updated.

Depending on the target system, different `tools` may be used to carry out `password spraying` attacks. For `web applications`, `Burp Suite` is a strong option, while for `Active Directory` environments, tools like `NetExec` or `Kerbrute` are commonly used.

```bash
OathBornX@htb[/htb]$ netexec smb 10.100.38.0/24 -u <usernames.list> -p 'ChangeMe123!'
```

---
## Credential Stuffing

`Credential stuffing` is another type of `brute-force` attack where an attacker uses stolen `credentials` from one service to try access on others. Because many `users` reuse their `usernames` and `passwords` across multiple platforms—like `email`, `social media`, and `enterprise systems`—these attacks can succeed. Like `password spraying`, credential stuffing uses various `tools` depending on the target. For example, with a list of `username:password` pairs from a database leak, you can use `hydra` to perform a credential stuffing attack against an `SSH` service with this syntax:

```bash
OathBornX@htb[/htb]$ hydra -C user_pass.list ssh://10.100.38.23
```

---
## Default Credentials

- https://github.com/ihebski/DefaultCreds-cheat-sheet

Many systems—like `routers`, `firewalls`, and `databases`—come with default `credentials`. Best practice says `administrators` should change these during setup, but often they don’t, creating serious security risks.

There are many lists of known default `credentials` online, and dedicated `tools` to automate checking for them. A popular one is the `Default Credentials` Cheat Sheet, which you can install with `pip3`.

```bash
OathBornX@htb[/htb]$ pip3 install defaultcreds-cheat-sheet
```

Once installed, we can use the `creds` command to search for known `default credentials` associated with a specific `product or vendor`.

```bash
OathBornX@htb[/htb]$ creds search linksys

+---------------+---------------+------------+
| Product       |    username   |  password  |
+---------------+---------------+------------+
| linksys       |    <blank>    |  <blank>   |
| linksys       |    <blank>    |   admin    |
| linksys       |    <blank>    | epicrouter |
| linksys       | Administrator |   admin    |
| linksys       |     admin     |  <blank>   |
| linksys       |     admin     |   admin    |
| linksys       |    comcast    |    1234    |
| linksys       |      root     |  orion99   |
| linksys       |      user     |  tivonpw   |
| linksys (ssh) |     admin     |   admin    |
| linksys (ssh) |     admin     |  password  |
| linksys (ssh) |    linksys    |  <blank>   |
| linksys (ssh) |      root     |   admin    |
+---------------+---------------+------------+
```

Besides public lists and tools, default `credentials` often appear in product `documentation` that details setup steps. Some devices or apps ask you to create a `password` during install, but many just stick with a weak default.

If you identify specific `applications` on a customer’s network, you can gather their default `credentials` into a `username:password` list and reuse the `hydra` command to try access.

Default `credentials` aren’t just for apps—they’re common on `routers` too. One such list exists online. While it’s less common for router creds to stay default (since routers are critical for `network security`), mistakes happen. `Routers` in internal test environments often keep `default settings` and can be` exploited` to gain deeper access.

|**Router Brand**|**Default IP Address**|**Default Username**|**Default Password**|
|---|---|---|---|
|3Com|http://192.168.1.1|admin|Admin|
|Belkin|http://192.168.2.1|admin|admin|
|BenQ|http://192.168.1.1|admin|Admin|
|D-Link|http://192.168.0.1|admin|Admin|
|Digicom|http://192.168.1.254|admin|Michelangelo|
|Linksys|http://192.168.1.1|admin|Admin|
|Netgear|http://192.168.0.1|admin|password|
