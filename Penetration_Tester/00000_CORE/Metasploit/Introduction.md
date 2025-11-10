`Msfconsole` is your main weapon for interacting with the `Metasploit Framework`. It’s the one-stop shop to launch `exploits`, configure `payloads`, and run `auxiliary modules`—all from a single terminal.

- Only supported interface for most `Metasploit` features
- Text-based `console`, no GUI distractions
- Most feature-rich and stable interface
- Supports command history, tab completion, and line editing
- Can run external shell commands without leaving the `console`

Mastering `msfconsole` means you control the entire `Metasploit` arsenal efficiently—no fluff, just raw power.

#### Modules

The `Modules` detailed above are split into separate categories in this folder. We will go into detail about these in the next sections. They are contained in the following folders:

```bash
OathBornX@htb[/htb]$ ls /usr/share/metasploit-framework/modules

auxiliary  encoders  evasion  exploits  nops  payloads  post
```

#### Plugins

Plugins offer the pentester more flexibility when using the `msfconsole` since they can easily be manually or automatically loaded as needed to provide extra functionality and automation during our assessment.

```bash
OathBornX@htb[/htb]$ ls /usr/share/metasploit-framework/plugins/

aggregator.rb      ips_filter.rb  openvas.rb           sounds.rb
alias.rb           komand.rb      pcap_log.rb          sqlmap.rb
auto_add_route.rb  lab.rb         request.rb           thread.rb
beholder.rb        libnotify.rb   rssfeed.rb           token_adduser.rb
db_credcollect.rb  msfd.rb        sample.rb            token_hunter.rb
db_tracker.rb      msgrpc.rb      session_notifier.rb  wiki.rb
event_tester.rb    nessus.rb      session_tagger.rb    wmap.rb
ffautoregen.rb     nexpose.rb     socket_logger.rb
```

#### Scripts

Meterpreter functionality and other useful scripts.

```bash
OathBornX@htb[/htb]$ ls /usr/share/metasploit-framework/scripts/

meterpreter  ps  resource  shell
```

#### Tools

Command-line utilities that can be called directly from the `msfconsole` menu.

```bash
OathBornX@htb[/htb]$ ls /usr/share/metasploit-framework/tools/

context  docs     hardware  modules   payloads
dev      exploit  memdump   password  recon
```

---
## MSF Engagement Structure

The `MSF` engagement structure is divided into five main categories to help locate and use framework features more effectively: `Enumeration`, `Preparation`, `Exploitation`, `Privilege Escalation`, and `Post-Exploitation`. Each category contains subcategories intended for specific tasks such as `Service Validation` and `Vulnerability Research`.

- `Enumeration`
- `Preparation`
- `Exploitation`
- `Privilege Escalation`
- `Post-Exploitation`

Familiarize yourself with how these categories relate so you can map MSF modules and workflows to the right phase.

![[S04_SS03.png]]

