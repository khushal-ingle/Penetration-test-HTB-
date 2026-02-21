
# DNS Enumeration Walkthrough

---

## Task Goal

The objective of this task is to perform DNS enumeration in order to:

1. Identify the **IP address** associated with a domain
2. Perform a **reverse DNS (PTR) lookup**
3. Enumerate **mail exchange (MX) records** for a domain

---

## Question 1

> Which IP address maps to inlanefreight.com?

---

### Command executed

```bash
dig inlanefreight.com
```
<img width="607" height="273" alt="image" src="https://github.com/user-attachments/assets/b24b1cf1-d9f6-46dd-8694-112791812518" />

---

### What is happening in this screenshot

A DNS query is performed to resolve the **A record** for `inlanefreight.com`.

---

### Highlighted output

```text
inlanefreight.com. 279 IN A 134.209.24.248
```

---

### Explanation

The **A record** maps a domain name to its IPv4 address.
From the DNS response, the IP address associated with `inlanefreight.com` is:

```
134.209.24.248
```

---

### Answer

```
134.209.24.248
```

---

## Question 2

> Which domain is returned when querying the PTR record for 134.209.24.248?

---

### Command executed

```bash
nslookup 134.209.24.248
```
<img width="477" height="109" alt="image" src="https://github.com/user-attachments/assets/79edb384-e50f-4b65-8b8b-f270811c6a45" />

---

### What is happening in this screenshot

A **reverse DNS lookup** is performed on the IP address to determine the associated domain name.

---

### Highlighted output

```text
name = inlanefreight.com.
```

---

### Explanation

The PTR record maps an IP address back to a domain name.
The reverse lookup confirms that the IP `134.209.24.248` resolves to:

```
inlanefreight.com.
```

(The trailing dot indicates a fully qualified domain name.)

---

### Answer

```
inlanefreight.com.
```

---

## Question 3

> What is the full domain returned when you query the mail records for facebook.com?

---

### Command executed

```bash
nslookup -type=mx facebook.com
```
<img width="629" height="202" alt="image" src="https://github.com/user-attachments/assets/0522d6ec-bbd9-4561-bcfe-ca177cc921ec" />

---

### What is happening in this screenshot

The DNS **MX records** for `facebook.com` are queried to identify the mail servers responsible for handling email.

---

### Highlighted output

```text
mail exchanger = 10 smtpin.vvv.facebook.com.
```

---

### Explanation

MX records specify the mail servers for a domain.
The response shows that `facebook.com` uses the following mail server:

```
smtpin.vvv.facebook.com.
```

---

### Answer

```
smtpin.vvv.facebook.com.
```

---

## Final Answers Summary

| Question                      | Answer                   |
| ----------------------------- | ------------------------ |
| IP for inlanefreight.com      | 134.209.24.248           |
| PTR Domain for 134.209.24.248 | inlanefreight.com.       |
| MX Domain for facebook.com    | smtpin.vvv.facebook.com. |

---

