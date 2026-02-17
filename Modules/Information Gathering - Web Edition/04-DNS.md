
The **Domain Name System (DNS)** functions like the internet’s GPS. Humans prefer memorable names such as `www.example.com`, while computers communicate using numerical **IP addresses** like `192.0.2.1`. DNS bridges this gap by translating domain names into IP addresses.

Without DNS, users would need to remember IP addresses for every website, making internet usage inefficient and error-prone. DNS enables fast, reliable, and scalable navigation across the internet.

---

## How DNS Works (Step-by-Step Resolution)

<img width="1025" height="220" alt="image" src="https://github.com/user-attachments/assets/c72bd2c5-01aa-419e-8a4b-2a39360da649" />


When a user enters `www.example.com` into a browser, the following process occurs:

1. **Local Cache Check**
   The system first checks the browser and OS DNS cache for a previously resolved IP.

2. **DNS Resolver (Recursive Resolver)**
   If not cached, the query is sent to a DNS resolver (usually provided by the ISP or a public resolver like Google DNS).

3. **Root Name Server**
   The resolver queries a root server.
   The root server does not know the IP but responds with the address of the relevant **TLD name server**.

4. **TLD Name Server**
   The TLD server (e.g., `.com`) responds with the location of the **authoritative name server** for `example.com`.

5. **Authoritative Name Server**
   This server contains the actual DNS records and returns the correct IP address.

6. **Response & Caching**
   The resolver returns the IP to the client and caches it for future queries.

7. **Connection Established**
   The browser connects directly to the web server using the resolved IP address.

---

## DNS Resolution Analogy

DNS works like a **relay race**:

* Client → Resolver → Root → TLD → Authoritative → Resolver → Client
  Each step narrows the search until the exact destination (IP address) is found.

---

## Hosts File (Local Name Resolution)

The **hosts file** provides a manual way to map hostnames to IP addresses, bypassing DNS entirely.

### File Locations

* **Windows:**
  `C:\Windows\System32\drivers\etc\hosts`
* **Linux / macOS:**
  `/etc/hosts`

### Format

```txt
<IP Address>    <Hostname> [Alias]
```

### Examples

```txt
127.0.0.1       localhost
192.168.1.10    devserver.local
```

### Common Use Cases

* **Local development**

```txt
127.0.0.1       myapp.local
```

* **Testing connectivity**

```txt
192.168.1.20    testserver.local
```

* **Blocking websites**

```txt
0.0.0.0         unwanted-site.com
```

Changes apply immediately after saving the file with admin/root privileges.

---

## DNS Zones and Zone Files

### DNS Zone

A **DNS zone** is a portion of the domain namespace managed by a specific administrator or organization.
Example: `example.com` and its subdomains (`mail.example.com`, `blog.example.com`).

### Zone File

A **zone file** defines all DNS records for a zone and resides on authoritative name servers.

### Sample Zone File

```zone
$TTL 3600
@   IN SOA ns1.example.com. admin.example.com. (
        2024060401
        3600
        900
        604800
        86400 )

@   IN NS ns1.example.com.
@   IN NS ns2.example.com.
@   IN MX 10 mail.example.com.
www IN A 192.0.2.1
mail IN A 198.51.100.1
ftp IN CNAME www.example.com.
```

---

## Common DNS Concepts

| Concept              | Description                  | Example              |
| -------------------- | ---------------------------- | -------------------- |
| Domain Name          | Human-readable identifier    | `www.example.com`    |
| IP Address           | Numerical network identifier | `192.0.2.1`          |
| DNS Resolver         | Resolves domain → IP         | ISP DNS, `8.8.8.8`   |
| Root Server          | Top of DNS hierarchy         | `a.root-servers.net` |
| TLD Server           | Handles `.com`, `.org`, etc. | Verisign (.com)      |
| Authoritative Server | Holds final DNS records      | Hosting provider     |

---

## DNS Record Types

| Record | Full Name          | Purpose                 | Example                         |
| ------ | ------------------ | ----------------------- | ------------------------------- |
| A      | Address            | Domain → IPv4           | `www IN A 192.0.2.1`            |
| AAAA   | IPv6 Address       | Domain → IPv6           | `www IN AAAA 2001:db8::1`       |
| CNAME  | Canonical Name     | Alias to another domain | `blog IN CNAME web.example.net` |
| MX     | Mail Exchange      | Mail server routing     | `IN MX 10 mail.example.com`     |
| NS     | Name Server        | Delegates zone          | `IN NS ns1.example.com`         |
| TXT    | Text               | Verification / security | SPF, DKIM                       |
| SOA    | Start of Authority | Zone metadata           | Serial, TTLs                    |
| SRV    | Service            | Service discovery       | `_sip._udp.example.com`         |
| PTR    | Pointer            | Reverse DNS             | IP → hostname                   |

### Meaning of `IN`

`IN` stands for **Internet** and specifies the DNS class.
It indicates that the record applies to standard internet protocols (IP).

---

## Why DNS Matters in Web Recon & Pentesting

DNS is a **goldmine for reconnaissance**:

### 1. Asset Discovery

* Subdomains (`dev`, `staging`, `vpn`)
* Mail servers
* Third-party services via CNAMEs

### 2. Infrastructure Mapping

* NS records reveal hosting providers
* Load balancers and CDNs
* Internal naming conventions

### 3. Attack Surface Expansion

* Forgotten subdomains
* Misconfigured DNS entries
* Leaked TXT records (SPF, verification tokens)

### 4. Intelligence Gathering

* TXT records may reveal:

  * Email providers
  * Password managers
  * Cloud services
* Useful for **phishing, social engineering, and lateral movement**

---

