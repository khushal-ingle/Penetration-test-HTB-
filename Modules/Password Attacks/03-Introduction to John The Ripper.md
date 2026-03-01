

## What is John the Ripper (JtR)?

**John the Ripper** is a password-cracking tool used in **penetration testing** to recover passwords from hashes or protected files.

Why it’s popular:

* Open-source, very fast
* Supports **hundreds of hash types**
* Works with **Linux, Windows, databases, archives, Wi-Fi, PDFs, SSH keys**, etc.
* The **Jumbo** version is best (more formats + better performance)

---

## Main Cracking Modes (Very Important)

### 1️⃣ Single Crack Mode

👉 **Smart guessing based on user info**

* Uses:

  * Username
  * Real name (GECOS)
  * Home directory
* Applies rules like:

  * Capitalization
  * Adding numbers
  * Reversing names

📌 Best for: **Linux password files**

**Example**

```bash
john --single passwd
```

If user = `r0lf`, it may try:

```
r0lf
Rolf
rolf123
Sebastian1
```

✅ Fast
❌ Limited if password is totally random

---

### 2️⃣ Wordlist Mode

👉 **Dictionary attack**

* Tries every word from a wordlist
* Can apply rules (best practice)

**Basic command**

```bash
john --wordlist=rockyou.txt hash.txt
```

**With rules**

```bash
john --wordlist=rockyou.txt --rules hash.txt
```

📌 Best for:

* Real-world passwords
* HTB / TryHackMe labs

✅ Most commonly used
❌ Fails if password isn’t word-based

---

### 3️⃣ Incremental Mode

👉 **Advanced brute force (Markov-based)**

* No wordlist needed
* Generates passwords statistically
* Trages all combinations

**Command**

```bash
john --incremental hash.txt
```

You can limit character sets:

```bash
john --incremental=ASCII hash.txt
```

📌 Best for:

* Short passwords
* Last-resort attacks

❌ Very slow for long passwords
🔥 High CPU usage

---

## Identifying Hash Formats

Sometimes hashes don’t say what they are.

Example:

```
193069ceb0461e1d40d216e32c79c704
```

### Use `hashid`

```bash
hashid -j <hash>
```

This tells:

* Possible hash types
* Correct **John format name**

Then run:

```bash
john --format=raw-md5 hash.txt
```

📌 **Context matters**

* Windows dump → NTLM
* Linux `/etc/shadow` → sha512crypt
* Web app → MD5 / SHA1 / bcrypt

---

## Cracking Encrypted / Protected Files

JtR can’t crack files directly → first convert them to hashes.
Introduction to John The Ripper

### General syntax

```bash
<tool> <file> > hash.txt
john hash.txt
```

### Common `*2john` tools

| Tool           | File Type        |
| -------------- | ---------------- |
| `zip2john`     | ZIP files        |
| `rar2john`     | RAR files        |
| `pdf2john`     | PDFs             |
| `ssh2john`     | SSH private keys |
| `keepass2john` | KeePass DB       |
| `office2john`  | MS Office        |
| `wpa2john`     | Wi-Fi handshakes |

**Example**

```bash
zip2john secret.zip > zip.hash
john zip.hash
```

---

## Useful John Commands (Cheat Sheet)

Show cracked passwords:

```bash
john --show hash.txt
```

Resume session:

```bash
john --restore
```

Specify format:

```bash
john --format=nt hash.txt
```

Use multiple wordlists:

```bash
john --wordlist=a.txt,b.txt hash.txt
```

---

## Interview One-Liners (🔥 Memorize)

* **Single mode** → user-based smart guessing
* **Wordlist mode** → dictionary attack
* **Incremental mode** → brute-force with statistics
* **2john tools** → convert files to crackable hashes
* **Rules** → multiply wordlist effectiveness

---

