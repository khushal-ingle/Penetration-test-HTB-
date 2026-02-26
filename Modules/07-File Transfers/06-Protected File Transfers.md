

# Protected File Transfers (Why This Matters)

As a penetration tester, you’ll often touch **high-risk data**, for example:

* Active Directory artifacts (e.g., `NTDS.dit`)
* Credential dumps
* Network maps and enumeration output
* Internal hostnames, IP ranges, trust relationships

If this data is **intercepted in transit**, the fallout can be serious:

* Client data breach
* Contract violations
* Legal exposure
* Loss of trust (career-ending in bad cases)

So the rule is simple:

> **If the transport isn’t encrypted, the data must be.**

---

## Professional & Ethical Boundary (Very Important)

Unless a client **explicitly asks**, you should **NOT exfiltrate real sensitive data** such as:

* PII (names, Aadhaar, PAN, SSNs)
* Financial data
* Trade secrets

For DLP testing:

* Use **dummy files**
* Mimic structure and size, not real content

This is standard professional practice.

---

# Windows: File Encryption with PowerShell (AES)

On Windows targets, a lightweight and practical approach is **AES encryption via PowerShell**.

### Why this approach works well

* No external binaries required
* Strong encryption (AES-256)
* Works on files *and* strings
* Easy to automate

---

## Invoke-AESEncryption.ps1 (What it does)

This script:

* Derives a **256-bit AES key** using SHA-256
* Uses **AES-CBC**
* Prepends the IV to ciphertext
* Outputs Base64 (for text) or `.aes` files (for binaries)

### Typical workflow

1. **Transfer the script** (any method you already know)
2. **Import it**

```powershell
Import-Module .\Invoke-AESEncryption.ps1
```

3. **Encrypt the file**

```powershell
Invoke-AESEncryption -Mode Encrypt -Key "StrongUniquePassword!" -Path .\scan-results.txt
```

Result:

```
scan-results.txt.aes
```

4. **Transfer the encrypted file**

* SMB, Netcat, HTTP, SCP, etc.

5. **Decrypt offline**

```powershell
Invoke-AESEncryption -Mode Decrypt -Key "StrongUniquePassword!" -Path scan-results.txt.aes
```

---

## Key Management (Don’t Skip This)

**Never reuse passwords across clients.**

Best practice:

* Unique password **per engagement**
* Store securely (password manager / encrypted notes)
* Share with the team **out-of-band**

Reusing one encryption key across multiple clients is a serious operational risk.

---

# Linux: File Encryption with OpenSSL

On Linux systems, the most reliable built-in tool is **OpenSSL**, maintained by the OpenSSL Project.

### Why OpenSSL is ideal

* Installed almost everywhere
* Strong, well-reviewed cryptography
* Scriptable
* No extra dependencies

---

## Encrypting Files with OpenSSL (Best Practice)

Recommended flags:

* `-aes256` → strong cipher
* `-pbkdf2` → modern key derivation
* `-iter 100000` → slows brute-force attacks

### Encrypt

```bash
openssl enc -aes256 -pbkdf2 -iter 100000 \
-in /etc/passwd -out passwd.enc
```

### Decrypt

```bash
openssl enc -d -aes256 -pbkdf2 -iter 100000 \
-in passwd.enc -out passwd
```

This produces a file that is:

* Safe to move over **unencrypted channels**
* Resistant to offline cracking if a strong password is used

---

# Combining Encryption + Transport (Real-World Strategy)

| Situation                 | Recommended Approach                 |
| ------------------------- | ------------------------------------ |
| HTTPS / SSH available     | Encrypt **and** use secure transport |
| Only HTTP / Netcat        | **Encrypt first**, then transfer     |
| IDS / DLP testing         | Dummy data + encryption              |
| Red team exfil simulation | Encrypt, throttle, log everything    |

Encryption is **defense-in-depth**, not a replacement for secure transport.

---

# Common Mistakes to Avoid

 Sending raw credential dumps
 Reusing encryption passwords
 Leaving decrypted files on disk
 Using weak passwords (“test123”)
 Forgetting to clean up artifacts

---

# Final Mental Model

Think in **three layers**:

1. **Data Sensitivity**

   * What would happen if this leaks?

2. **Transport Security**

   * Is the channel encrypted?

3. **Payload Protection**

   * Is the file encrypted before transit?

If **any layer is weak**, compensate with another.

---

## Big Takeaway

> **Encryption is not optional during a pentest — it’s part of your professionalism.**

Mastering protected file transfers:

* Keeps you legally safe
* Keeps clients safe
* Makes you a **trusted** security professional
