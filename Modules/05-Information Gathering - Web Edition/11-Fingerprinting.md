
# Fingerprinting

Fingerprinting focuses on extracting technical details about the technologies powering a website or web application. Similar to how a fingerprint uniquely identifies a person, the digital signatures of web servers, operating systems, content management systems, frameworks, and security mechanisms can reveal critical information about a target’s infrastructure and potential security weaknesses. This information allows attackers or penetration testers to tailor attacks that are specific to the identified technologies.

Fingerprinting is a fundamental component of web reconnaissance and plays a crucial role in understanding a target before launching further assessments or attacks.

---

## Importance of Fingerprinting

Fingerprinting serves as a cornerstone of web reconnaissance for several key reasons:

### Targeted Attacks

By identifying the exact technologies and versions in use, attackers can focus on exploits and vulnerabilities that are known to affect those specific systems. This significantly increases the likelihood of a successful compromise.

### Identifying Misconfigurations

Fingerprinting can reveal outdated software, missing security patches, default configurations, or insecure settings that may not be visible through surface-level analysis.

### Prioritising Targets

When multiple systems are available for assessment, fingerprinting helps determine which targets are more vulnerable or more valuable, allowing attackers or testers to allocate their efforts efficiently.

### Building a Comprehensive Profile

Fingerprinting data, when combined with other reconnaissance results such as DNS enumeration or directory discovery, creates a complete picture of the target’s infrastructure and security posture. This holistic view helps identify potential attack vectors.

---

## Fingerprinting Techniques

Several techniques are commonly used to fingerprint web servers and application technologies.

### Banner Grabbing

Banner grabbing involves collecting and analysing banners returned by web servers or services. These banners often reveal information such as the server software, version numbers, and operating system details.

### Analysing HTTP Headers

HTTP headers are sent with every client-server interaction and contain valuable information.
Common headers of interest include:

* `Server`, which often discloses the web server software
* `X-Powered-By`, which may reveal backend languages or frameworks
* `X-Redirect-By`, which can indicate CMS behaviour

### Probing for Specific Responses

Specially crafted requests can trigger unique responses from a server. Certain error messages, redirects, or behaviours are characteristic of specific web servers, CMS platforms, or frameworks.

### Analysing Page Content

The structure and content of a web page can provide strong indicators of underlying technologies. Examples include CMS-specific paths, API endpoints, JavaScript libraries, or copyright notices.

---

## Fingerprinting Tools

A variety of tools automate fingerprinting by combining multiple techniques to identify web technologies.

| Tool       | Description                          | Features                                                    |
| ---------- | ------------------------------------ | ----------------------------------------------------------- |
| Wappalyzer | Browser extension and online service | Identifies CMSs, frameworks, analytics tools, and libraries |
| BuiltWith  | Website technology profiler          | Provides detailed reports on technology stacks              |
| WhatWeb    | Command-line fingerprinting tool     | Uses signatures to identify web technologies                |
| Nmap       | Network and service scanner          | Performs service and OS fingerprinting using NSE scripts    |
| Netcraft   | Web security intelligence service    | Reports on hosting, technologies, and security posture      |
| wafw00f    | WAF detection tool                   | Identifies Web Application Firewalls and their types        |

---

## Fingerprinting inlanefreight.com

The following section applies fingerprinting techniques to the target domain inlanefreight.com using both manual and automated methods.

---

## Banner Grabbing Using curl

The first step is to retrieve HTTP headers from the server using the `curl` command with the `-I` option, which fetches headers only.

```bash
curl -I inlanefreight.com
```

### Output Analysis

```
HTTP/1.1 301 Moved Permanently
Date: Fri, 31 May 2024 12:07:44 GMT
Server: Apache/2.4.41 (Ubuntu)
Location: https://inlanefreight.com/
Content-Type: text/html; charset=iso-8859-1
```

This response reveals:

* The web server is Apache version 2.4.41
* The operating system is Ubuntu
* HTTP traffic is redirected to HTTPS

---

## Analysing HTTPS Redirects

Since the server redirects to HTTPS, the next step is to retrieve headers from the HTTPS endpoint.

```bash
curl -I https://inlanefreight.com
```

### Output Analysis

```
HTTP/1.1 301 Moved Permanently
Server: Apache/2.4.41 (Ubuntu)
X-Redirect-By: WordPress
Location: https://www.inlanefreight.com/
```

The `X-Redirect-By: WordPress` header indicates that WordPress is responsible for handling the redirect, strongly suggesting the use of a WordPress CMS.

---

## Final Header Analysis

The final destination is accessed to collect additional headers.

```bash
curl -I https://www.inlanefreight.com
```

### Output Analysis

```
HTTP/1.1 200 OK
Server: Apache/2.4.41 (Ubuntu)
Link: <https://www.inlanefreight.com/index.php/wp-json/>; rel="https://api.w.org/"
Content-Type: text/html; charset=UTF-8
```

The presence of `wp-json` confirms the WordPress REST API, further validating the use of WordPress.

---

## WAF Detection Using wafw00f

Before proceeding with deeper scanning, it is important to check for the presence of a Web Application Firewall, as it may block or interfere with reconnaissance attempts.

### Installation

```bash
pip3 install git+https://github.com/EnableSecurity/wafw00f
```

### Detection

```bash
wafw00f inlanefreight.com
```

### Result

```
The site https://inlanefreight.com is behind Wordfence (Defiant) WAF.
```

This indicates that the site is protected by the Wordfence Web Application Firewall, which may filter malicious requests and reconnaissance probes.

---

## Fingerprinting with Nikto

Nikto is an open-source web server scanner that also provides fingerprinting and configuration analysis.

### Installation

```bash
sudo apt update && sudo apt install -y perl
git clone https://github.com/sullo/nikto
cd nikto/program
chmod +x nikto.pl
```

### Fingerprinting Scan

```bash
nikto -h inlanefreight.com -Tuning b
```

The `-Tuning b` option limits the scan to software identification checks.

---

## Nikto Scan Results and Analysis

Key findings from the Nikto scan include:

* Multiple IP addresses (IPv4 and IPv6)
* Apache/2.4.41 running on Ubuntu
* WordPress installation detected
* `/wp-login.php` accessible
* `license.txt` file exposed
* Missing security headers such as:

  * Strict-Transport-Security
  * X-Content-Type-Options
* Cookie created without the HttpOnly flag
* Potential BREACH attack exposure due to compression
* Deprecated security headers in use
* Apache version identified as outdated

---

## Final Fingerprinting Summary

* Web Server: Apache 2.4.41 (Ubuntu)
* CMS: WordPress
* WAF: Wordfence
* IP Stack: IPv4 and IPv6
* Security Issues: Missing headers, outdated software, exposed WordPress endpoints
* Attack Surface: WordPress login, REST API, server misconfigurations

---

## Conclusion

Fingerprinting alone revealed critical information about the target’s technology stack, security mechanisms, and potential weaknesses. This phase of reconnaissance provides a solid foundation for further enumeration, vulnerability assessment, or exploitation attempts and demonstrates why fingerprinting is an essential step in any penetration testing methodology.

