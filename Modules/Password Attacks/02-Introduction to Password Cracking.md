
# Introduction to Password Cracking

Passwords are usually **stored as hashes**, not in plaintext, to reduce damage if a database is compromised. A **hash** is the output of a mathematical function that converts an input of arbitrary length into a fixed-size string.

Common hash functions include:

* MD5
* SHA-1
* SHA-256
* SHA-512

## Example: Hashing a Password

Password:

```
Soccer06!
```

MD5 hash:

```
40291c1d19ee11a7df8495c4cccefdfa
```

SHA-256 hash:

```
a025dc6fabb09c2b8bfe23b5944635f9b68433ebd9a1a09453dd4fee00766d93
```

Hash functions are **one-way functions**, meaning the original password **cannot be mathematically reversed** from the hash.
Attempting to recover the original password from its hash is known as **password cracking**.

---

# Common Password Cracking Techniques

## 1. Rainbow Tables

Rainbow tables are **pre-computed lookup tables** that map plaintext passwords to their corresponding hashes. If an attacker finds a hash in the table, the password is immediately revealed.

### Example (MD5):

| Password | MD5 Hash                         |
| -------- | -------------------------------- |
| 123456   | e10adc3949ba59abbe56e057f20f883e |
| password | 5f4dcc3b5aa765d61d8327deb882cf99 |
| rockyou  | f806fc5a2a0d5ba2471600758452799c |

### Why Rainbow Tables Are Dangerous

* Extremely fast
* No computation required at attack time
* Effective against **unsalted hashes**

---

## Salting

To defend against rainbow table attacks, systems use **salts**.

A **salt** is a random value added to the password **before hashing**.

Example:

```
Salt: Th1sIsTh3S@lt_
Password: Soccer06!
```

Resulting MD5 hash:

```
90a10ba83c04e7996bc53373170b5474
```

### Important Points About Salts

* Salts are **not secret**
* Stored alongside the hash
* Each password should have a **unique salt**
* Prevents reuse of pre-computed rainbow tables

### Why Salting Works

Even a **1-byte salt** increases rainbow table size by a factor of **256**.
A properly salted database makes rainbow table attacks impractical.

---

## 2. Brute-Force Attacks

A brute-force attack attempts **every possible character combination** until the correct password is found.

### Characteristics

* Guaranteed success given enough time
* Very slow for long passwords
* Mostly ineffective against strong passwords

### Practical Limits

* Passwords shorter than **9 characters** may still be crackable
* Performance depends on:

  * Hash algorithm
  * Hardware (CPU vs GPU)

### Example Hash Speeds

* MD5: millions of guesses per second
* DCC2 / bcrypt: thousands per second

Because of the time required, pure brute-force attacks are rarely used in real engagements.

---

## 3. Dictionary (Wordlist) Attacks

Dictionary attacks use **lists of common passwords** instead of all possible combinations.

### Why Dictionary Attacks Are Effective

* Humans choose predictable passwords
* Much faster than brute force
* High success rate in real-world scenarios

### Common Wordlists

* rockyou.txt
* SecLists
* Custom organization-specific lists

Example from rockyou.txt:

```
123456
password
iloveyou
princess
qwerty
```

### rockyou.txt Background

* Contains **14+ million real passwords**
* Leaked in 2009 from RockYou
* Passwords were stored in plaintext
* Still highly effective today

---

# Comparison of Techniques

| Method        | Speed     | Effectiveness        | Use Case           |
| ------------- | --------- | -------------------- | ------------------ |
| Rainbow Table | Very fast | Only unsalted hashes | Legacy systems     |
| Brute-Force   | Very slow | Guaranteed           | Short passwords    |
| Dictionary    | Fast      | Very high            | Real-world attacks |

---

# Key Takeaways

* Hashing protects passwords, but does not make them immune to attack
* Unsalted hashes are highly vulnerable
* Salting dramatically improves security
* Dictionary attacks are the most practical cracking method
* Human password behavior is the weakest link

---

