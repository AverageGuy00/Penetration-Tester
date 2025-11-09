`Laudanum` is a repository of prebuilt payloads designed for injection into targets, enabling reverse `shell` access, remote command execution through the browser, and more. It includes injectable files for numerous `web application` languages such as `asp`, `aspx`, `jsp`, `php`, and others. It’s an essential tool for any `pentest`. If using your own VM, `Laudanum` comes preinstalled in `Parrot OS` and `Kali` by default.

---

## Laudanum Demonstration

Now that `Laudanum` is clear, we’ll examine a `web application` in our lab to test running a `web shell`. To follow along, add this entry to your `/etc/hosts` file on your attack VM or in `Pwnbox`:

##### Move a Copy for Modification

```bash
OathBornX@htb[/htb]$ cp /usr/share/laudanum/aspx/shell.aspx /home/tester/demo.aspx
```
##### Modify the Shell for Use 

![[{41A15517-B654-4818-BF57-DAA5F31769C4}.png]]

We’re `exploiting` the upload function at the bottom of the status page (Green Arrow). Select your `shell` file and hit upload. If successful, it will display the path where the file was saved (Yellow Arrow). Use the `upload function`, note the path, then navigate to it.

##### Take Advantage of the Upload Function

![[{B497A342-7877-4524-8409-A4DA8E5512FC}.png]]

Once the upload succeeds, navigate to your `web shell` to use its functions. The image below shows the process. Our `shell` was uploaded to the `\\files\` directory, retaining its original name. This isn’t always the case—some setups randomize filenames or restrict public directories as safeguards. For now, we’re lucky. In this app, the file resides at `status.inlanefreight.local\\files\demo.aspx`. Note the use of backslashes (`\`) in the path instead of forward slashes (`/`). Your browser will automatically convert this to `status.inlanefreight.local//files/demo.aspx` in the URL bar.
##### Navigate to Shell

![[{626041D5-A071-4C0D-83EA-77864614979F}.png]]

We can now utilize the Laudanum shell we uploaded to issue commands to the host. We can see in the example that the `systeminfo` command was run.
##### Shell Success

![[{A9D940E4-7E82-4C30-9830-709CC884B014}.png]]

