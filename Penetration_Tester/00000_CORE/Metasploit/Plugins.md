`Plugins` are third-party pieces of software integrated into the `Metasploit Framework` with permission from their creators. They can be commercial products (often with limited Community Editions) or independent projects. `Plugins` bring external functionality directly into `msfconsole` and `Metasploit Pro`, reducing tool switching and manual import/export work by automatically documenting findings into the `database`.

- `Plugins` work with the framework `API` to extend and manipulate `msfconsole`.
- They automate repetitive tasks and can add new `commands` to the `msfconsole`.
- When integrated, scan results, `hosts`, `services`, and `vulnerabilities` are populated into the `database` for at-a-glance use.
- They streamline workflows for the `pentester` by centralizing data, decreasing context switching, and improving traceability.

## Using Plugins

To start using a plugin, we will need to ensure it is installed in the correct directory on our machine. Navigating to `/usr/share/metasploit-framework/plugins`, which is the default directory for every new installation of `msfconsole`, should show us which plugins we have to our availability:

```bash
OathBornX@htb[/htb]$ ls /usr/share/metasploit-framework/plugins

aggregator.rb      beholder.rb        event_tester.rb  komand.rb     msfd.rb    nexpose.rb   request.rb  session_notifier.rb  sounds.rb  token_adduser.rb  wmap.rb
alias.rb           db_credcollect.rb  ffautoregen.rb   lab.rb        msgrpc.rb  openvas.rb   rssfeed.rb  session_tagger.rb    sqlmap.rb  token_hunter.rb
auto_add_route.rb  db_tracker.rb      ips_filter.rb    libnotify.rb  nessus.rb  pcap_log.rb  sample.rb   socket_logger.rb     thread.rb  wiki.rb
```

If the plugin is found here, we can fire it up inside `msfconsole` and will be met with the greeting output for that specific plugin, signaling that it was successfully loaded in and is now ready to use:
#### MSF - Load Nessus

```bash
msf6 > load nessus

[*] Nessus Bridge for Metasploit
[*] Type nessus_help for a command listing
[*] Successfully loaded Plugin: Nessus


msf6 > nessus_help

Command                     Help Text
-------                     ---------
Generic Commands            
-----------------           -----------------
nessus_connect              Connect to a Nessus server
nessus_logout               Logout from the Nessus server
nessus_login                Login into the connected Nessus server with a different username and 

<SNIP>

nessus_user_del             Delete a Nessus User
nessus_user_passwd          Change Nessus Users Password
                            
Policy Commands             
-----------------           -----------------
nessus_policy_list          List all polciies
nessus_policy_del           Delete a policy
```

If the plugin is not installed correctly, we will receive the following error upon trying to load it.

```bash
msf6 > load Plugin_That_Does_Not_Exist

[-] Failed to load plugin from /usr/share/metasploit-framework/plugins/Plugin_That_Does_Not_Exist.rb: cannot load such file -- /usr/share/metasploit-framework/plugins/Plugin_That_Does_Not_Exist.rb
```

---
## Installing custom plugins (Parrot OS)

New, more popular `plugins` are bundled with each update of the `Parrot OS` distro and distributed through the `Parrot update repo`. To install a custom plugin that isn't included in an official update, download the provided `.rb` file from the author's page and place it in `/usr/share/metasploit-framework/plugins` with the correct file `permissions`. For example, to install `DarkOperator's Metasploit-Plugins`, copy the `Ruby` `.rb` files into `/usr/share/metasploit-framework/plugins` and ensure ownership and execute/read permissions are set so `Metasploit` can load them.
#### Downloading MSF Plugins

```bash
OathBornX@htb[/htb]$ git clone https://github.com/darkoperator/Metasploit-Plugins
OathBornX@htb[/htb]$ ls Metasploit-Plugins

aggregator.rb      ips_filter.rb  pcap_log.rb          sqlmap.rb
alias.rb           komand.rb      pentest.rb           thread.rb
auto_add_route.rb  lab.rb         request.rb           token_adduser.rb
beholder.rb        libnotify.rb   rssfeed.rb           token_hunter.rb
db_credcollect.rb  msfd.rb        sample.rb            twitt.rb
db_tracker.rb      msgrpc.rb      session_notifier.rb  wiki.rb
event_tester.rb    nessus.rb      session_tagger.rb    wmap.rb
ffautoregen.rb     nexpose.rb     socket_logger.rb
growl.rb           openvas.rb     sounds.rb
```

#### MSF - Copying Plugin to MSF

```shell-session
OathBornX@htb[/htb]$ sudo cp ./Metasploit-Plugins/pentest.rb /usr/share/metasploit-framework/plugins/pentest.rb
```

Afterward, launch `msfconsole` and check the plugin's installation by running the `load` command. After the plugin has been loaded, the `help menu` at the `msfconsole` is automatically extended by additional functions.

#### MSF - Load Plugin

```shell-session
OathBornX@htb[/htb]$ msfconsole -q

msf6 > load pentest

       ___         _          _     ___ _           _
      | _ \___ _ _| |_ ___ __| |_  | _ \ |_  _ __ _(_)_ _
      |  _/ -_) ' \  _/ -_|_-<  _| |  _/ | || / _` | | ' \ 
      |_| \___|_||_\__\___/__/\__| |_| |_|\_,_\__, |_|_||_|
                                              |___/
      
Version 1.6
Pentest Plugin loaded.
by Carlos Perez (carlos_perez[at]darkoperator.com)
[*] Successfully loaded plugin: pentest

```

Many people write many different plugins for the Metasploit framework. They all have a specific purpose and can be an excellent help to save time after familiarizing ourselves with them. Check out the list of popular plugins below:

| Tool             | Status        |
| ---------------- | ------------- |
| `nMap`           | pre-installed |
| `NexPose`        | pre-installed |
| `Nessus`         | pre-installed |
| `Mimikatz` (v1)  | pre-installed |
| `Stdapi`         | pre-installed |
| `Railgun`        | not specified |
| `Priv`           | not specified |
| `Incognito`      | pre-installed |
| `Darkoperator's` | not specified |

# Mixins

The Metasploit Framework is written in `Ruby`, an object-oriented language that enables a lot of the framework’s flexibility. `Mixins` are reusable modules that provide methods to other classes via inclusion rather than inheritance, so you get shared behavior without forcing a parent/child relationship.

Use cases for `mixins` include providing many optional features to a single class and sharing one feature across many classes. In `Ruby` this is implemented with the `include` keyword, which pulls a `Module`’s methods into the target class.

If you’re just starting with `Metasploit` and `msfconsole`, you don’t need to worry about `mixins` — they’re an implementation detail that explains why modules can be so easily customized and extended.