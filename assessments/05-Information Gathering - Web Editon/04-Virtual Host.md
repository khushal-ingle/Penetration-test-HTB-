
# Virtual Host (VHOST) Enumeration Walkthrough

---

## Task Goal

The objective of this task is to brute-force **virtual hosts (vhosts)** on the target web server in order to:

1. Identify hidden subdomains hosted on the same IP and port
2. Discover vhosts prefixed with specific strings:

   * `web`
   * `vm`
   * `br`
   * `a`
   * `su`

These vhosts are not resolvable via DNS directly and must be discovered using **Host header brute-forcing**.

---

## Step 1: Preparing Local Name Resolution

### Command executed

```bash
nano /etc/hosts
```

### What is happening here

The target domain `inlanefreight.htb` is added to `/etc/hosts` so that local DNS resolution works correctly during enumeration.

### Relevant entry added

```text
154.57.164.76 inlanefreight.htb
```

This ensures that all HTTP requests sent to `inlanefreight.htb` resolve to the correct target IP.

---

## Step 2: Building a Targeted VHOST Wordlist

### Command executed

```bash
grep -h '^web' /usr/share/wordlists/seclists/Discovery/DNS/* > web-subdomains.txt
```

### What is happening here

Instead of using a massive wordlist, the wordlist is **filtered by prefix** to reduce noise and speed up discovery.

This step is repeated multiple times with different prefixes:

* `web`
* `vm`
* `br`
* `a`
* `su`

Each filtered list is saved as `web-subdomains.txt`.

---

## Step 3: VHOST Enumeration Using Gobuster

### Tool used

```bash
gobuster vhost
```

Gobuster is used in **VHOST mode**, which sends HTTP requests with different `Host:` headers to detect virtual hosts configured on the server.

---

### General command structure

```bash
gobuster vhost \
-u http://inlanefreight.htb:31736 \
-w web-subdomains.txt \
--append-domain
```

### Important flags explained

* `-u`
  Target URL and port where the web server is listening

* `-w`
  Wordlist containing possible subdomain prefixes

* `--append-domain`
  Automatically appends `.inlanefreight.htb` to each word

---

## Step 4: Discovering Subdomains by Prefix

Each run targets a specific prefix and stops once a valid vhost is found.

---

### Prefix: `web`

### Result found

```text
Found: web17611.inlanefreight.htb
```

### Answer

```text
web17611.inlanefreight.htb
```

---

### Prefix: `vm`

### Result found

```text
Found: vm5.inlanefreight.htb
```

### Answer

```text
vm5.inlanefreight.htb
```

---

### Prefix: `br`

### Result found

```text
Found: browse.inlanefreight.htb
```

### Answer

```text
browse.inlanefreight.htb
```

---

### Prefix: `a`

### Result found

```text
Found: admin.inlanefreight.htb
```

### Answer

```text
admin.inlanefreight.htb
```

---

### Prefix: `su`

### Result found

```text
Found: support.inlanefreight.htb
```

### Answer

```text
support.inlanefreight.htb
```

---

## Final Answers Summary

| Prefix | Discovered Subdomain       |
| ------ | -------------------------- |
| web    | web17611.inlanefreight.htb |
| vm     | vm5.inlanefreight.htb      |
| br     | browse.inlanefreight.htb   |
| a      | admin.inlanefreight.htb    |
| su     | support.inlanefreight.htb  |

---

## Key Takeaway

* DNS brute-forcing alone is **not sufficient** when vhosts are configured
* VHOST enumeration relies on **HTTP Host header manipulation**
* Filtering wordlists by prefix greatly improves speed and accuracy
* Gobuster’s `--append-domain` option is critical for clean results

---

