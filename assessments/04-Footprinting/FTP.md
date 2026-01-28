# FTP Enumration

## Spawn the target, gain a foothold and submit the contents of the user.txt flag.


## 🔐 VPN Connection

>Before interacting with the target, I established a secure connection to the lab using **OpenVPN**.

```
openvp academy-regular.ovpn
```
## Question.1
> Which version of the FTP server is running on the target system? Submit the entire banner as the answer.

---
# Scan the Target with Nmap

First, we scanned the target IP using Nmap to check if FTP is running:

```bash
nmap -p 21 --script "banner" 10.129.13.96
```

###  What this command does:

>`-p 21` → scans only port **21** (FTP port)

>`--script "banner"` → grabs the service banner (version info)
### Output:

<img width="516" height="215" alt="image" src="https://github.com/user-attachments/assets/cbcc32e5-3bc7-468d-8eef-1681c0e250a0" />

## Answer 
> InFreight FTP v1.1
---
## Question.2
> Enumerate the FTP server and find the flag.txt file. Submit the contents of it as the answer.

> Some FTP servers allow **anonymous login**, which can lead to sensitive file exposure. This is a common misconfiguration in real-world pentesting.

## **Anonymous Login Test**

```
ftp <target-ip>
```

Username:

```
anonymous
```

Password:

```
anything
```
> After getting login access i find the flag.txt with using **`ls`** and downloaded in my local storage with using **`get`** 
<img width="600" height="526" alt="image" src="https://github.com/user-attachments/assets/a00ec4a0-9dcf-4c59-8fa7-8e48e2160abe" />

## Answer 
> HTB{b7skjr4c76zhsds7fzhd4k3ujg7nhdjre}

