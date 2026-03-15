# Linux File Upload Walkthrough

---

# Task Goal

The objective of this task is to perform two different operations:

1. **Download a file from a web server using Python from the Pwnbox**
2. **Upload a file to a Linux target system, extract it, execute a hashing program, and retrieve the generated hash**

These steps simulate common penetration testing activities such as **file retrieval from exposed web roots** and **file transfer to compromised systems**.

---

# Question 1

**Download the file `flag.txt` from the web root using Python from the Pwnbox. Submit the contents of the file as your answer.**

---

## Step 1 — Download the file from the web server

A web server is hosting the file `flag.txt` at the following address:

```
http://10.129.234.168/flag.txt
```

To retrieve this file, the `wget` command is executed from the Pwnbox.

### Command executed

```bash
wget 10.129.234.168/flag.txt
```

---

## What is happening in this step

The `wget` tool sends an HTTP request to the remote web server to download the file.

The output confirms:

* A successful connection to the server
* The server returned **HTTP 200 OK**
* The file was downloaded successfully

---

## Observed output

```
HTTP request sent, awaiting response... 200 OK
Length: 33 [text/plain]
Saving to: ‘flag.txt’
```

This confirms that the file exists in the web root and is accessible without authentication.

---

## Step 2 — Display the file contents

After the file is downloaded, the contents can be displayed.

### Command executed

```bash
cat flag.txt
```

---

## Extracted file contents

```
5d21cf3da9c0ccb94f709e2559f3ea50
```

---

## Answer

```
5d21cf3da9c0ccb94f709e2559f3ea50
```

---

# Question 2

**Upload the attached file `upload_nix.zip` to the target system, SSH into the machine, extract the file, and run `hasher <extracted file>`. Submit the generated hash as your answer.**

The system can be accessed using the provided SSH credentials:

```
Username: htb-student
Password: HTB_@cademy_stdnt!
```

---

# Step 1 — Host the file using a Python web server

To transfer the file to the target Linux system, a simple HTTP server is started on the Pwnbox.

### Command executed

```bash
python -m http.server 80
```

---

## What is happening

This command starts a lightweight HTTP server that:

* Serves files from the current directory
* Allows the target machine to download files directly

The server listens on:

```
0.0.0.0:80
```

which means it is accessible to all network interfaces.

---

# Step 2 — Download the file from the target machine

After connecting to the target system via SSH, the file is downloaded using `wget`.

### Command executed on the target

```bash
wget http://10.10.15.176/upload_nix.txt
```

---

## What is happening

The Linux target system connects to the HTTP server running on the Pwnbox and downloads the file.

The output confirms:

```
upload_nix.txt saved [32/32]
```

which indicates that the file transfer was successful.

---

# Step 3 — Run the hashing program

After the file is downloaded, the hashing utility is executed.

### Command executed

```bash
hasher upload_nix.txt
```

---

## What is happening

The `hasher` binary processes the file `upload_nix.txt` and generates a cryptographic hash of its contents.

The resulting hash is printed to the terminal.

---

## Generated hash

```
159cfe5c65054bbadb2761cfa359c8b0
```

---

# Answer

```
159cfe5c65054bbadb2761cfa359c8b0
```

---

# Final Answers

| Question                             | Answer                           |
| ------------------------------------ | -------------------------------- |
| Contents of `flag.txt`               | 5d21cf3da9c0ccb94f709e2559f3ea50 |
| Generated hash from `upload_nix.txt` | 159cfe5c65054bbadb2761cfa359c8b0 |

---
