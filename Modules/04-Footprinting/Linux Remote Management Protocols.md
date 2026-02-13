

# Linux Remote Management Protocols

Remote management protocols allow administrators to manage Linux systems over a network without physical access. These services are common on internal and public-facing servers and are high-value targets during penetration testing due to misconfigurations, weak authentication, or legacy usage.

---

## 1. SSH (Secure Shell)

### Overview

* Encrypted remote management protocol
* Default port: **TCP 22**
* Replaced insecure protocols like Telnet and r-services
* Works on Linux, macOS, and Windows
* Most common implementation: **OpenSSH**

### SSH Protocol Versions

| Version | Status     | Notes                                              |
| ------- | ---------- | -------------------------------------------------- |
| SSH-1   | Deprecated | Vulnerable to MITM attacks                         |
| SSH-2   | Secure     | Improved encryption, integrity, and authentication |

---

### SSH Capabilities

* Remote shell access
* Command execution
* Secure file transfer (SCP, SFTP)
* Port forwarding and tunneling
* X11 forwarding (GUI apps)

---

### SSH Authentication Methods

OpenSSH supports six authentication mechanisms:

1. Password authentication
2. Public-key authentication
3. Host-based authentication
4. Keyboard-interactive authentication
5. Challenge-response authentication
6. GSSAPI authentication

Most common and secure method: **Public-key authentication**

---

## 2. Public Key Authentication (SSH)

### How It Works

1. Server presents its **host public key** to the client
2. Client verifies server identity (first-time trust risk)
3. Client proves ownership of **private key**
4. Server verifies client using stored **authorized public key**

### Key Points

* Private key never leaves the client
* Private key protected by a passphrase
* Password not transmitted over the network
* Single passphrase unlocks access to multiple servers per session

---

## 3. SSH Default Configuration

Default OpenSSH configuration file:

```
/etc/ssh/sshd_config
```

Common default active settings:

* `UsePAM yes`
* `X11Forwarding yes`
* `ChallengeResponseAuthentication no`
* `Subsystem sftp /usr/lib/openssh/sftp-server`

Most security-critical options are **commented out** and must be manually configured.

---

## 4. Dangerous SSH Configurations

| Setting                      | Risk                                   |
| ---------------------------- | -------------------------------------- |
| `PasswordAuthentication yes` | Enables brute-force attacks            |
| `PermitEmptyPasswords yes`   | Allows blank passwords                 |
| `PermitRootLogin yes`        | Direct root access                     |
| `Protocol 1`                 | Weak, deprecated crypto                |
| `X11Forwarding yes`          | Past command injection vulnerabilities |
| `AllowTcpForwarding yes`     | Pivoting and tunneling risk            |
| `PermitTunnel yes`           | Network abuse                          |
| `DebianBanner yes`           | Information disclosure                 |

---

## 5. SSH Footprinting

### ssh-audit Tool

* Enumerates SSH version
* Identifies weak algorithms
* Detects deprecated crypto

Key info obtained:

* OpenSSH version
* Supported authentication methods
* Weak key exchange or host-key algorithms

### Banner Example

```
SSH-2.0-OpenSSH_8.2p1
```

Indicates:

* Only SSH-2 supported
* OpenSSH version 8.2p1

---

### Authentication Enumeration

Using verbose mode:

```
ssh -v user@host
```

Example output:

```
Authentications that can continue: publickey,password,keyboard-interactive
```

You can force an authentication method:

```
ssh -o PreferredAuthentications=password user@host
```

Useful for:

* Brute-force testing
* Understanding server auth policy

---

## 6. Rsync

### Overview

* File synchronization and transfer utility
* Default port: **TCP 873**
* Can operate over SSH
* Uses delta-transfer algorithm

### Common Uses

* Backups
* Mirroring
* File replication

---

### Rsync Security Issues

* Anonymous access possible
* Sensitive files may be exposed
* Often reused credentials
* SSH keys may be downloadable

---

### Rsync Enumeration

Scan:

```
nmap -sV -p 873 target
```

Manual probe:

```
nc target 873
#list
```

List shared modules:

```
rsync rsync://target/
```

List files in a module:

```
rsync -av --list-only rsync://target/module
```

Download contents:

```
rsync -av rsync://target/module
```

---

## 7. R-Services (Legacy Remote Access)

### Overview

* Pre-SSH remote management suite
* Unencrypted communication
* Vulnerable to MITM attacks
* Still found in legacy Unix systems

### Default Ports

| Port | Service |
| ---- | ------- |
| 512  | rexec   |
| 513  | rlogin  |
| 514  | rsh     |

---

### Common R-Commands

| Command | Purpose                    |
| ------- | -------------------------- |
| rcp     | Remote file copy           |
| rsh     | Remote shell without login |
| rlogin  | Remote login               |
| rexec   | Remote command execution   |
| rwho    | Lists logged-in users      |
| rusers  | Detailed user session info |

---

## 8. Trust Relationships (Critical Weakness)

### Trust Files

* `/etc/hosts.equiv` (global trust)
* `~/.rhosts` (per-user trust)

Syntax:

```
username hostname
username IP
+ +
```

### Risks

* No password required
* IP-based trust
* Wildcards allow anyone

---

### Exploitation Example

```
rlogin target -l user
```

Successful login without credentials indicates:

* Misconfigured trust relationship
* Immediate shell access

---

## 9. Network User Enumeration

### rwho

* Lists logged-in users across network
* Uses UDP broadcasts

### rusers

* Shows detailed session info
* Useful for username discovery

---

## Final Takeaways

* SSH is secure **only when properly configured**
* Rsync frequently leaks sensitive data
* R-services are inherently insecure and still exploitable
* Credential reuse across services is common
* Remote management services should always be:

  * Enumerated
  * Fingerprinted
  * Tested for misconfigurations

