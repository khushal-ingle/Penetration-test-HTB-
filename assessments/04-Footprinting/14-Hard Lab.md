
# Footprinting Hard Lab 
## Question
>Enumerate the server carefully and find the username "HTB" and its password. Then, submit HTB's password as the answer.

---

## Task Goal

The objective of this task is to enumerate the target server in order to:

1. Identify exposed services
    
2. Extract credentials via SNMP
    
3. Access the IMAP service and retrieve sensitive data
    
4. Abuse leaked SSH private keys
    
5. Access MySQL and extract the password for user **HTB**
    

---

## 1 — Full Port and Service Enumeration

### Command executed

```bash
nmap -sT -sV -sU -A -O 10.129.6.155
```
<img width="751" height="867" alt="image" src="https://github.com/user-attachments/assets/208a94e7-dd48-408c-b173-1d92454e8123" />

---

### What is happening

A comprehensive Nmap scan is performed to identify:

- Open TCP and UDP ports
    
- Running services and versions
    
- OS information
    

---

### Key services identified

```text
22/tcp   SSH
110/tcp  POP3
143/tcp  IMAP
993/tcp  IMAPS
995/tcp  POP3S
161/udp  SNMP
```

---

### Explanation

The presence of **SNMP**, **IMAP/IMAPS**, and **SSH** suggests a strong possibility of credential leakage and service chaining.

---

## 2 — SNMP Enumeration with Community String

### Command executed

```bash
snmpwalk -c backup -v1 10.129.6.155
```
<img width="927" height="166" alt="image" src="https://github.com/user-attachments/assets/7ba91aea-d4fc-464f-9c76-19aff50c0c0a" />
<img width="666" height="41" alt="image" src="https://github.com/user-attachments/assets/78dad01e-c083-4398-960d-17358cbb92fc" />

---

### What is happening

SNMP is queried using the **community string `backup`**, which successfully authenticates.

---

### Sensitive information discovered

```text
Admin <tech@inlanefreight.htb>
NIXHARD
Inlanefreight
```

---

### Explanation

SNMP leaks:

- Hostname: `NIXHARD`
    
- Organization: `Inlanefreight`
    
- Admin email: `tech@inlanefreight.htb`
    

This confirms misconfigured SNMP exposing sensitive metadata.

---

## Discovering a Custom Script via SNMP

### Highlighted OIDs

```text
/opt/tom-recovery.sh
tom NMds732Js2761
```

---

### Explanation

SNMP reveals:

- A recovery script location
    
- A username (`tom`)
    
- A password (`NMds732Js2761`)
    

This is a critical credential disclosure.

---

## 3 — Connecting to IMAPS

### Command executed

```bash
openssl s_client -connect 10.129.6.246:imaps
```
<img width="403" height="185" alt="image" src="https://github.com/user-attachments/assets/4be1bf34-f79b-44ac-91ec-617fd56caa63" />

---

### What is happening

A TLS connection is established to the IMAPS service to allow manual IMAP interaction.

Certificate warnings are ignored due to self-signed certs.

---

## 4 — Authenticating to IMAP

### Commands executed

```text
LOGIN tom NMds732Js2761
```
<img width="653" height="108" alt="image" src="https://github.com/user-attachments/assets/bb52adba-74b2-4247-b266-e0384bc116a4" />

---

### Explanation

Authentication is successful, confirming the credentials obtained via SNMP are valid.

---

## 5 — Enumerating Mailboxes

### Command executed

```text
LIST "" *
```
<img width="408" height="118" alt="image" src="https://github.com/user-attachments/assets/1fd86d79-3ba1-46fa-9042-249b965efe09" />

---

### Output

```text
Notes
Meetings
Important
INBOX
```

---

### Explanation

Multiple mailboxes are available. The **Important** mailbox is likely to contain sensitive information.

---

## 6 — Selecting the INBOX

### Command executed

```text
SELECT INBOX
```
<img width="680" height="151" alt="image" src="https://github.com/user-attachments/assets/b527cdd2-195b-416d-8c23-0a1eecd589f4" />

---

### Explanation

The mailbox is selected and message metadata is loaded.

---

## 7 — Fetching Email Contents

### Command executed

```text
FETCH 1 BODY[TEXT]
```
<img width="570" height="272" alt="image" src="https://github.com/user-attachments/assets/9b541bd0-5e0f-4827-ac6a-6089d70f9b3c" />

---

### What is happening

The body of the first email is retrieved.

---

### Critical content found

```text
BEGIN OPENSSH PRIVATE KEY
```

---

### Explanation

The email contains an **SSH private key**, which can be used for authentication.

---

## 8 — Saving and Preparing SSH Key

### Commands executed

```bash
vim tom_id_rsa
chmod 600 tom_id_rsa
```
<img width="354" height="105" alt="image" src="https://github.com/user-attachments/assets/1f6e56a8-7a29-4675-bdda-8a56572debea" />

---

### Explanation

The SSH private key is saved locally and permissions are fixed to allow SSH usage.

---

## 9 — SSH Access Using Private Key

### Command executed

```bash
ssh tom@10.129.6.246 -i tom_id_rsa
```
<img width="393" height="44" alt="image" src="https://github.com/user-attachments/assets/dffb7765-ff45-43b7-a258-9d7765d97b61" />

---

### Explanation

SSH authentication using the leaked private key is successful, granting shell access as user `tom`.

---

## 10 — MySQL Login Using Tom’s Credentials

### Command executed

```bash
mysql -u tom -pNMds732Js2761
```
<img width="612" height="309" alt="image" src="https://github.com/user-attachments/assets/f077f6e8-4742-4248-add4-1a31dab86cd9" />

---

### Explanation

Tom’s password also works for MySQL, indicating password reuse.

---

## 11 — Enumerating MySQL Databases

### Command executed

```sql
show databases;
```
<img width="547" height="305" alt="image" src="https://github.com/user-attachments/assets/cd90dc91-274f-46e9-924d-384b2c20f008" />

---

### Output

```text
users
mysql
information_schema
performance_schema
sys
```

---

### Explanation

The `users` database is non-default and likely contains application credentials.

---

## 12 — Querying User HTB

### Commands executed

```sql
use users;
select * from users where username='HTB';
```
<img width="458" height="136" alt="image" src="https://github.com/user-attachments/assets/fff335d7-c121-40b1-95be-7e8ab998c07a" />

---

### Highlighted output

```text
username: HTB
password: cr3n4o7rzse7rzhnckhssncif7ds
```

---

### Explanation

The password for user **HTB** is stored in cleartext in the database.

---

## Final Answer

```
cr3n4o7rzse7rzhnckhssncif7ds
```

---

## Attack Chain Summary

```text
SNMP → Credential leak
→ IMAP login
→ SSH private key disclosure
→ SSH access
→ MySQL access
→ Credential extraction
```

---
