
# DNS Zone Transfer Walkthrough

---

## Task Goal

The objective of this task is to:

1. Perform a **DNS zone transfer (AXFR)** against `inlanefreight.htb`
2. Count the total number of DNS records retrieved
3. Identify the IP address for a specific hostname
4. Determine the largest IP address allocated in a given subnet range

---

## Question 1

> After performing a zone transfer for the domain inlanefreight.htb on the target system, how many DNS records are retrieved from the target system's name server?

---

### Command executed

```bash
dig axfr inlanefreight.htb @10.129.8.177
```
<img width="895" height="618" alt="image" src="https://github.com/user-attachments/assets/419bea62-b3ac-441e-8228-e7439ad9b2c9" />

---

### What is happening in this screenshot

A **full DNS zone transfer** is requested from the name server at `10.129.8.177`.

Because the DNS server is misconfigured, it allows unauthenticated AXFR requests and returns the entire zone file for `inlanefreight.htb`.

---

### Highlighted output

At the bottom of the output:

```text
XFR size: 22 records (messages 1, bytes 594)
```

---

### Explanation

The `XFR size` line indicates the total number of DNS records returned in the zone transfer.

---

### Answer

```
22
```

---

## Question 2

> Within the zone record transferred above, find the IP address for ftp.admin.inlanefreight.htb.

---

### Relevant zone entry

```text
ftp.admin.inlanefreight.htb. 604800 IN A 10.10.34.2
```

---

### Explanation

The `A` record for `ftp.admin.inlanefreight.htb` maps the hostname directly to its IPv4 address.

---

### Answer

```
10.10.34.2
```

---

## Question 3

> Within the same zone record, identify the largest IP address allocated within the 10.10.200 IP range.

---

### Relevant entries in the zone file

```text
us.inlanefreight.htb.            604800 IN A 10.10.200.5
cluster14.us.inlanefreight.htb.  604800 IN A 10.10.200.14
messagecenter.us.inlanefreight.htb. 604800 IN A 10.10.200.10
```

---

### Explanation

All IP addresses within the `10.10.200.x` range are reviewed.
Comparing the last octet values:

* 10.10.200.5
* 10.10.200.10
* 10.10.200.14

The **largest IP address** in this range is:

```
10.10.200.14
```

---

### Answer

```
10.10.200.14
```

---

## Final Answers Summary

| Question                           | Answer       |
| ---------------------------------- | ------------ |
| Total DNS Records Retrieved        | 22           |
| IP for ftp.admin.inlanefreight.htb | 10.10.34.2   |
| Largest IP in 10.10.200 Range      | 10.10.200.14 |

---
