# File Transfers

There are many situations during an assessment where transferring files **to or from a target system** becomes necessary. This can include uploading tools, downloading data, or moving scripts and binaries needed for further exploitation.

To understand why file transfers matter and how restrictions affect them, let’s walk through a realistic scenario.

---

## Setting the Stage

During an engagement, we gain **remote code execution (RCE)** on a Windows IIS web server through an **unrestricted file upload vulnerability**.

### Initial Access

* We upload a **web shell** to gain basic command execution.
* From the web shell, we send ourselves a **reverse shell** to perform deeper system enumeration.

---

### Encountering Restrictions

* We try to use **PowerShell** to download `PowerUp.ps1`, a script used to find privilege escalation paths.
* PowerShell is blocked by an **Application Control Policy**.

Because of this restriction:

* Automated enumeration is not possible
* We switch to **manual enumeration**

---

### Privilege Discovery

During manual checks, we discover that our user has:

```
SeImpersonatePrivilege
```

This privilege can be abused to escalate to a higher-level user.

---

### Need for File Transfer

To exploit this privilege, we need to upload a compiled binary called **PrintSpoofer** to the target system.

However, transferring this binary turns out to be difficult.

---

## Failed File Transfer Attempts

### Using Certutil

* We try to use `certutil` to download the binary from our own **GitHub** repository.
* The organization has **strong web content filtering**.
* Access to GitHub, Dropbox, Google Drive, and similar services is blocked.

---

### Using FTP

* We set up an FTP server.
* We try to download the file using the Windows FTP client.
* Outbound traffic on **TCP port 21** is blocked by the firewall.

---

### Using SMB

* We try using **Impacket smbserver** to host the file.
* Outbound traffic on **TCP port 445 (SMB)** is allowed.
* We successfully copy the binary to the target machine.

---

### Success

* The binary is executed.
* We escalate privileges.
* We gain **administrator-level access**.

---

## Why Understanding File Transfers Matters

This scenario highlights why knowing **multiple file transfer techniques** is critical during an assessment.

File transfers can fail due to:

* Application whitelisting
* Antivirus or EDR blocking tools
* Network firewalls blocking ports
* IDS or IPS detecting unusual traffic
* Web content filtering

Understanding both **host-level controls** and **network-level restrictions** helps you choose the right transfer method.

---

## Core Concepts of File Transfers

* File transfer is a **basic feature of all operating systems**
* Many tools exist to transfer files
* Some tools are blocked, logged, or monitored
* Relying on only one method is risky

Good operators always know **multiple ways** to move files.

---

## About This Module

This module focuses on file transfer techniques that use **tools already available** on:

* Windows systems
* Linux systems

The techniques shown are **not exhaustive**, but they cover common and reliable methods.

The content in this module also serves as a **reference guide** for other modules in **HTB Academy**, since many labs require transferring files:

* To a target host
* From a target host
* Between the target and Pwnbox

---

## Hands-On Practice

The module provides:

* Target Windows machines
* Target Linux machines

You are encouraged to:

* Practice each file transfer method
* Compare their behavior
* Note which methods work in restricted environments
* Observe differences between Windows and Linux transfers

After completing this module, you should:

* Reuse these techniques in other Academy modules
* Apply them in Hack The Box labs and boxes
* Build intuition on when to use each method

---

## Conclusion

File transfer is a critical skill in penetration testing. Restrictions are common, and no single method works everywhere. By understanding how operating systems, applications, and networks affect file transfers, you can adapt quickly and continue progressing during an engagement.

Mastering these techniques ensures you are never blocked just because one transfer method fails.
