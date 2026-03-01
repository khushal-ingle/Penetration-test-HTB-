
# Introduction

Confidentiality, Integrity, and Availability (CIA) are the foundational principles of information security. Every infosec practitioner is responsible for maintaining a balance between these three. If one fails, the overall security of an organization is compromised.

This balance is achieved by:

* Auditing and accounting for files, systems, and users
* Ensuring users have the correct permissions (authorization)
* Verifying user identities before granting access (authentication)

Most security breaches can be traced back to a failure in one of these principles. This module focuses on **authentication**, specifically how it can be attacked and bypassed through password compromise across operating systems, applications, and encryption methods.

Before learning how passwords are attacked, it is important to understand authentication and how it works.

---

# Authentication

Authentication is the process of validating a user’s identity. This validation is based on one or more authentication factors presented to an authentication mechanism.

## Authentication Factors

Authentication can rely on four primary factors:

1. **Something you know**

   * Passwords
   * PINs
   * Passcodes
   * Passphrases

2. **Something you have**

   * Smart cards
   * ID cards
   * Trusted mobile devices
   * Hardware tokens

3. **Something you are**

   * Fingerprints
   * Facial recognition
   * Iris or retina scans
   * Voice recognition

4. **Somewhere you are**

   * Geographic location
   * IP address

Systems may require one or multiple factors depending on the sensitivity of the resource. High-security environments often enforce multi-factor authentication (MFA). For example, healthcare professionals may need a smart card combined with a PIN or password to access patient records.

A common everyday example is email access. Typically, authentication requires knowledge of the email address and password. Additional factors such as a mobile device for 2FA or biometric verification may also be involved.

---

# Authentication vs Authorization

Authentication verifies **who you are**.
Authorization determines **what you are allowed to do**.

If the correct authentication credentials are provided, authorization rules are applied, granting the user specific permissions. If authentication is compromised, authorization controls often become ineffective.

---

# The Use of Passwords

Passwords remain the most common authentication method. A password or passphrase is a string of characters used to validate identity.

For example, an 8-character password using only uppercase letters and numbers provides:

36⁸ = 208,827,064,576 possible combinations

In practice, passwords do not need to be random strings. They may be:

* Song lyrics
* Book quotes
* Memorable phrases
* Random word combinations (e.g., “TreeDogEvilElephant”)

The goal is to meet or exceed an organization’s security policy while remaining usable.

---

# Usability vs Security

Stronger authentication mechanisms often introduce complexity and inconvenience. This can negatively impact user experience and productivity. For this reason, username and password authentication remains dominant because it balances usability and security.

An example is online shopping: creating an account simplifies checkout and authentication compared to re-entering personal information repeatedly.

---

# Password Behavior and Statistics

Human behavior is one of the biggest weaknesses in password security.

### Survey Findings

* Common passwords include:

  * 123456
  * password
  * qwerty
* Many users base passwords on:

  * Their own name
  * Pet names
  * Children’s names
* Password reuse is widespread

A 2019 Google and Harris Poll survey found:

* 24% used extremely weak passwords
* Only 15% used password managers
* 66% reused passwords across multiple platforms

This means that once a password is compromised, there is a high likelihood it can be reused on other services using the same username or email address.

More recent statistics from 2025 show:

* Weak passwords remain common
* Password reuse is still prevalent
* Password manager usage has increased to 36%

---

# Passwords After Data Breaches

One concerning finding is that only 45% of users change their passwords after a data breach. This means over half continue using known-compromised credentials.

Users can check whether their email addresses have appeared in known breaches using public breach-notification services. These services list breaches associated with an email address, highlighting potential exposure.

---

# Digging In

Now that we understand:

* What authentication is
* Why passwords are widely used
* How user behavior weakens security

The next step is to explore **how passwords and credentials are stored**, protected, and ultimately attacked.

This knowledge forms the foundation for understanding real-world password attacks and authentication bypass techniques.
