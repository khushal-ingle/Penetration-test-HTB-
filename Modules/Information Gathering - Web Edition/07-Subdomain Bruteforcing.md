
## Subdomain Bruteforcing (Easy Explanation)

Subdomain bruteforcing is an **active reconnaissance technique** used to discover subdomains of a target domain by **guessing names from a wordlist** and checking whether they exist.

A subdomain is anything that comes before the main domain name.
Example:

* `www.example.com`
* `admin.example.com`
* `mail.example.com`

---

## Why Subdomain Bruteforcing Is Important

Many organizations host different services on subdomains, such as:

* Admin panels
* Development or staging servers
* APIs and dashboards
* Internal tools

These subdomains are often **less secured** than the main website, making them valuable during penetration testing.

---

## How Subdomain Bruteforcing Works

### 1. Wordlist Selection

A wordlist contains possible subdomain names.

Types of wordlists:

* **General-purpose**: Common names like `www`, `mail`, `dev`, `test`, `admin`
* **Targeted**: Based on company, technology, or industry
* **Custom**: Created using information gathered during reconnaissance

---

### 2. Iteration and Querying

Each word from the wordlist is attached to the main domain.

Example:

* `dev` + `example.com` → `dev.example.com`
* `admin` + `example.com` → `admin.example.com`

---

### 3. DNS Lookup

For every generated subdomain, a DNS query is performed.

If DNS returns an IP address, the subdomain exists.
If it does not resolve, the subdomain is ignored.

---

### 4. Filtering and Validation

Valid subdomains are collected.
They can be further tested by:

* Opening them in a browser
* Checking HTTP/HTTPS responses
* Identifying running services

---

## Tools Used for Subdomain Bruteforcing

| Tool        | Description                                        |
| ----------- | -------------------------------------------------- |
| dnsenum     | Full DNS enumeration and subdomain brute-forcing   |
| fierce      | Recursive and easy-to-use subdomain discovery      |
| dnsrecon    | Multiple DNS reconnaissance techniques             |
| amass       | Powerful and professional subdomain discovery tool |
| assetfinder | Fast and lightweight subdomain finder              |
| puredns     | High-speed DNS resolving and filtering             |

---

## dnsenum Explained Simply

`dnsenum` is a command-line tool written in Perl used for **DNS reconnaissance**.

It can:

* Enumerate DNS records (A, AAAA, MX, NS, TXT)
* Attempt zone transfers
* Brute-force subdomains using a wordlist
* Perform Google-based subdomain discovery
* Execute reverse DNS lookups
* Collect WHOIS information

---

## Example: Using dnsenum

### Command:

```bash
dnsenum --enum inlanefreight.com \
-f /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt \
-r
```

### Explanation:

* `dnsenum` runs the tool
* `--enum` enables multiple enumeration features
* `inlanefreight.com` is the target domain
* `-f` specifies the wordlist
* `-r` enables recursive subdomain enumeration

---

## Sample Output Meaning

```text
www.inlanefreight.com
support.inlanefreight.com
```

These subdomains:

* Exist in DNS
* Resolve to an IP address
* Should be analyzed further

---

## Key Points to Remember

* Subdomain bruteforcing guesses subdomains using wordlists
* DNS resolution confirms whether a subdomain exists
* More discovered subdomains mean a larger attack surface
* This technique is essential in web reconnaissance

---

* amass vs dnsenum
* Best wordlists for reconnaissance
