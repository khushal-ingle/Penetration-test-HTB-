
# DNS Subdomain Brute-Forcing Walkthrough

---

## Task Goal

The objective of this task is to:

1. Use **known subdomains** of `inlanefreight.com`
2. Brute-force additional subdomains using a DNS wordlist
3. Identify any **previously unknown subdomain**
4. Submit the **full subdomain name** as the answer

---

## What is happening in the screenshot

A DNS brute-force attack is performed using a common subdomain wordlist:

```text
/usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt
```
<img width="694" height="134" alt="image" src="https://github.com/user-attachments/assets/ce5660a1-03ff-4ede-8ec6-8bc6afc7d237" />

Each word from the list is appended to `inlanefreight.com` and queried to check whether it resolves to an IP address.

---

## Observed Results

The brute-force output shows several valid subdomains resolving successfully:

```text
www.inlanefreight.com
ns2.inlanefreight.com
customer.inlanefreight.com
```

A **new subdomain** is also discovered that was not included in the originally known list:

```text
my.inlanefreight.com
```

This subdomain resolves to the same IP address as the main domain, confirming it is valid.

---

## Explanation

* DNS brute-forcing helps uncover **hidden or undocumented subdomains**
* The word `my` was found in the wordlist and successfully resolved
* Since it was not part of the initially provided subdomains, it qualifies as the missing subdomain

---

## Final Answer

```
my.inlanefreight.com
```

---
