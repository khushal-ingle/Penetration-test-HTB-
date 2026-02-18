
# Virtual Hosts (VHosts)

## 1. What Are Virtual Hosts?

Once DNS resolves a domain name to an IP address, the **web server configuration** determines how incoming requests are handled. Modern web servers such as Apache, Nginx, and IIS are designed to host **multiple websites or applications on a single server**. This capability is known as **virtual hosting**.

Virtual hosting allows a single server to:

* Serve multiple domains or subdomains
* Host separate applications with different content
* Share the same IP address and infrastructure

---

## 2. How Virtual Hosts Work

At the core of virtual hosting is the **HTTP Host header**.

When a browser sends an HTTP request, it includes a `Host` header that specifies the domain name the user wants to access. The web server uses this value to decide **which website configuration and document root** should handle the request.

### Key Concept

* Same IP address
* Different domain names
* Distinguished by the `Host` header

---

## 3. VHosts vs Subdomains

Although often confused, **subdomains and virtual hosts serve different roles**.

### Subdomains

* Extensions of a primary domain
  Example: `blog.example.com`
* Defined at the **DNS level**
* Each subdomain typically has its own DNS record
* Can point to the same IP or a different IP
* Used to organise services or sections of a website

### Virtual Hosts (VHosts)

* Defined at the **web server configuration level**
* Allow multiple sites to run on the same server
* Can be mapped to:

  * Main domains (`example.com`)
  * Subdomains (`dev.example.com`)
* Each VHost can have:

  * Separate document roots
  * Independent configurations
  * Different access rules

### Important Distinction

* DNS decides **where traffic goes**
* Virtual hosts decide **what content is served**

---

## 4. Hosts File and Non-DNS VHosts

A virtual host does **not strictly require a DNS record** to exist publicly.

By modifying the local `hosts` file, you can manually map a domain name to an IP address, bypassing DNS resolution entirely. This is commonly used for:

* Internal testing
* Development environments
* Accessing hidden or non-public virtual hosts

---

## 5. Hidden and Internal Virtual Hosts

Many websites contain:

* Internal subdomains
* Development or staging VHosts
* Admin interfaces not exposed via DNS

These hosts:

* Do not appear in DNS records
* Are still configured on the web server
* Can be discovered by testing different `Host` header values

This is where **VHost fuzzing** becomes valuable.

---

## 6. Example: Name-Based Virtual Hosting (Apache)

```apacheconf
<VirtualHost *:80>
    ServerName www.example1.com
    DocumentRoot /var/www/example1
</VirtualHost>

<VirtualHost *:80>
    ServerName www.example2.org
    DocumentRoot /var/www/example2
</VirtualHost>

<VirtualHost *:80>
    ServerName www.another-example.net
    DocumentRoot /var/www/another-example
</VirtualHost>
```

### Explanation

* All domains resolve to the same IP
* Web server checks the `Host` header
* Correct document root is served based on the domain name

---

## 7. Server VHost Lookup Process
<img width="2613" height="1632" alt="ig_virtualhosts_1" src="https://github.com/user-attachments/assets/3aaf7317-891d-41d8-8fa2-638b39e2fecf" />

### Step-by-Step Flow

1. **Browser Sends Request**
   User enters a URL in the browser.

2. **Host Header Included**
   The request contains a `Host` header with the domain name.

3. **Web Server Matches VHost**
   Server checks its virtual host configuration.

4. **Correct Content Served**
   Files are loaded from the matching document root and returned.

### Key Insight

The `Host` header acts as a **routing switch** inside the web server.

---

## 8. Types of Virtual Hosting

### 8.1 Name-Based Virtual Hosting

* Differentiation based on `Host` header
* Most common method
* No need for multiple IP addresses
* Cost-effective and scalable
* Some limitations with legacy SSL/TLS setups

### 8.2 IP-Based Virtual Hosting

* Each website has its own IP address
* No reliance on the `Host` header
* Better isolation
* Requires multiple IPs (less scalable)

### 8.3 Port-Based Virtual Hosting

* Different sites served on different ports
* Example:

  * Site A → port 80
  * Site B → port 8080
* Requires users to specify port numbers
* Less user-friendly and uncommon

---

## 9. Virtual Host Discovery

Virtual host discovery aims to identify:

* Hidden domains
* Internal subdomains
* Misconfigured or forgotten services

Manual approaches include:

* Inspecting HTTP responses
* Reverse DNS lookups
* Header analysis

However, **automated tools** are far more efficient.

---

## 10. Virtual Host Discovery Tools

### Common Tools

| Tool        | Description                    | Key Features         |
| ----------- | ------------------------------ | -------------------- |
| gobuster    | Multi-purpose brute-force tool | Fast, wordlist-based |
| feroxbuster | Rust-based brute forcer        | Recursive scanning   |
| ffuf        | Web fuzzer                     | Highly customizable  |

---

## 11. Virtual Host Fuzzing with Gobuster

Gobuster can brute-force virtual hosts by:

* Sending requests to a known IP
* Modifying the `Host` header
* Analysing server responses

### Preparation Steps

1. Identify target IP address
2. Prepare a wordlist (e.g., SecLists)
3. Determine base domain name

### Basic Command

```bash
gobuster vhost -u http://<target_ip> -w <wordlist> --append-domain
```

### Important Flags

* `-u` → Target URL or IP
* `-w` → Wordlist path
* `--append-domain` → Appends base domain to wordlist entries
* `-t` → Increase threads
* `-k` → Ignore SSL errors
* `-o` → Save output to file

### Sample Output Insight

A valid virtual host usually returns:

* HTTP status 200
* Different response size
* Unique content

---

## 12. Detection and Legal Considerations

Virtual host fuzzing:

* Generates noticeable traffic
* Can trigger IDS/WAF alerts
* Must only be performed with **explicit authorisation**

Always follow:

* Scope limitations
* Engagement rules
* Legal guidelines

---

## 13. Why Virtual Hosts Matter in Pentesting

* Hidden VHosts often expose:

  * Admin panels
  * Debug interfaces
  * Development code
* VHost discovery expands the **attack surface**
* A single missed VHost can hide critical vulnerabilities

---

