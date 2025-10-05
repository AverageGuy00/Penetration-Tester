Scanning performance matters for `scanning performance`, `large networks`, or `low bandwidth` scenarios — tune timing and rates to balance speed, accuracy, and stealth.

Use `-T <0-5>` to set overall timing, `--min-parallelism <number>` to control concurrent probes, `--max-rtt-timeout <time>` to cap RTT waits, `--min-rate <number>` to force a minimum packet send rate, and `--max-retries <number>` to limit retries per probe.

Tip: start conservative (lower `-T`, modest `--min-rate`, fewer retries) then ramp up if reliable — like seasoning a scan, not torching the network.

### Timeouts

When `Nmap` sends a `probe` it waits for the target's `Round-Trip-Time` (`RTT`); by default it starts with a conservative `--min-RTT-timeout` of about `100ms`. Scanning `256` `hosts` against the `top 100 ports` means roughly `25,600` `port probes` — multiply that by `retries`, `RTTs`, and `script` checks and your `scan time` balloons quickly.

For large or low-bandwidth scans tune `timing` and `parallelism`: lower `--min-RTT-timeout` and `--max-rtt-timeout` for fast `LANs`, raise `--min-parallelism` or `--min-rate` and choose an appropriate `-T` template to trade `stealth` for `speed`, and cap `--max-retries` to avoid endless waits. Also consider splitting the `target range` or scanning in `waves` so you don’t accidentally `DDoS` your own curiosity. 😄

#### Default Scan

```bash
sudo nmap 10.129.2.0/24 -F

<SNIP>
Nmap done: 256 IP addresses (10 hosts up) scanned in 39.44 seconds
```
#### Optimized RTT

```bash
sudo nmap 10.129.2.0/24 -F --initial-rtt-timeout 50ms --max-rtt-timeout 100ms

<SNIP>
Nmap done: 256 IP addresses (8 hosts up) scanned in 12.29 seconds
```

When comparing the two scans, the `optimized scan` found two fewer `hosts` but finished in a quarter of the `scan time`, showing that setting `--initial-rtt-timeout` (too short) can cause missed `hosts` by cutting off probes before the expected `RTT` — a speed win that may cost you coverage (fast ≠ flawless, unless you like surprises).

---

# Max Retries

Reducing `--max-retries` (default `10`) to `0` makes `Nmap` skip any `port` that doesn't respond—no further `probes` are sent—so the `scan` runs much faster but can miss transient or lossy `hosts`/`ports`; use on reliable networks when speed > completeness. Fast, but potentially blindfolded.

#### Default Scan

```bash
sudo nmap 10.129.2.0/24 -F | grep "/tcp" | wc -l

23
```

#### Reduced Retries

```bash
sudo nmap 10.129.2.0/24 -F --max-retries 0 | grep "/tcp" | wc -l

21
```

Again, we recognize that accelerating can also have a negative effect on our results, which means we can overlook important information.

---

# Rates

In `white-box` tests where you're `whitelisted` by `security systems`, set `--min-rate <number>` so `Nmap` sends that many probes concurrently and attempts to maintain the rate—use when you know the available `bandwidth` to speed scans without flooding the network.

#### Default Scan

```bash
sudo nmap 10.129.2.0/24 -F -oN tnet.default

<SNIP>
Nmap done: 256 IP addresses (10 hosts up) scanned in 29.83 seconds
```

#### Optimized Scan

```bash
sudo nmap 10.129.2.0/24 -F -oN tnet.minrate300 --min-rate 300

<SNIP>
Nmap done: 256 IP addresses (10 hosts up) scanned in 8.67 seconds
```

---

# Timing

`Nmap` provides six `timing templates` (`-T <0-5>`) that control scan `aggressiveness`—higher values speed scans but increase `noise` and the chance `security systems` will block you; the default is `-T 3`. Use lower templates for `stealthy` or unstable networks and higher ones only when you have enough `bandwidth` and `authorization`—think of them like espresso shots: more speed, more jitter.

- `-T 0` / `-T paranoid`
- `-T 1` / `-T sneaky`
- `-T 2` / `-T polite`
- `-T 3` / `-T normal`
- `-T 4` / `-T aggressive`
- `-T 5` / `-T insane`

