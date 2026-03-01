
# Introduction to Hashcat

Hashcat is a powerful, cross-platform **password cracking tool** available on **Linux, Windows, and macOS**. Originally proprietary (2009–2015), it is now **open-source** and widely used in penetration testing and digital forensics.

Hashcat is known for:

* Excellent **GPU acceleration**
* Support for **hundreds of hash algorithms**
* Multiple efficient **attack modes**
* High performance compared to CPU-only tools

Like John the Ripper (JtR), Hashcat focuses on **offline password cracking**, where the attacker already possesses password hashes.

---

# Basic Hashcat Syntax

```
hashcat -a <attack_mode> -m <hash_type> <hashes> [wordlist | rule | mask | ...]
```

### Key Options

* `-a` → Attack mode
* `-m` → Hash type (hash mode ID)
* `<hashes>` → Single hash or file containing hashes
* `[wordlist | rule | mask]` → Depends on attack mode

---

# Hash Types in Hashcat

Each hash algorithm is identified by a **numeric ID** called a *hash mode*.

To list all supported hash modes:

```
hashcat --help
```

### Common Hash Mode Examples

| Hash      | Hashcat Mode |
| --------- | ------------ |
| MD5       | 0            |
| SHA-1     | 100          |
| SHA-256   | 1400         |
| SHA-512   | 1700         |
| NTLM      | 1000         |
| bcrypt    | 3200         |
| MD5-crypt | 500          |

---

## Identifying Unknown Hashes

### Using hashID

```
hashid -m '<hash>'
```

Example output:

```
[+] MD5 Crypt [Hashcat Mode: 500]
```

This helps determine which `-m` value to use.

---

# Attack Modes Overview

Hashcat supports many attack modes. The two most commonly used are:

| Mode       | ID     | Description               |
| ---------- | ------ | ------------------------- |
| Dictionary | `-a 0` | Wordlist-based            |
| Mask       | `-a 3` | Pattern-based brute force |

---

# Dictionary Attack (`-a 0`)

A dictionary attack tests passwords from a **wordlist** against one or more hashes.

### Example

Cracking an MD5 hash using `rockyou.txt`:

```
hashcat -a 0 -m 0 e3e3ec5831ad5e7288241960e5d4fdb8 /usr/share/wordlists/rockyou.txt
```

### Why Dictionary Attacks Are Effective

* Humans choose predictable passwords
* Faster than brute-force
* High success rate in real environments

---

## Rule-Based Dictionary Attacks

Wordlists alone are often insufficient. **Rules** modify words to create realistic variations.

### Examples of Rule Modifications

* Capitalizing first letter
* Appending numbers
* Replacing letters with symbols (leet speak)

Rules are stored in:

```
/usr/share/hashcat/rules/
```

### Popular Rule Files

* `best64.rule`
* `rockyou-30000.rule`
* `leetspeak.rule`
* `dive.rule`

---

### Dictionary + Rules Example

```
hashcat -a 0 -m 0 1b0556a75770563578569ae21392630c \
/usr/share/wordlists/rockyou.txt \
-r /usr/share/hashcat/rules/best64.rule
```

This greatly increases success while remaining efficient.

---

# Mask Attack (`-a 3`)

A mask attack is a **controlled brute-force attack** where you define the exact password structure.

Instead of testing every possible combination, you define a **pattern**.

---

## Built-in Character Sets

| Symbol | Characters        |
| ------ | ----------------- |
| `?l`   | lowercase letters |
| `?u`   | uppercase letters |
| `?d`   | digits            |
| `?s`   | symbols           |
| `?a`   | all printable     |
| `?h`   | hex lowercase     |
| `?H`   | hex uppercase     |
| `?b`   | full byte range   |

---

## Custom Character Sets

Defined using:

* `-1`, `-2`, `-3`, `-4`
* Referenced as `?1`, `?2`, etc.

---

## Mask Attack Example

Password pattern:

* 1 uppercase letter
* 4 lowercase letters
* 1 digit
* 1 symbol

Mask:

```
?u?l?l?l?l?d?s
```

Command:

```
hashcat -a 3 -m 0 1e293d6912d074c0fd15844d803400dd '?u?l?l?l?l?d?s'
```

This is **far faster than pure brute-force** and extremely effective when password structure is known.

---

# Dictionary vs Mask Attacks

| Feature        | Dictionary       | Mask           |
| -------------- | ---------------- | -------------- |
| Speed          | Fast             | Very fast      |
| Flexibility    | Medium           | High           |
| Human behavior | Exploits         | Exploits       |
| Best use case  | Common passwords | Known patterns |

---

# Key Takeaways

* Hashcat is one of the **fastest password crackers available**
* Correct hash identification (`-m`) is critical
* Dictionary attacks should always be tried first
* Rules dramatically improve success rates
* Mask attacks outperform brute-force when structure is known
* GPU acceleration provides massive speed advantages

---

