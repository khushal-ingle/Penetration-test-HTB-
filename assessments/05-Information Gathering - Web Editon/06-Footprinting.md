
# Web Fingerprinting & vHost Enumeration 

---

## Task Goal

The objective of this task is to enumerate multiple **virtual hosts (vHosts)** hosted on the same target system in order to:

1. Identify the **Apache version** running on `app.inlanefreight.local`
2. Identify the **CMS** used by `app.inlanefreight.local`
3. Identify the **operating system** running the web server for `dev.inlanefreight.local`

---

## Step 1: Configure Local Host Resolution

### Screenshot: `/etc/hosts`

### Command used

```bash
nano /etc/hosts
```
<img width="549" height="270" alt="image" src="https://github.com/user-attachments/assets/d7facb63-4fc0-439b-9460-4e8fba35e437" />

### What is happening here

The domain `app.inlanefreight.local` is not publicly resolvable.
To interact with it through a browser and tools like Nikto, it must be manually mapped to the target IP.

### Relevant entry added

```text
10.129.13.166 app.inlanefreight.local
```

This ensures that all requests to `app.inlanefreight.local` resolve correctly.

---

## Step 2: Apache Version Enumeration with Nikto

### Screenshot: Nikto scan output

### Command executed

```bash
nikto -h 10.129.13.166
```
<img width="457" height="222" alt="image" src="https://github.com/user-attachments/assets/4751cd58-8871-4e24-9740-f874d406abad" />

### What is happening here

Nikto performs a web server scan to:

* Identify the web server software
* Detect server headers
* Reveal misconfigurations and version information

---

### Highlighted result

```text
Server: Apache/2.4.41 (Ubuntu)
```

### Explanation

The HTTP response header reveals:

* Web server: Apache
* Version: 2.4.41
* Underlying OS: Ubuntu

---

### Answer

```
2.4.41
```

---

## Step 3: CMS Identification on app.inlanefreight.local

### Screenshot: Wappalyzer output (app.inlanefreight.local)

### Tool used

**Wappalyzer (Browser Extension)**

### What is happening here

Wappalyzer inspects:

* HTTP headers
* JavaScript files
* HTML structure

to identify technologies used by the web application.

---

### Highlighted result

```text
CMS: Joomla
```

### Explanation

Wappalyzer confirms that the application running on `app.inlanefreight.local` is built using **Joomla CMS**.

---

### Answer

```
Joomla
```

---

## Step 4: Operating System Identification for dev.inlanefreight.local

### Screenshot: Wappalyzer output (dev.inlanefreight.local)

### What is happening here

The `dev.inlanefreight.local` vHost is accessed in the browser, and Wappalyzer is again used to fingerprint the backend environment.

---

### Highlighted result

```text
Operating System: Ubuntu
```

### Explanation

The web server headers and detected technologies confirm that the `dev.inlanefreight.local` web server is running on **Ubuntu**.

---

### Answer

```
Ubuntu
```

---

## Final Answers Summary

| Question                                  | Answer |
| ----------------------------------------- | ------ |
| Apache version on app.inlanefreight.local | 2.4.41 |
| CMS used on app.inlanefreight.local       | Joomla |
| OS running dev.inlanefreight.local        | Ubuntu |

---

## Key Takeaways

* Manual `/etc/hosts` entries are essential for internal vHost enumeration
* Nikto is reliable for **Apache version fingerprinting**
* Wappalyzer quickly identifies **CMS and OS details**
* Multiple vHosts on the same IP can expose different technologies

---
