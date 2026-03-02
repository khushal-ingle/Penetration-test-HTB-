

# Cracking Protected Archives 

## Why attackers care about archives

In real environments, archives are everywhere because they:

* Bundle many files into one
* Preserve folder structure
* Are easy to email, upload, and store
* Often contain **high-value data** (reports, credentials, backups)

From an attacker’s point of view:

> **One weak password can unlock dozens or hundreds of files.**

That’s why cracking archives is high-ROI.

---

## Common archive formats you’ll see

Frequently encountered:

```
zip, rar, 7z, tar, gz, gzip,
vhd/vmdk, bitlocker, truecrypt, kdbx
```

Not all formats support passwords natively:

* `zip`, `rar`, `7z` → native password support
* `tar` → usually encrypted using **openssl** or **gpg**
* `vhd` → often **BitLocker**

---

## General cracking workflow (important)

1. **Identify archive type**
2. **Determine encryption method**
3. **Extract a crackable hash (if possible)**
4. **Crack offline (John or Hashcat)**
5. **Access contents**
6. **Pivot / escalate**

Offline cracking = no alerts, no lockouts.

---

## 1️1 Cracking ZIP archives

### Step 1: Extract hash

```bash
zip2john ZIP.zip > zip.hash
```

This converts the ZIP encryption into a hash John can crack.

### Step 2: Crack with John

```bash
john --wordlist=rockyou.txt zip.hash
```

### Step 3: Show result

```bash
john zip.hash --show
```

✔ Very fast
✔ Very common in Windows environments
❌ Strong ZIP passwords still resist wordlists

---

## 2️2 Cracking OpenSSL-encrypted GZIP files

### Why this is tricky

* `.gzip` doesn’t normally support passwords
* `openssl` can encrypt **anything**
* No reliable hash extraction

### Step 1: Identify encryption

```bash
file GZIP.gzip
```

If you see:

```
openssl enc'd data with salted password
```

→ password-protected via OpenSSL

### Step 2: Brute-force decryption directly

```bash
for i in $(cat rockyou.txt); do
  openssl enc -aes-256-cbc -d -in GZIP.gzip -k $i 2>/dev/null | tar xz
done
```

* Errors are normal
* **Correct password = files extract successfully**

✔ Works when hash cracking fails
❌ Slower
❌ No progress indicator

---

## 3️3 Cracking BitLocker-encrypted drives

### Key facts

* Uses AES-128 / AES-256
* Two secrets:

  * **Password**
  * **Recovery key (48 digits)** → not brute-forceable

We target the **password hash only**.

---

### Step 1: Extract hashes

```bash
bitlocker2john -i Backup.vhd > backup.hashes
```

### Step 2: Isolate password hash

```bash
grep "bitlocker\$0" backup.hashes > backup.hash
```

Why?

* `$bitlocker$0$` → password hash
* Other hashes = recovery key (ignore)

---

### Step 3: Crack with Hashcat

```bash
hashcat -a 0 -m 22100 backup.hash /usr/share/wordlists/rockyou.txt
```

Important:

* `-m 22100` → BitLocker
* Cracking speed is **slow**
* Weak passwords still fall

---

## 4️4 Mounting BitLocker drives after cracking

### Windows (easy)

* Double-click `.vhd`
* Enter cracked password
* Browse files

---

### Linux / macOS (using dislocker)

Install:

```bash
sudo apt install dislocker
```

Create mount points:

```bash
sudo mkdir -p /media/bitlocker /media/bitlockermount
```

Attach VHD:

```bash
sudo losetup -f -P Backup.vhd
```

Decrypt:

```bash
sudo dislocker /dev/loop0p2 -uPASSWORD -- /media/bitlocker
```

Mount:

```bash
sudo mount -o loop /media/bitlocker/dislocker-file /media/bitlockermount
```

Unmount when done:

```bash
sudo umount /media/bitlockermount
sudo umount /media/bitlocker
```

---

## Key takeaways (very important)

* **ZIP → zip2john → john**
* **BitLocker → bitlocker2john → hashcat -m 22100**
* **OpenSSL encryption may require brute-forcing decryption, not hashes**
* Always check file type with `file`
* Offline cracking = stealthy and safe
* Archives often lead to **credential reuse, lateral movement, or privilege escalation**

---

## Mental shortcut (remember this)

> If it’s an archive → try to extract a hash
> If no hash → brute-force decryption
> If BitLocker → Hashcat 22100

---
