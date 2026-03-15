
# File Retrieval & Windows Upload Walkthrough

---

# Task Goal

The objective of this task is to perform two different actions on the target systems:

1. **Retrieve a file from a web server** using `wget` from the Pwnbox.
2. **Upload a file to a Windows system**, extract it, execute a hashing program, and obtain the generated hash.

---

# Question 1

**Download the file `flag.txt` from the web root using wget from the Pwnbox. Submit the contents of the file as your answer.**

---

## Step 1 — Download the file using wget

### Command executed

```bash
wget 10.129.201.55/flag.txt
```

---

## What is happening in this step

The `wget` command is used to download files from web servers via HTTP.

When the command runs:

* The Pwnbox sends an HTTP request to the target server at `10.129.201.55`.
* The server responds with **HTTP 200 OK**, meaning the file is accessible.
* The file `flag.txt` is downloaded and saved locally.

---

## Observed output

```
HTTP request sent, awaiting response... 200 OK
Length: 32 [text/plain]
Saving to: ‘flag.txt’
```

The output confirms:

* The file exists on the web server.
* The server allowed direct access.
* The file has been downloaded successfully.

---

## Step 2 — Display the file contents

### Command executed

```bash
cat flag.txt
```

---

## What is happening

The `cat` command reads and prints the contents of the downloaded file.

---

## Extracted content

```
b1a4ca918282fcd96004565521944a3b
```

---

## Answer

```
b1a4ca918282fcd96004565521944a3b
```

---

# Question 2

**Upload the attached file `upload_win.zip` to the Windows target, extract it, and run `hasher upload_win.txt`. Submit the generated hash.**

You are provided with the following credentials to access the Windows machine:

```
Username: htb-student
Password: HTB_@cademy_stdnt!
```

---

# Step 1 — Create an SMB share on Pwnbox

To transfer files to the Windows system, an SMB share is created using **Impacket**.

### Command executed

```bash
impacket-smbserver pwn .
```

---

## What is happening

This command creates a network share named **pwn** from the current directory.

The Windows system will now be able to access files from the Pwnbox using the SMB protocol.

---

# Step 2 — Connect to the Windows system using RDP

### Command executed

```bash
xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:10.129.6.119
```

---

## What is happening

This command connects to the remote Windows desktop using **Remote Desktop Protocol (RDP)**.

Once connected:

* You gain access to the Windows desktop environment.
* You can execute PowerShell commands and transfer files.

---

# Step 3 — Copy the file from the SMB share

Inside the Windows PowerShell session, the file is copied from the SMB share.

### Command executed

```powershell
copy \\10.10.15.176\pwn\upload_win.txt C:\Users\htb-student\upload_win.txt
```

---

## What is happening

* Windows accesses the SMB share hosted on the Pwnbox.
* The file `upload_win.txt` is copied to the local user directory.
* The directory listing confirms that the file is now present.

---

# Step 4 — Execute the hashing program

After extracting the uploaded archive, the provided hashing program is executed.

### Command executed

```powershell
.\hasher.exe upload_win.txt
```

---

## What is happening

The program:

1. Reads the contents of `upload_win.txt`.
2. Computes a cryptographic hash of the file.
3. Displays the resulting hash in the PowerShell console.

---

## Generated hash

```
f458303ea783c224c6b4e7ef7f17eb9d
```

---

# Answer

```
f458303ea783c224c6b4e7ef7f17eb9d
```

---

# Final Answers

| Question                             | Answer                           |
| ------------------------------------ | -------------------------------- |
| Contents of `flag.txt`               | b1a4ca918282fcd96004565521944a3b |
| Hash generated from `upload_win.txt` | f458303ea783c224c6b4e7ef7f17eb9d |

---
