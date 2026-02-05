
# SMTP (Simple Mail Transfer Protocol)

---

## 1. What is SMTP?

SMTP (Simple Mail Transfer Protocol) is a network protocol used **only for sending emails** over an IP network.

It can operate between:

- An email client and a mail server
- One mail server and another mail server

SMTP is typically used together with:

- **POP3** or **IMAP** for receiving emails
## Understand how they work together

SMTP is used **only for sending emails**.  
For receiving emails, it is typically used together with:

- **POP3 (Post Office Protocol v3)** – Downloads emails from the server to the client, usually removing them from the server.

- **IMAP (Internet Message Access Protocol)** – Synchronizes emails between the server and multiple clients, keeping messages on the server.

In short:

- **SMTP → Send**
    
- **POP3 / IMAP → Receive**
    

SMTP cannot retrieve emails by design; it only transfers outgoing mail to the destination mail server.

---

## 2. SMTP Ports and Transport Security

### Default Ports

|Port|Usage|
|---|---|
|25|Default SMTP (server-to-server communication)|
|587|Mail submission for authenticated users|
|465|SMTP over SSL/TLS (implicit TLS)|

### Encryption Behavior

- SMTP is **unencrypted by default**
- Commands, email content, and authentication credentials are sent in **plaintext**
- Encryption is added using:
    
    - **STARTTLS** on port 25 or 587
        
    - SSL/TLS directly on port 465
        

STARTTLS upgrades an existing plaintext connection to an encrypted one after the `EHLO` command.

---

## 3. SMTP Mail Flow Architecture

```
Client (MUA)
   →
Mail Submission Agent (MSA)
   →
Mail Transfer Agent (MTA)
   →
Mail Delivery Agent (MDA)
   →
Mailbox (POP3 / IMAP)
```

### Component Roles

- **MUA (Mail User Agent)**  
    Email client such as Thunderbird, Gmail, Outlook
    
- **MSA (Mail Submission Agent)**  
    Validates sender and authentication  
    Often responsible for relay decisions
    
- **MTA (Mail Transfer Agent)**  
    Routes email between mail servers  
    Uses DNS (MX records) to find recipient servers  
    Common attack surface for open relay issues
    
- **MDA (Mail Delivery Agent)**  
    Delivers the email to the recipient mailbox
    

---

## 4. Core SMTP Limitations

### 1. No Reliable Delivery Confirmation

- SMTP does not guarantee structured delivery receipts
- Failures usually return generic error messages
- Formatting of delivery notifications is not standardized

### 2. Weak Sender Authentication

- Sender identity is not verified by default
- Enables:
    
    - Email spoofing
    - Spam campaigns
    - Phishing attacks
- Open relays worsen this issue significantly
---

## 5. SMTP Security Extensions

### Anti-Spoofing and Spam Controls

|Mechanism|Purpose|
|---|---|
|SPF|Validates allowed sending IPs|
|DKIM|Cryptographically signs emails|
|DMARC|Enforces SPF/DKIM policies|
|SMTP AUTH|Authenticates sending users|
|TLS / STARTTLS|Encrypts SMTP traffic|

---

## 6. ESMTP (Extended SMTP)

Modern SMTP implementations use **ESMTP**.

Key points:

- Initiated using the `EHLO` command
    
- Supports:
    
    - STARTTLS
    - AUTH mechanisms
    - Message size limits
    - UTF-8 email content

After STARTTLS is enabled, authentication methods such as `AUTH PLAIN` can be used securely.

---

## 7. Common SMTP Commands

|Command|Purpose|
|---|---|
|HELO|Starts SMTP session|
|EHLO|Starts ESMTP session|
|AUTH|Authenticates client|
|MAIL FROM|Specifies sender|
|RCPT TO|Specifies recipient|
|DATA|Sends email content|
|VRFY|Verifies user existence|
|EXPN|Expands mailing lists|
|RSET|Resets transaction|
|NOOP|Keeps connection alive|
|QUIT|Terminates session|

---

## 8. SMTP Enumeration with Telnet

### Establish Connection

```bash
telnet <IP> 25
```

### Identify Server Capabilities

```text
EHLO mail1
```

This reveals:

- Supported authentication methods
- STARTTLS availability
- Size limits
- VRFY/EXPN support
---

## 9. User Enumeration with VRFY

```text
VRFY username
```

### Common Behavior

- Servers often respond with:
```
252 2.0.0 username
```

- This response does **not** confirm user existence
- Many servers intentionally return false positives

Conclusion:  
VRFY results should never be trusted alone.

---

## 10. Sending Email Manually via SMTP

SMTP allows manual email construction using standard commands.

### Basic Flow

```text
MAIL FROM:<sender>
RCPT TO:<recipient>
DATA
```

Email body ends with:

```text
.
```

Use cases in testing:

- Spoofing checks
- Open relay confirmation
- Mail header analysis

---

## 11. Email Headers

Email headers contain metadata such as:

- Sender and recipient
- Mail servers traversed
- Timestamps
- Client software
- IP addresses

Headers are:

- Defined in **RFC 5322**
- Accessible to both sender and recipient
- Extremely valuable for forensics and OSINT

---

## 12. Dangerous SMTP Configuration: Open Relay

### Example Misconfiguration

```text
mynetworks = 0.0.0.0/0
```

### Impact

- Anyone can relay emails
- Enables:
    
    - Mass spam
    - Phishing campaigns
    - Email spoofing
    - Reputation blacklisting
Open relay findings are considered **high to critical severity** in penetration tests.

---

## 13. SMTP Footprinting with Nmap

### Basic Enumeration

```bash
nmap -sC -sV -p25 <IP>
```

Uses:

- `smtp-commands`
- `EHLO` probing

---

### Open Relay Detection

```bash
nmap -p25 --script smtp-open-relay <IP>
```

This script:

- Runs 16 relay tests
- Safely determines relay behavior
- Provides detailed relay vectors

---

## 14. SMTP via Web Proxy

SMTP can be accessed through HTTP proxies using:

```text
CONNECT <IP>:25 HTTP/1.0
```

Useful when:

- Direct SMTP access is blocked
- Working from restricted networks

---

## Key Pentesting Takeaways

- SMTP is used only for sending mail
- Always start enumeration with `EHLO`
- Look for STARTTLS and AUTH support
- VRFY is unreliable by design
- Open relay misconfigurations are critical
- Email headers provide valuable intelligence
