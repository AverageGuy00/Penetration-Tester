Unfortunately, the `tendency for users` to create` weak passwords` occurs even when `password policies` are in place. Most individuals follow predictable patterns when `creating passwords`, often incorporating words closely related to the service being accessed. For example, many employees choose passwords that include the company's name.

Commonly, users use the following additions for their `password` to fit the most common `password policies`:

|**Description**|**Password Syntax**|
|---|---|
|First letter is uppercase|`Password`|
|Adding numbers|`Password123`|
|Adding year|`Password2022`|
|Adding month|`Password02`|
|Last character is an exclamation mark|`Password2022!`|
|Adding special characters|`P@ssw0rd2022!`|

Hashcat merges lists of potential `names` and `labels` applying specific `mutation rules` to generate custom `wordlists`. It uses a defined `syntax` for `characters`, `words`, and `transformations`. The full `syntax` is detailed in the official `Hashcat rule-based attack documentation`, but the examples shown provide enough insight into how `Hashcat` mutates input `words`.

|**Function**|**Description**|
|---|---|
|`:`|Do nothing|
|`l`|Lowercase all letters|
|`u`|Uppercase all letters|
|`c`|Capitalize the first letter and lowercase others|
|`sXY`|Replace all instances of X with Y|
|`$!`|Add the exclamation character at the end|
Each `rule` is written on a new line and determines how a given word should be `transformed`. If we write the functions shown above into a file, it may look like this:

```bash
[!bash!]$ cat custom.rule

:
c
so0
c so0
sa@
c sa@
c sa@ so0
$!
$! c
$! so0
$! sa@
$! c so0
$! c sa@
$! so0 sa@
$! c so0 sa@
```

We can use the following command to apply the rules in `custom.rule` to each word in `password.list` and store the mutated results in `mut_password.list`.

```bash
[!bash!]$ hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
```

In this case, the single input word will produce` fifteen mutated variants`.

```bash
[!bash!]$ cat mut_password.list

password
Password
passw0rd
Passw0rd
p@ssword
P@ssword
P@ssw0rd
password!
Password!
passw0rd!
p@ssword!
Passw0rd!
P@ssword!
p@ssw0rd!
P@ssw0rd!
```

`Hashcat` and `JtR` include pre-built `rule lists` for `password generation and cracking`. One of the most effective and popular is `best64.rule`, which applies common `transformations` often leading to successful password guesses. Password cracking and custom `wordlist` creation are essentially guessing games. Targeted guessing improves if there is knowledge of the `password policy` and factors like the `company name`, `geographical region`, `industry`, and relevant `keywords` users might choose. The main exception is when `passwords` are leaked and directly accessible.

---

## Generating wordlists using CeWL

`CeWL` is a tool used to `extract potential words` from a company’s website and save them in a separate list. This list can then be combined with specific `rules` to create a customized `password list` with a higher chance of containing the correct employee password. Parameters include the spidering depth (`-d`), minimum word length (`-m`), storing words in lowercase (`--lowercase`), and specifying the output file for results (`-w`).

```bash
[!bash!]$ cewl https://www.inlanefreight.com -d 4 -m 6 --lowercase -w inlane.wordlist
[!bash!]$ wc -l inlane.wordlist
```

---
##### Methodology for the Assessment 

- Host the details on a website
- Use CeWL to get wordlist on the website 

```bash
cewl 10.10.16.25:8000/mark.html -d 1 -m 3 --with-numbers -w mark.list
```

- Generate combination by using the wordlist to itself using hashcat

```bash
hashcat --stdout -a 1 mark.list mark.list > combi.txt 
```

- Based on password policy, we will take the `N` amount of characters that would be valid from the list

```bash
awk 'length($0) >= 12' combi.txt > valid.txt
```

- Create custom rule using the same on module

```bash
cat << 'EOF' > custom.rule  
:  
c  
so0  
c so0  
sa@  
c sa@  
c sa@ so0  
$!  
$! c  
$! so0  
$! sa@  
$! c so0  
$! c sa@  
$! so0 sa@  
$! c so0 sa@  
EOF
```

- Run hashcat

```bash
hashcat -a 0 -m 0 97268a8ae45ac7d15c3cea4ce6ea550b /home/haz0x/pairs_length12.txt -r custom.rule

97268a8ae45ac7d15c3cea4ce6ea550b:Baseball1998!    
```








