

# Web Enumeration & Hidden Resource Discovery Walkthrough

**Target Domain:** `inlanefreight.htb`

---

## Task Goal

The objective of this task is to enumerate the **inlanefreight.htb** web infrastructure in depth in order to:

1. Identify the **domain registrar IANA ID**
2. Determine the **web server software** running on the target
3. Discover **hidden virtual hosts (vHosts)**
4. Locate **hidden admin directories**
5. Extract **API keys** exposed in web content
6. Crawl discovered vHosts to extract **email addresses**
7. Identify **developer comments** revealing future configuration changes

---

## Question 1

**What is the IANA ID of the registrar of the inlanefreight.com domain?**

---

### Step 1: Perform WHOIS lookup

A WHOIS lookup is performed against the domain to identify registrar metadata.

From the WHOIS output shown in the screenshot, the following registrar information is visible:
<img width="505" height="231" alt="image" src="https://github.com/user-attachments/assets/c2343dde-2cf8-4b11-a6b8-3a92a4ce16ff" />

* **Registrar Name:** Amazon Registrar, Inc.
* **IANA ID:** **468**

The IANA ID uniquely identifies the registrar responsible for managing the domain registration.

---

### Answer

```
468
```

---

## Question 2

**What HTTP server software is powering the inlanefreight.htb site on the target system?**

---

### Step 2: Resolve the domain locally

The domain is mapped locally using `/etc/hosts` to ensure requests resolve correctly to the target IP.


```
154.57.164.69 inlanefreight.htb
```
<img width="320" height="74" alt="image" src="https://github.com/user-attachments/assets/afc10e6c-7545-4d1c-9b62-9dccd96b7620" />


This ensures all HTTP requests reach the intended server during enumeration.

---

### Step 3: Identify web server software

The following command is executed to inspect HTTP response headers:

```bash
curl -i http://inlanefreight.htb:31155
```
<img width="385" height="209" alt="image" src="https://github.com/user-attachments/assets/67dc540b-8273-4c7b-86f3-b4fcd648979f" />

---

### Observed Output

The HTTP response headers clearly reveal the web server:

```
Server: nginx/1.26.1
```

Only the **software name** is required, not the version.

---

### Answer

```
nginx
```

---

## Question 3

**What is the API key in the hidden admin directory that you have discovered on the target system?**

---

### Step 4: Brute-force virtual hosts (vHosts)

Gobuster is used in **VHOST enumeration mode** to discover hidden subdomains hosted on the same IP.

```bash
gobuster host -u http://inlanefreight.htb:31777 \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt \
  -t 100 --append-domain
```
<img width="615" height="373" alt="image" src="https://github.com/user-attachments/assets/d7b6837c-f355-40d1-8d05-8b49a5effcb2" />

---

### Result

A hidden vHost is discovered:

```
web1337.inlanefreight.htb
```

This vHost responds with HTTP 200, confirming it is valid.

---

### /etc/hosts entries

<img width="498" height="57" alt="image" src="https://github.com/user-attachments/assets/723cbe59-49d4-4467-b8b1-2cb3cd77b11c" />


### Step 5: Access hidden admin directory

A suspicious admin path is manually tested:

```bash
curl -i http://web1337.inlanefreight.htb:31777/admin_h1dd3n/
```
<img width="647" height="305" alt="image" src="https://github.com/user-attachments/assets/84657e15-b2f3-4516-8264-1e5d371e70b5" />


---

### Observed Content

The page reveals a maintenance message stating that the admin panel is temporarily unavailable.
However, within the page content, an **API key is exposed in plaintext**.

---

### Extracted API Key

```
e963d863ee0e82ba7080fbf558ca0d3f
```

---

### Answer

```
e963d863ee0e82ba7080fbf558ca0d3f
```

---
### Step 6: Brute-force virtual hosts (vHosts)

Gobuster is used in **VHOST enumeration mode** to discover hidden subdomains hosted on the same IP.

```bash
gobuster host -u http://web1337.inlanefreight.htb:31777 \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt \
  -t 100 --append-domain
```
<img width="687" height="406" alt="image" src="https://github.com/user-attachments/assets/9b81f571-cc76-4c5e-9c4e-b3d6b29677dd" />



---

### Result

A hidden vHost is discovered:

```
dev.web1337.inlanefreight.htb
```

This vHost responds with HTTP 200, confirming it is valid.
---
## Question 4

**After crawling the inlanefreight.htb domain on the target system, what is the email address you have found?**

---

### Step 7: Crawl the discovered vHost

ReconSpider is executed against the discovered vHost to extract metadata, comments, and emails.

```bash
python3 ReconSpider.py http://dev.web1337.inlanefreight.htb:31777
```
<img width="674" height="78" alt="image" src="https://github.com/user-attachments/assets/50ab8e2d-6741-449a-8a14-2fd968f15d1a" />

---

### Step 8: Analyze crawler output

After execution, ReconSpider generates a `results.json` file.

```bash
cat results.json
```
<img width="382" height="128" alt="image" src="https://github.com/user-attachments/assets/f8013e61-2e3a-4a97-8283-daf784b2dee9" />


Within the JSON output, the `emails` field contains the discovered email address.

---

### Extracted Email

```
1337testing@inlanefreight.htb
```

---

### Answer

```
1337testing@inlanefreight.htb
```

---

## Question 5

**What is the API key the inlanefreight.htb developers will be changing to?**

---

### Step 8: Inspect HTML comments

ReconSpider also extracts HTML comments embedded in the website source.

Within the `comments` section of `results.json`, the following developer note is found:

```html
<!-- Remember to change the API key to ba988b835be4aa97d068941dc852ff33 -->
```
<img width="687" height="185" alt="image" src="https://github.com/user-attachments/assets/455c2ab6-6e13-4505-9bd9-ab6f0f55949c" />

This comment clearly indicates a **future API key rotation value**.

---

### Answer

```
ba988b835be4aa97d068941dc852ff33
```

---

## Final Answers Summary

| Question | Description                       | Answer                                                                |
| -------- | --------------------------------- | --------------------------------------------------------------------- |
| 1        | Registrar IANA ID                 | 468                                                                   |
| 2        | Web server software               | nginx                                                                 |
| 3        | API key in hidden admin directory | e963d863ee0e82ba7080fbf558ca0d3f                                      |
| 4        | Email discovered during crawling  | [1337testing@inlanefreight.htb](mailto:1337testing@inlanefreight.htb) |
| 5        | Future API key value              | ba988b835be4aa97d068941dc852ff33                                      |

---


Just say the word.
