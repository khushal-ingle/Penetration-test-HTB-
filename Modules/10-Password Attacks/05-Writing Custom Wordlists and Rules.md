
## Writing Custom Wordlists and Rules

### Why custom wordlists matter

Even when strong password policies exist, **human behavior stays predictable**. Users usually:

* Reuse meaningful words
* Apply minimal changes to satisfy policy
* Increment years, months, or numbers
* Add a single symbol at the end

This makes **targeted wordlists + rules** far more effective than blind brute force.

---

## Common Human Password Patterns

| Requirement      | Typical User Behavior              |
| ---------------- | ---------------------------------- |
| Uppercase letter | Capitalize first letter            |
| Numbers          | Append birth year or random digits |
| Symbols          | Add `!`, `@`, or `#` at the end    |
| Length           | Barely meet minimum length         |
| Rotation         | Change year or digit only          |

Example transformations:

* `password` → `Password`
* `password` → `Password2022`
* `password` → `P@ssw0rd2022!`

---

## Hashcat Rules Refresher

Each rule is **one transformation per line**.

### Common rule functions

| Rule  | Meaning                 |
| ----- | ----------------------- |
| `:`   | No change               |
| `c`   | Capitalize first letter |
| `l`   | Lowercase               |
| `u`   | Uppercase               |
| `sXY` | Replace X with Y        |
| `$!`  | Append `!`              |

---

## Example Custom Rule File

```
:
c
so0
sa@
c so0
c sa@
c sa@ so0
$!
$! c
$! so0
$! sa@
$! c so0
$! c sa@
$! so0 sa@
$! c so0 sa@
```

---

## Generating Mutated Wordlists (Offline)

```
hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
```

This allows you to:

* Generate wordlists without cracking
* Reuse them in other tools
* Inspect guesses manually

---

## Using CeWL for OSINT-Based Wordlists

CeWL extracts **real words from company websites**, which users frequently reuse in passwords.

```
cewl https://www.inlanefreight.com -d 4 -m 6 --lowercase -w inlane.wordlist
```

Best used with:

* Rules
* Personal data
* Company branding

---

# Exercise: Targeted Password Cracking (Mark White)

### Known Information (OSINT)

| Category  | Data                                    |
| --------- | --------------------------------------- |
| Name      | Mark White                              |
| DOB       | August 5, 1998                          |
| Company   | Nexura, Ltd.                            |
| Location  | San Francisco, CA                       |
| Pet       | Bella (cat)                             |
| Wife      | Maria                                   |
| Son       | Alex                                    |
| Interest  | Baseball                                |
| Policy    | ≥12 chars, upper, lower, number, symbol |
| Hash      | `97268a8ae45ac7d15c3cea4ce6ea550b`      |
| Hash Type | MD5 (`-m 0`)                            |

---

## Step 1: Build a Targeted Base Wordlist

```
cat > mark_base.list << EOF
mark
white
nexura
bella
maria
alex
baseball
sf
sanfrancisco
august
aug
1998
EOF
```

---

## Step 2: Create a Policy-Aware Rule File

```
cat > mark.rule << EOF
c
$1998
$98
$!
$1998!
c $1998!
c sa@ so0 $1998!
EOF
```

This enforces:

* Uppercase
* Numbers
* Symbol
* Length ≥12

---

## Step 3: Generate Custom Wordlist

```
hashcat --force mark_base.list -r mark.rule --stdout | sort -u > mark_custom.list
```

---

## Step 4: Crack the Hash

```
hashcat -a 0 -m 0 97268a8ae45ac7d15c3cea4ce6ea550b mark_custom.list
```

---

## Result

The password cracks successfully as:

```
BellaAlex1998!
```

---

## Why This Worked

* Used **family names**
* Included **birth year**
* Capitalized first letter
* Added a **single symbol**
* Met **exact policy minimum**
* Extremely common real-world behavior

---

## Key Takeaways

* Custom wordlists outperform massive generic lists
* OSINT dramatically increases success rate
* Rules model **human laziness**
* Most users meet policy requirements with **minimal effort**
* Password cracking is **pattern recognition**, not guessing

---

