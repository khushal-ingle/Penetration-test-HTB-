
# IMAP & POP3 Enumeration Walkthrough

---

## Task Goal

The objective of this task is to enumerate the IMAP and POP3 services running on the target host in order to:

1. Identify the **organization name** from IMAP/POP3 SSL certificate data
2. Determine the **FQDN assigned to the IMAP and POP3 servers**
3. Enumerate the IMAP service and extract the **IMAP flag**
4. Identify the **customized POP3 server version**
5. Discover the **administrator email address**
6. Access the IMAP mailbox and retrieve the **email flag**

---

## Question.1

> Figure out the exact organization name from the IMAP/POP3 service and submit it as the answer.

### Command executed

```bash
nmap -sV -sC -p110,143,993,995 10.129.30.76
```
<img width="1294" height="567" alt="image" src="https://github.com/user-attachments/assets/ca5058c1-54ba-4549-9b85-ddf39b3ff917" />

---

### What is happening in this screenshot

An Nmap scan is performed against common IMAP and POP3 ports, both encrypted and unencrypted:

* 110 → POP3
* 143 → IMAP
* 993 → IMAPS
* 995 → POP3S

The scan uses:

* `-sV` for service and version detection
* `-sC` to run default scripts, including SSL certificate inspection

---

### Highlighted result

```text
organizationName=InlaneFreight Ltd
```

---

### Explanation

The SSL certificate exposed by the IMAP and POP3 services contains an `organizationName` field.
This field often leaks internal organizational information due to misconfigured certificates.

---

### Answer

```
InlaneFreight Ltd
```

---

## Question.2

> What is the FQDN that the IMAP and POP3 servers are assigned to?
<img width="1287" height="566" alt="image" src="https://github.com/user-attachments/assets/dcb002e6-7f86-4725-b9da-eed71cc5ef3b" />

---

### Highlighted result

```text
commonName=dev.inlanefreight.htb
```

---

### What is happening in this screenshot

The SSL certificate also reveals the **Common Name (CN)** field, which usually corresponds to the hostname assigned to the service.

---

### Explanation

Both IMAP and POP3 services are assigned to the following fully qualified domain name:

```
dev.inlanefreight.htb
```

---

### Answer

```
dev.inlanefreight.htb
```

---

## Question.3

> Enumerate the IMAP service and submit the flag as the answer. (Format: HTB{...})

### Command executed

```bash
curl -k imaps://10.129.30.76 --user robin:robin -v
```
<img width="997" height="482" alt="image" src="https://github.com/user-attachments/assets/f8bac108-c2ea-4b09-bbc5-2912a199750d" />

---

### What is happening in this screenshot

An authenticated IMAP connection is established over SSL (port 993) using previously discovered credentials (`robin:robin`).

The verbose output confirms:

* Successful TLS handshake
* Successful authentication
* IMAP capability enumeration

---

### Highlighted result

```text
HTB{roncfbw7iszerd7shni7jr2343zhrj}
```

---

### Explanation

The IMAP service responds with a flag after successful authentication and enumeration, confirming access to the service.

---

### Answer

```
HTB{roncfbw7iszerd7shni7jr2343zhrj}
```

---

## Question.4

> What is the customized version of the POP3 server?

### Command executed

```bash
nmap -sV -p110 --script banner 10.129.30.76
```
<img width="575" height="181" alt="image" src="https://github.com/user-attachments/assets/cb6d7e60-49af-4b34-9ae6-06fe16de4321" />

---

### What is happening in this screenshot

The POP3 service is specifically queried for banner information to identify any custom server version.

---

### Highlighted result

```text
InFreight POP3 v9.188
```

---

### Explanation

The POP3 server exposes a customized banner, revealing the exact POP3 server version running on the target.

---

### Answer

```
InFreight POP3 v9.188
```

---


## Question.5

> What is the admin email address?

### Commands executed

```text
LOGIN robin robin
LIST "" *
SELECT DEV.DEPARTMENT.INT
FETCH 1 ENVELOPE
```
<img width="688" height="474" alt="image" src="https://github.com/user-attachments/assets/200e5114-778f-4b4d-84c9-4caa3b42bb69" />

---

### What is happening in this screenshot

After authenticating to the IMAP server, mailbox enumeration is performed to locate internal folders.  
The `DEV.DEPARTMENT.INT` mailbox is selected, and the email **envelope** is fetched.

The IMAP `ENVELOPE` field contains structured email metadata such as:

- From address
    
- To address
    
- Subject
    
- Date
    

---

### Highlighted result

```text
("CTO" NIL "devadmin" "inlanefreight.htb")
```

---

### Explanation

From the email envelope, the sender address is revealed.  
Combining the username and domain gives the administrator email address:

```
devadmin@inlanefreight.htb
```

---

### Answer

```
devadmin@inlanefreight.htb
```

---


---

## Question.6

> Try to access the emails on the IMAP server and submit the flag as the answer. (Format: HTB{...})

### Commands executed

```text
LOGIN robin robin
LIST "" *
SELECT DEV.DEPARTMENT.INT
FETCH 1 BODY[TEXT]
```
<img width="1426" height="429" alt="image" src="https://github.com/user-attachments/assets/db48e7cd-e4df-453a-b9ef-802be4164943" />

---

### What is happening in this screenshot

An authenticated IMAP session is used to:

* List available mailboxes
* Select an internal mailbox
* Retrieve the body of an email

---

### Highlighted result

```text
HTB{983uzn8jmfgpd8jmof8c34n7zio}
```

---

### Explanation

The body of the email contains a flag, confirming successful access to internal email communications.

---

### Answer

```
HTB{983uzn8jmfgpd8jmof8c34n7zio}
```

---

## Final Answers Summary

| Question              | Answer                                                          |
| --------------------- | --------------------------------------------------------------- |
| Organization Name     | InlaneFreight Ltd                                               |
| IMAP/POP3 Server FQDN | dev.inlanefreight.htb                                           |
| IMAP Enumeration Flag | HTB{roncfbw7iszerd7shni7jr2343zhrj}                             |
| POP3 Custom Version   | InFreight POP3 v9.188                                           |
| Admin Email Address   | [devadmin@inlanefreight.htb](mailto:devadmin@inlanefreight.htb) |
| IMAP Email Flag       | HTB{983uzn8jmfgpd8jmof8c34n7zio}                                |

---

