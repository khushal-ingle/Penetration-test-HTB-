
## What is DNS Reconnaissance?

DNS reconnaissance means:

* Asking DNS servers questions
* Collecting information about a domain
* Finding hidden assets like subdomains or servers

Attackers and pentesters use DNS because:

* It is **quiet** (less noisy than port scans)
* Misconfigured DNS can leak **important data**

---

## Common DNS Tools (Simple)

| Tool             | What it does                | When to use                 |
| ---------------- | --------------------------- | --------------------------- |
| `dig`            | Advanced DNS lookup tool    | Deep analysis, learning DNS |
| `nslookup`       | Simple DNS queries          | Quick checks                |
| `host`           | Very short output           | Fast lookups                |
| `dnsenum`        | Automatic subdomain finding | Large recon                 |
| `fierce`         | Finds subdomains            | Easy DNS recon              |
| `dnsrecon`       | All-in-one DNS recon        | Full enumeration            |
| `theHarvester`   | Finds emails & domains      | OSINT gathering             |
| Online DNS tools | Browser-based lookups       | When terminal not available |

---

## What is `dig`?

`dig` (Domain Information Groper) is the **most powerful DNS command**.
It lets you ask DNS servers questions like:

* What is the IP address?
* What are the mail servers?
* Who controls this domain?

---

## Basic `dig` Commands (Very Easy)

| Command                 | Meaning                          |
| ----------------------- | -------------------------------- |
| `dig domain.com`        | Get IP address                   |
| `dig domain.com A`      | IPv4 address                     |
| `dig domain.com AAAA`   | IPv6 address                     |
| `dig domain.com MX`     | Mail servers                     |
| `dig domain.com NS`     | Name servers                     |
| `dig domain.com TXT`    | Text records (SPF, verification) |
| `dig domain.com SOA`    | Domain owner info                |
| `dig +trace domain.com` | Shows DNS path step-by-step      |
| `dig -x IP`             | Reverse lookup (IP → name)       |
| `dig +short domain.com` | Only show result                 |

---

## Example: `dig google.com`

```bash
dig google.com
```

### Header (What happened?)

* Query was sent
* DNS replied successfully (`NOERROR`)
* This is a response, not a request

### Question Section

```
What is the IP address of google.com?
```

### Answer Section

```
google.com → 142.251.47.142
```

This means:

* Google’s server IP is `142.251.47.142`
* TTL = `0` → result is not cached (common for big websites)

### Footer

* Query time: how fast DNS replied
* Server: which DNS server answered
* Time and message size

---

## Why You See “Recursion Not Available”

```
WARNING: recursion requested but not available
```

This simply means:

* You asked the DNS server to do extra work
* That DNS server said **“I only answer, I don’t search”**

This is normal and **not an error**.

---

## Getting Only the IP (Clean Output)

```bash
dig +short hackthebox.com
```

Output:

```
104.18.20.126
104.18.21.126
```

Meaning:

* Website has **multiple IPs**
* Usually means **CDN or load balancer**

---

## Important Note

* Too many DNS queries can be detected
* Zone transfers and brute-force need permission
* DNS recon is **legal only in allowed scope**

---

### One-line summary:

> **DNS recon = asking smart questions to DNS servers to discover useful information about a target.**

