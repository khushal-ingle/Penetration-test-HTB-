
### What is a DNS Zone Transfer?

A DNS zone transfer is a process where one DNS server copies all DNS records of a domain from another DNS server.

In simple terms:

* The **primary DNS server** stores the original DNS records.
* The **secondary DNS server** acts as a backup.
* The secondary server requests a complete copy of DNS data from the primary server.

This full copy of DNS records is called a **zone transfer**.

---

### Why Zone Transfers Are Used

Zone transfers are a normal and necessary part of DNS operations. They are used to:

* Keep DNS data consistent across multiple servers
* Provide redundancy and fault tolerance
* Ensure DNS services remain available if one server fails

---

### The Security Risk

The vulnerability occurs when a DNS server allows **any client** to request a zone transfer.

If a DNS server is misconfigured, an attacker can:

* Request a zone transfer
* Download the entire DNS zone file
* Instantly obtain all subdomains and their IP addresses

This makes reconnaissance extremely easy.

---

### Information Exposed Through a Zone Transfer

An unauthorized zone transfer can reveal:

* All subdomains, including hidden or internal ones
* IP addresses mapped to each subdomain
* Mail servers (MX records)
* Authoritative name servers (NS records)
* Other sensitive DNS configurations

This information can be used for further attacks.

---

### How a Zone Transfer Works

1. **Zone Transfer Request (AXFR)**
   The secondary DNS server sends an AXFR request to the primary server.

2. **SOA Record Sent**
   The primary server responds with the Start of Authority (SOA) record, which contains zone details and a serial number.

3. **DNS Records Transmission**
   All DNS records in the zone are sent, including A, AAAA, MX, CNAME, NS, TXT, and others.

4. **Zone Transfer Completion**
   The primary server signals that all records have been sent.

5. **Acknowledgement**
   The secondary server confirms successful receipt of the zone data.

---

### Why This Matters in Web Reconnaissance

In early internet configurations, zone transfers were often left open for simplicity. This allowed anyone to download DNS data.

Modern DNS servers restrict zone transfers to trusted secondary servers, but misconfigurations still exist. Because of this, attempting a zone transfer is a valuable reconnaissance technique.

---

### Testing for Zone Transfers

You can test for a zone transfer using the `dig` tool:

```bash
dig axfr @nsztm1.digi.ninja zonetransfer.me
```

If the server is vulnerable, it will return a complete list of DNS records for the domain.

---

### Example Result

A successful zone transfer may display:

* SOA records
* TXT records
* MX records
* A and AAAA records
* Numerous subdomains

The domain `zonetransfer.me` is intentionally configured to allow zone transfers for educational purposes.

---

### Remediation

To prevent this vulnerability, DNS administrators should:

* Restrict zone transfers to trusted IP addresses
* Disable public AXFR requests
* Regularly audit DNS configurations

---

### Why Pentesters Still Attempt Zone Transfers

Even if a zone transfer fails, it can still reveal:

* Authoritative name servers
* DNS security posture
* Configuration weaknesses

For this reason, zone transfer checks are a standard part of DNS reconnaissance.

