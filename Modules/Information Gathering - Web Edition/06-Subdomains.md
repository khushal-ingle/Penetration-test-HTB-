
## Subdomains – Web Reconnaissance 

When performing DNS reconnaissance, analysts often begin with the **primary domain** (e.g., `example.com`). However, modern web infrastructures rarely rely on a single domain. Instead, organisations create **subdomains** to logically separate services, environments, and applications.

A **subdomain** is a prefix added to the main domain, such as:

* `blog.example.com`
* `shop.example.com`
* `mail.example.com`

Each subdomain can point to a **different server, application, or environment**, making them critical targets during reconnaissance.

---

## Why Subdomains Matter in Web Recon

Subdomains frequently expose **attack surface that is not visible from the main website**. These hidden areas often lack the same security controls and monitoring.

### Common Risks Found on Subdomains

#### 1. Development and Staging Environments

* Used for testing features before production
* Often protected weakly or not at all
* May expose:

  * Debug panels
  * Test credentials
  * Source code
  * APIs with excessive permissions

Examples:

* `dev.example.com`
* `staging.example.com`
* `test.example.com`

---

#### 2. Hidden Login Portals

* Admin dashboards or internal tools
* Not linked publicly but accessible if discovered
* Common targets for:

  * Credential stuffing
  * Brute-force attacks
  * Default credentials

Examples:

* `admin.example.com`
* `panel.example.com`
* `cp.example.com`

---

#### 3. Legacy Applications

* Old applications left running after migrations
* Often unpatched and vulnerable
* May use:

  * Deprecated frameworks
  * Outdated CMS versions
  * Weak authentication mechanisms

Examples:

* `old.example.com`
* `legacy.example.com`

---

#### 4. Sensitive Information Exposure

* Misconfigured subdomains may leak:

  * Backup files
  * Configuration files
  * Internal documents
  * API keys or tokens

Examples:

* `files.example.com`
* `backup.example.com`
* `internal.example.com`

---

## Subdomain Enumeration

**Subdomain enumeration** is the process of discovering all subdomains associated with a target domain.

From a DNS perspective, subdomains are commonly mapped using:

* **A / AAAA records** – map subdomains to IP addresses
* **CNAME records** – alias one subdomain to another domain or service

---

## Subdomain Enumeration Techniques

### 1. Active Subdomain Enumeration

Active techniques involve **direct interaction with the target’s DNS infrastructure**.

#### a) DNS Zone Transfer

* Attempts to retrieve the entire DNS zone
* Only succeeds if the DNS server is misconfigured
* Rare in real-world targets

Command example:

```
dig axfr example.com @nameserver
```

---

#### b) Brute-Force Subdomain Enumeration

* Tests common or custom subdomain names
* Uses wordlists to guess valid subdomains
* Detectable but often effective

Common tools:

* `dnsenum`
* `ffuf`
* `gobuster`
* `amass (active mode)`

Advantages:

* Can uncover obscure or internal subdomains

Disadvantages:

* Generates traffic
* May trigger alerts or rate limits

---

### 2. Passive Subdomain Enumeration

Passive techniques rely on **third-party data sources** and do not interact directly with the target.

#### a) Certificate Transparency (CT) Logs

* Public logs of SSL/TLS certificates
* Certificates often list subdomains in the **SAN (Subject Alternative Name)** field
* Excellent source for discovering historical and active subdomains

---

#### b) Search Engine Reconnaissance

* Uses advanced search operators

Example:

```
site:example.com -www
```

Can reveal:

* Forgotten subdomains
* Indexed internal services
* Publicly exposed resources

---

#### c) DNS & OSINT Databases

* Aggregate DNS data from multiple sources
* Allow silent discovery of subdomains
* Examples include:

  * DNS datasets
  * Passive DNS platforms
  * Recon-focused OSINT tools

Advantages:

* Stealthy
* No direct contact with target

Disadvantages:

* May miss newly created subdomains

---

## Active vs Passive Enumeration – Comparison

| Aspect            | Active              | Passive                 |
| ----------------- | ------------------- | ----------------------- |
| Detectability     | High                | Low                     |
| Coverage          | Potentially broader | Depends on data sources |
| Stealth           | No                  | Yes                     |
| Real-time results | Yes                 | Sometimes outdated      |

---

## Key Takeaway

Subdomains **significantly expand the attack surface** of a target. Many high-impact vulnerabilities are discovered not on the main domain, but on forgotten or poorly secured subdomains.

A **hybrid approach**, combining:

* Passive enumeration (for stealth and breadth)
* Active enumeration (for depth and validation)

provides the most effective and realistic subdomain discovery strategy during web reconnaissance.

