
# DNS Enumeration Walkthrough

---

## Task Goal

The objective of this task is to interact directly with the target DNS server and enumerate the `inlanefreight.htb` domain in order to:

1. Identify the **authoritative DNS server (FQDN)** for the domain
    
2. Determine whether **DNS zone transfer (AXFR)** is possible
    
3. Extract sensitive DNS records exposed through zone transfers
    
4. Identify the **IPv4 address of the host `DC1`**
    
5. Discover the **fully qualified domain name (FQDN)** of the host whose IP address ends with `.203`

## Question.1
> Interact with the target DNS using its IP address and enumerate the FQDN of it for the "inlanefreight.htb" domain.
## Identifying the Authoritative DNS Server

### Command 

```bash
dig ns inlanefreight.htb @10.129.27.233
```
<img width="575" height="505" alt="image" src="https://github.com/user-attachments/assets/9184a0df-da1d-4610-b9c1-5c3034ea0ad5" />

### What is happening 

The DNS server is queried directly by IP address (`10.129.27.233`) to determine which nameserver is authoritative for the domain `inlanefreight.htb`.

By bypassing the local resolver and querying the target DNS server directly, we ensure that the response comes from the authoritative source.

### Highlighted result
> The NS record reveals that the authoritative DNS server for the domain is:
```text
ns.inlanefreight.htb.
```
This information is required before attempting advanced DNS enumeration such as zone transfers.


## Question.2
>  Identify if its possible to perform a zone transfer and submit the TXT record as the answer. (Format: HTB{...})

## Attempting a Zone Transfer on the Main Domain

### Command 

```bash
dig axfr inlanefreight.htb @10.129.27.233
```
<img width="1761" height="403" alt="image" src="https://github.com/user-attachments/assets/12653640-d730-43eb-83e5-e775a0597452" />

### What is happening 

A DNS zone transfer (AXFR) is attempted against the authoritative DNS server.  
A successful zone transfer means the server is misconfigured and allows disclosure of all DNS records for the domain.

### Explanation

The DNS server allows zone transfers.  
A TXT record inside the zone contains a flag, confirming the misconfiguration.

Zone transfers can leak internal infrastructure details such as domain controllers, mail servers, and development systems.

### Answer

```
HTB{DN5_z0N3_7r4N5F3r_iskdufhcnlu34}
```

---
## Question.3
> What is the IPv4 address of the hostname DC1?
> 
## Zone Transfer on the Internal Subdomain

### Command 

```bash
dig axfr internal.inlanefreight.htb @10.129.27.233
```
<img width="1764" height="473" alt="image" src="https://github.com/user-attachments/assets/147970b5-ae55-43a0-98be-633bbaf772b5" />

### What is happening 

After confirming that zone transfers are allowed, the same technique is applied to the internal subdomain.  
Internal DNS zones are often less protected and may expose sensitive hosts.

### Answer

```text
internal.inlanefreight.htb. IN TXT "HTB{DNS_Z0N3_TR4NSF3R_iskdufhcnlu34}"
```

### Explanation

The internal DNS zone is fully exposed.  
The TXT record confirms successful enumeration of internal DNS data.

Additional records reveal internal systems such as domain controllers and workstations.

---

## Question.4 
> What is the IPv4 address of the hostname DC1?

## Identifying the IPv4 Address of DC1

### Relevant record shown
<img width="983" height="467" alt="image" src="https://github.com/user-attachments/assets/bfcd80bc-8649-4f08-9b6b-287d791af2d1" />


```text
dc1.internal.inlanefreight.htb. IN A 10.129.34.16
```

### What is happening 

From the zone transfer output, the A record for the hostname `DC1` is identified.

### Explanation

The hostname `dc1.internal.inlanefreight.htb` resolves to the IPv4 address:10.129.34.16

This host is likely a Domain Controller, making it a high-value target for further attacks.

### Answer

```
10.129.34.16
```

---

## Question.5
> What is the FQDN of the host where the last octet ends with "x.x.x.203"?

## Finding the Hostname Ending with IP `.203`

## Subdomain Enumeration 

### Command used

```bash
for sub in $(cat /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt); do
  dig $sub.inlanefreight.htb @10.129.27.233 |
  grep -v ';\|SOA' |
  sed -r '/^\s*$/d' |
  grep $sub |
  tee -a subdomains.txt
done
```

### Explanation

This command brute-forces subdomains by querying the DNS server directly and filtering valid responses.  
Discovered subdomains are saved to `subdomains.txt` for later analysis.

## We will use dnsenum tool
### Command 

```bash
dnsenum --dnsserver 10.129.27.233 --enum -p 0 -s 0 \
-o subdomains.txt \
-f /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt \
dev.inlanefreight.htb
```
<img width="1224" height="697" alt="image" src="https://github.com/user-attachments/assets/d1daae9c-d4bf-4bb0-9842-d1d1398e5630" />

### What is happening 

DNS brute-force enumeration is performed against the `dev.inlanefreight.htb` subdomain using a wordlist.

This technique is used to identify hosts that may not be obvious or clearly listed during zone transfers.

### Highlighted result

```text
win2k.dev.inlanefreight.htb. IN A 10.12.3.203
```

### Explanation

The hostname associated with an IP address ending in `.203` is:

## Answer
```
win2k.dev.inlanefreight.htb
```

---

## Final Answers Summary

|Question|Answer|
|---|---|
|Authoritative DNS FQDN|ns.inlanefreight.htb|
|Zone Transfer TXT Record|HTB{DN5_z0N3_7r4N5F3r_iskdufhcnlu34}|
|DC1 IPv4 Address|10.129.34.16|
|Host with IP ending `.203`|win2k.dev.inlanefreight.htb|
