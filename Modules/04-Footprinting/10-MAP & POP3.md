## MAP & POP3 

---

## 1. What is IMAP?

**IMAP (Internet Message Access Protocol)** allows users to access and manage emails directly on the mail server.

### Key Characteristics

- Emails remain on the server
    
- Supports folders and subfolders
    
- Multiple clients can access the same mailbox simultaneously
    
- Provides real-time synchronization
    
- Works like a network file system for emails
    

### Why IMAP is Powerful

- Same mailbox view across multiple devices
    
- Sent emails can be stored in IMAP folders
    
- Ideal for enterprise and organizational environments
    

### Limitations

- Requires an active server connection
    
- Offline mode works only via cached copies
    
- Higher server storage requirements
    

---

## 2. What is POP3?

**POP3 (Post Office Protocol v3)** is a simple email retrieval protocol.

### Key Characteristics

- Downloads emails to the client
    
- Emails are usually deleted from the server
    
- No folder structure
    
- One-way interaction
    

### POP3 Supports Only

- Listing emails
    
- Retrieving emails
    
- Deleting emails
    

### POP3 Limitations

- No synchronization
    
- No folder support
    
- Poor multi-device usability
    
- Largely obsolete in modern environments
    

---

## 3. IMAP vs POP3 Comparison

|Feature|IMAP|POP3|
|---|---|---|
|Emails stored on server|Yes|No|
|Folder support|Yes|No|
|Multi-device sync|Yes|No|
|Offline support|Partial|Yes|
|Complexity|High|Low|

---

## 4. Ports and Encryption

### Default Ports

|Protocol|Plaintext|SSL/TLS|
|---|---|---|
|IMAP|143|993|
|POP3|110|995|

Without TLS, credentials are transmitted in plaintext.

---

## 5. IMAP Authentication Flow

1. Client connects to the server
    
2. Server sends a banner
    
3. Client authenticates using username and password
    
4. Mailbox access is granted
    
5. Client issues commands
    

---

## 6. IMAP Commands

|Command|Description|
|---|---|
|LOGIN username password|Authenticate|
|LIST "" *|List all mailboxes|
|CREATE "INBOX"|Create a mailbox|
|DELETE "INBOX"|Delete a mailbox|
|RENAME old new|Rename a mailbox|
|LSUB "" *|List subscribed mailboxes|
|SELECT INBOX|Select mailbox|
|UNSELECT|Exit mailbox|
|FETCH id all|Fetch email data|
|CLOSE|Remove deleted emails|
|LOGOUT|Close connection|

---

## 7. POP3 Commands

|Command|Description|
|---|---|
|USER username|Identify user|
|PASS password|Authenticate|
|STAT|Mailbox statistics|
|LIST|List emails|
|RETR id|Retrieve email|
|DELE id|Delete email|
|CAPA|Server capabilities|
|RSET|Reset session|
|QUIT|Close connection|

---

## 8. Dangerous Misconfigurations (Dovecot)

Misconfigured email servers can expose sensitive data.

|Setting|Risk|
|---|---|
|auth_debug|Logs authentication process|
|auth_debug_passwords|Logs plaintext passwords|
|auth_verbose|Logs failed login attempts|
|auth_verbose_passwords|Logs passwords used|
|auth_anonymous_username|Allows anonymous login|

In worst cases, attackers can read all emails.

---

## 9. Footprinting IMAP and POP3 Services

### Nmap Scan

```bash
sudo nmap -sV -sC -p110,143,993,995 <IP>
```

### Information Gained

- Mail server software
    
- Supported authentication methods
    
- SSL certificate details
    
- Domain and organization name
    

---

## 10. Interacting with IMAP Using cURL

```bash
curl -k imaps://<IP> --user username:password
```

### Verbose Output

```bash
curl -k imaps://<IP> --user username:password -v
```

Provides TLS details, server banner, and authentication flow.

---

## 11. TLS Interaction Using OpenSSL

### POP3 over TLS

```bash
openssl s_client -connect <IP>:pop3s
```

### IMAP over TLS

```bash
openssl s_client -connect <IP>:imaps
```

After connection:

```text
USER username
PASS password
```

or

```text
LOGIN username password
```

---

## 12. Attack Scenario Example

- SMTP enumeration reveals user credentials
    
- Password reuse allows IMAP login
    
- Attacker reads confidential emails
    
- Attacker can impersonate the user
    

Email access often leads to full account compromise.

---

## 13. Key Takeaways

- IMAP provides extensive functionality but increases attack surface
    
- POP3 is simple but limited
    
- TLS must be enforced
    
- Password reuse is critical risk
    
- Email servers often expose high-value information
    
