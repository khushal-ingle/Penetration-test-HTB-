
# Automating Recon

Manual reconnaissance works, but it takes a lot of time and effort. It is also easy to miss things or make mistakes. **Automating reconnaissance** helps solve these problems by using tools to collect information quickly, accurately, and at a large scale.

Automation allows you to focus more on **analysis and decision-making** instead of repeating the same tasks again and again.

---

## Why Automate Reconnaissance?

Automation provides many benefits during web reconnaissance.

### Efficiency

Automated tools can perform repetitive tasks much faster than a human. This saves time and speeds up the recon process.

### Scalability

Automation makes it possible to scan many domains or targets at once. This is very useful during large engagements.

### Consistency

Automated tools always follow the same rules and steps. This produces reliable and repeatable results and reduces human errors.

### Comprehensive Coverage

Automated recon tools can perform many tasks, such as:

* DNS enumeration
* Subdomain discovery
* Web crawling
* Directory enumeration
* Port scanning

This ensures that important attack surfaces are not missed.

### Integration

Many recon tools can work together or integrate with other platforms. This creates a smooth workflow from reconnaissance to vulnerability testing and exploitation.

---

## Reconnaissance Frameworks

Reconnaissance frameworks combine many tools and features into one system.

### FinalRecon

A Python-based reconnaissance tool with multiple modules for:

* SSL certificate checks
* Whois lookups
* Header analysis
* Crawling and enumeration

It is modular and easy to customise.

---

### Recon-ng

A powerful Python framework with many modules for:

* DNS and subdomain enumeration
* Port scanning
* Web crawling
* Exploiting known vulnerabilities

---

### theHarvester

A Python command-line tool focused on gathering:

* Email addresses
* Subdomains
* Hostnames
* Employee names
* Open ports and banners

It uses public sources like search engines, PGP servers, and SHODAN.

---

### SpiderFoot

An OSINT automation tool that collects information such as:

* IP addresses
* Domain names
* Email addresses
* Social media profiles

It supports DNS lookups, crawling, port scanning, and more.

---

### OSINT Framework

A large collection of OSINT tools and resources covering:

* Search engines
* Social media
* Public records
* Technical intelligence

---

## FinalRecon

FinalRecon provides detailed reconnaissance information through multiple modules.

### Header Information

Reveals:

* Server details
* Technologies in use
* Possible misconfigurations

---

### Whois Lookup

Shows:

* Domain registration details
* Registrar information
* Contact details

---

### SSL Certificate Information

Checks:

* Certificate validity
* Issuer details
* SSL/TLS configuration

---

### Crawler

Extracts data from:

* HTML, CSS, JavaScript
* Internal and external links
* Images
* robots.txt and sitemap.xml
* Links inside JavaScript files
* Wayback Machine archives

---

### DNS Enumeration

Queries more than 40 DNS record types, including:

* DMARC records
* Email security records

---

### Subdomain Enumeration

Uses multiple data sources such as:

* crt.sh
* AnubisDB
* ThreatMiner
* CertSpotter
* Facebook API
* VirusTotal API
* Shodan API
* BeVigil API

---

### Directory Enumeration

Finds hidden directories and files using:

* Custom wordlists
* File extensions

---

### Wayback Machine

Fetches URLs from the last five years to identify:

* Old pages
* Removed resources
* Past vulnerabilities

---

## Installing FinalRecon

Installation is simple and quick.

```bash
git clone https://github.com/thewhiteh4t/FinalRecon.git
cd FinalRecon
pip3 install -r requirements.txt
chmod +x ./finalrecon.py
./finalrecon.py --help
```

### What These Steps Do

1. `git clone` downloads the FinalRecon tool
2. `cd FinalRecon` moves into the tool directory
3. `pip3 install -r requirements.txt` installs required Python libraries
4. `chmod +x` makes the script executable
5. `--help` shows available options

---

## FinalRecon Options Overview

| Option     | Argument | Description                 |
| ---------- | -------- | --------------------------- |
| -h, --help |          | Show help message           |
| --url      | URL      | Target URL                  |
| --headers  |          | Get header information      |
| --sslinfo  |          | Get SSL certificate details |
| --whois    |          | Perform Whois lookup        |
| --crawl    |          | Crawl target website        |
| --dns      |          | DNS enumeration             |
| --sub      |          | Subdomain enumeration       |
| --dir      |          | Directory search            |
| --wayback  |          | Get Wayback URLs            |
| --ps       |          | Fast port scan              |
| --full     |          | Full reconnaissance         |

---

## Example Usage

To collect **header information** and perform a **Whois lookup** on inlanefreight.com:

```bash
./finalrecon.py --headers --whois --url http://inlanefreight.com
```

---

## Sample Output Explained

### Target Information

* Target URL
* IP address

---

### Header Information

* Server: Apache/2.4.41 (Ubuntu)
* Content type and encoding
* WordPress REST API links

---

### Whois Information

* Domain creation and expiry dates
* Registrar details
* Name servers
* Domain protection status

---

### Completion Details

* Scan time
* Exported result file location

Example export path:

```
~/.local/share/finalrecon/dumps/
```

---

## Conclusion

Automating reconnaissance makes the recon process faster, more accurate, and more scalable. Tools like FinalRecon combine many reconnaissance techniques into one framework, allowing you to quickly gather useful information about a target.

Automation does not replace understanding—it improves it by giving you better data to analyse and act upon.


