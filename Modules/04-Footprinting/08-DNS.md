

## 1. What is DNS?

DNS (Domain Name System) is the Internet’s phonebook.

- Humans remember names:  
    `academy.hackthebox.com`
    
- Computers communicate using IPs:  
    `10.129.14.5`
    

DNS performs translation:

- Domain Name → IP Address
- IP Address → Domain Name (reverse lookup)

DNS is a distributed system, not a centralized database.

It can be compared to a library with many phonebooks spread worldwide.

---

## 2. Why DNS is Important?

DNS does more than just mapping names to IP addresses:

- Identifies web servers
- Locates mail servers
- Reveals domain name servers
- Helps attackers discover internal infrastructure
- Stores verification and security records (TXT)

---

## Types of DNS Servers

|Server Type|Description|
|---|---|
|Root Server|Handles Top-Level Domains (TLDs) like `.com`, `.org`. There are 13 root servers globally, coordinated by ICANN.|
|Authoritative Nameserver|Official DNS server for a zone, provides binding answers.|
|Non-authoritative Nameserver|Not responsible for the zone, collects information through recursion.|
|Caching DNS Server|Temporarily stores DNS responses for faster lookups.|
|Forwarding Server|Forwards DNS queries to another DNS server.|
|Resolver|Local DNS component in a system/router performing name resolution.|

---

## DNS Hierarchy Example

```
Root (.)
 └── TLD (.com)
      └── Second-Level Domain (inlanefreight.com)
           ├── www.inlanefreight.com
           ├── mail.inlanefreight.com
           └── dev.inlanefreight.com
                └── ws01.dev.inlanefreight.com
```

---

## DNS Records

DNS supports different record types for different services:

|Record|Purpose|
|---|---|
|A|Domain → IPv4 address|
|AAAA|Domain → IPv6 address|
|MX|Mail servers responsible for the domain|
|NS|Nameservers of the domain|
|TXT|Verification data, SPF, DMARC, certificate validation|
|CNAME|Alias pointing to another domain|
|PTR|Reverse lookup: IP → Domain|
|SOA|Zone authority information and administrator email|

---

### Example: SOA Record

```bash
dig soa www.inlanefreight.com
```

SOA output contains:

- Primary DNS server
- Administrator email
- Zone serial number
- Refresh/retry timing

Email format example:

```
awsdns-hostmaster.amazon.com
→ awsdns-hostmaster@amazon.com
```

---

## DNS Server Configuration (Bind9)

Most Linux DNS servers use BIND9.

Main configuration file types:

- `named.conf.local`
- `named.conf.options`
- `named.conf.log`

Example zone definition:

```conf
zone "domain.com" {
    type master;
    file "/etc/bind/db.domain.com";
    allow-update { key rndc-key; };
};
```

---

## Zone Files (Forward Lookup)

Zone files act as DNS phonebooks for a domain.

Requirements:

- Must contain exactly one SOA record
- Must contain at least one NS record

Example:

```bash
cat /etc/bind/db.domain.com
```

Zone file contains:

- NS records
- MX records
- A records
- CNAME aliases

Example entries:

```dns
server1  IN A 10.129.14.5
ftp      IN CNAME server1
www      IN CNAME server2
```

---

## Reverse Lookup Zone Files

Reverse DNS maps IP addresses back into domain names using PTR records.

Example file:

```bash
cat /etc/bind/db.10.129.14
```

Example PTR record:

```dns
5 IN PTR server1.domain.com.
```

---

## Dangerous DNS Misconfigurations

Misconfigured DNS settings can lead to major attacks:

|Option|Risk|
|---|---|
|allow-query|Controls which hosts may query the DNS server|
|allow-recursion|Controls who can send recursive requests|
|allow-transfer|Controls who can perform zone transfers (AXFR)|
|zone-statistics|Can expose internal information|

---

## DNS Footprinting and Enumeration

DNS reconnaissance helps discover:

- Subdomains
- Internal IP addresses
- Mail infrastructure
- Domain structure
- Misconfigurations

---

### 1. Nameserver Enumeration (NS Query)

```bash
dig ns inlanefreight.htb @10.129.14.128
```

Finds authoritative nameserver:

```
ns.inlanefreight.htb → 10.129.34.136
```

---

### 2. DNS Version Disclosure (CHAOS TXT)

```bash
dig CH TXT version.bind @10.129.120.85
```

If enabled, the server leaks version information:

```
"9.10.6-P1-Debian"
```

Useful for vulnerability research.

---

### 3. ANY Query

```bash
dig any inlanefreight.htb @10.129.14.128
```

May reveal:

- TXT SPF entries
- NS records
- SOA records

Not always complete.

---

## Zone Transfer Attack (AXFR)

Zone transfer copies the full DNS zone database.

Occurs over TCP port 53.

If misconfigured, attackers can dump all DNS records.

Example:

```bash
dig axfr inlanefreight.htb @10.129.14.128
```

Possible output:

```
app.inlanefreight.htb → 10.129.18.15
internal.inlanefreight.htb → 10.129.1.6
mail1.inlanefreight.htb → 10.129.18.201
```

---

### Internal Zone Transfer

```bash
dig axfr internal.inlanefreight.htb @10.129.14.128
```

May expose:

- Domain Controllers
- VPN gateways
- Workstations

Example:

```
dc1.internal → 10.129.34.16
vpn.internal → 10.129.1.6
ws1.internal → 10.129.1.34
```

---

### Why AXFR is Dangerous?

If the DNS server uses:

```conf
allow-transfer { any; };
```

Then any attacker can download the full DNS zone map.

---

## Subdomain Brute Force Enumeration

If zone transfer fails, brute forcing subdomains is effective.

Example loop:

```bash
for sub in $(cat subdomains.txt); do
  dig $sub.inlanefreight.htb @10.129.14.128
done
```

Finds:

```
mail1.inlanefreight.htb
app.inlanefreight.htb
ns.inlanefreight.htb
```

---

## Automated Tool: dnsenum

```bash
dnsenum --dnsserver 10.129.14.128 \
        --enum -p 0 -s 0 \
        -f subdomains.txt \
        inlanefreight.htb
```

Performs:

- NS lookup
- MX lookup
- Version detection
- Zone transfer attempts
- Subdomain brute forcing

---

## DNS Pentesting Checklist

|Step|Command|
|---|---|
|NS lookup|`dig ns domain.com`|
|SOA info|`dig soa domain.com`|
|MX record|`dig mx domain.com`|
|Version leak|`dig CH TXT version.bind @IP`|
|ANY query|`dig any domain.com @IP`|
|Zone transfer|`dig axfr domain.com @IP`|
|Subdomain brute force|`dnsenum`, `ffuf`, `gobuster dns`|

---

## CPTS Exam Key Takeaways

- DNS is one of the most valuable reconnaissance services
- Zone transfers are the most critical DNS misconfiguration
- TXT records often leak internal services and configurations
- Reverse DNS helps map infrastructure
- Subdomain brute forcing is essential when AXFR is blocked
