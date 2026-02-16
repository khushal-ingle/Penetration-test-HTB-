
# Footprinting Easy Lab
## Question.
> Enumerate the server carefully and find the flag.txt file. Submit the contents of this file as the answer.
---

## Task Goal

The objective of this task is to enumerate the target server in order to:

1. Identify exposed network services
2. Access the FTP service using discovered credentials
3. Locate exposed SSH keys via FTP
4. Use the SSH private key to gain shell access
5. Locate and read the `flag.txt` file

---

## Initial Service Enumeration
Command.
```
nmap -sT -sV -sU -A -O 10.129.4.15
```
<img width="793" height="377" alt="image" src="https://github.com/user-attachments/assets/0d1ffbed-9962-4502-a03a-3f715489ad60" />


### What is shown in this screenshot

An Nmap scan output showing multiple open services on the target host.

---

### Observed services

```text
21/tcp   open  ftp      ProFTPD Server (ftp.int.inlanefreight.htb)
22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu
53/tcp   open  domain   ISC BIND
2121/tcp open  ftp      ProFTPD Server (Ceil's FTP)
623/udp  open  asf-rmcp
```

---

### Explanation

The scan reveals **two FTP services**, one of which is explicitly identified as **“Ceil’s FTP”**.
This strongly suggests a user-specific FTP service that may allow authentication or data leakage.

FTP combined with SSH often indicates potential **credential reuse or key exposure**.

---

## Discovered FTP Credentials
<img width="790" height="59" alt="image" src="https://github.com/user-attachments/assets/d42479ce-47d1-4b32-9723-81cab4d91d9f" />

### Highlighted information

```text
ceil:qwer1234
```

---

### What is happening in this screenshot

Credentials for the FTP service are discovered, likely through prior enumeration or information disclosure.

---

### Explanation

The credentials indicate:

* Username: `ceil`
* Password: `qwer1234`

These credentials can now be tested against the FTP service.

---

## FTP Login and File Enumeration
Command.
```
ftp 10.129.4.145 2121
```
### What is shown in this screenshot

An authenticated FTP session as user `ceil`, followed by directory listing.

<img width="574" height="405" alt="image" src="https://github.com/user-attachments/assets/65196298-6189-4d59-918f-c55323d8a9d8" />

---

### Relevant output

```text
drwxr-xr-x  .ssh
-rw-------  id_rsa
-rw-r--r--  id_rsa.pub
```

---

### Explanation

After logging into FTP:

* The `.ssh` directory is accessible
* The **private SSH key (`id_rsa`)** is readable and downloadable

This is a critical misconfiguration, as private SSH keys should **never** be accessible via FTP.

---

## Downloading the SSH Private Key

### Command executed in FTP

```text
get id_rsa
```
<img width="824" height="174" alt="image" src="https://github.com/user-attachments/assets/d33e7cf8-da5c-4480-aa6c-dd69ab257af9" />

---

### What is happening in this screenshot

The private SSH key is downloaded from the FTP server to the local attacker machine.

---

### Explanation

Possession of a valid private SSH key allows direct SSH authentication without knowing the account password.

---

##  Preparing the SSH Key and Logging In

### Commands executed

```bash
chmod 600 id_rsa
ssh ceil@10.129.4.2 -i id_rsa
```
<img width="688" height="124" alt="image" src="https://github.com/user-attachments/assets/44691b63-3c9d-431b-87ae-88ef38438bf2" />

---

### What is happening in this screenshot

* File permissions on the private key are fixed (`chmod 600`)
* SSH login is performed using key-based authentication

---

### Explanation

SSH enforces strict permissions on private keys.
Once corrected, the key successfully authenticates the user `ceil`, granting shell access to the system.

---

## Locating and Reading the Flag

### Commands executed

```bash
cd flag
ls
cat flag.txt
```
<img width="644" height="128" alt="image" src="https://github.com/user-attachments/assets/de4969bb-6c1e-499a-80f9-e7ada68406fc" />

---

### Highlighted output

```text
HTB{7nrzise7hednrxihskjed7nzrgkweunj47zngrhdbkjhgdfbjkc7hgj}
```

---

### Explanation

After gaining shell access:

* The `flag` directory is located
* The `flag.txt` file is read successfully
* The contents of the flag are revealed

---

## Final Answer

```
HTB{7nrzise7hednrxihskjed7nzrgkweunj47zngrhdbkjhgdfbjkc7hgj}
```

---


If you want, I can now:

* Convert this into **CPTS-style exam notes**
* Merge it into your **full service enumeration report**
* Or continue with the **next machine or service** in the same format
