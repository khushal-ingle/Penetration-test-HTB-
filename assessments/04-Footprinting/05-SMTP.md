
# SMTP Enumeration Walkthrough

---

## Task Goal

The objective of this task is to enumerate the SMTP service running on the target host in order to:

1. Identify the **SMTP service banner and version**
2. Enumerate the SMTP service further to find a **valid username** that exists on the system

---

## Question.1
> Enumerate the SMTP service and submit the banner, including its version as the answer

### Command executed

```bash
nmap -sC -sV -p25 10.129.30.14
```
<img width="1021" height="330" alt="image" src="https://github.com/user-attachments/assets/cf179ff0-1a4e-4b03-a703-5a6a6c2a3f27" />

---

### What is happening in this screenshot

An Nmap scan is performed against port **25**, the default SMTP port, to determine whether an SMTP service is running and to collect banner and version information.

The scan uses:

* `-sC` to execute default Nmap scripts, including SMTP-related checks
* `-sV` to identify the service version

---

### Highlighted result

```text
25/tcp open smtp
220 InFreight ESMTP v2.11
```

---

### Explanation

The SMTP server responds with its banner during the initial connection.
This banner reveals the service name, protocol, and version running on the target system.

From the response, we learn that the SMTP service is:

```
InFreight ESMTP v2.11
```

---

### Answer

```
InFreight ESMTP v2.11
```

---

## Question.2
> Enumerate the SMTP service even further and find the username that exists on the system. Submit it as the answer.

### Tool and module used

```bash
msfconsole
use auxiliary/scanner/smtp/smtp_enum
```
<img width="806" height="785" alt="image" src="https://github.com/user-attachments/assets/914c3612-4535-4117-84d1-39687877d07c" />

---

### What is happening in this screenshot

The Metasploit Framework is used to enumerate valid SMTP users on the target system.
The `smtp_enum` module attempts to verify usernames by sending SMTP commands and analyzing the server’s responses.

---

### Configuration shown

```bash
set rhost 10.129.30.14
set rport 25
set USER_FILE /home/cybervault/Downloads/Footprinting-wordlist/footprinting-wordlist.txt
```

---

### Highlighted result

```text
Banner: 220 InFreight ESMTP v2.11
Users found: robin
```

---

### Explanation

The SMTP server responds differently when a valid username is tested.
Based on these responses, Metasploit confirms that the username **`robin`** exists on the system.

---

### Answer

```
robin
```

---

## Final Answers Summary

| Question                | Answer                |
| ----------------------- | --------------------- |
| SMTP Banner and Version | InFreight ESMTP v2.11 |
| Valid SMTP Username     | robin                 |


