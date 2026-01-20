## 🔐 VPN Connection

>Before interacting with the target, I established a secure connection to the lab using **OpenVPN**.

```
openvp academy-regular.ovpn
```
## Service Scan using Nmap

```
nmap -v -sT -sV -sC -A -O 10.129.130.170
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

![file:///tmp/.55BCJ3/6.png](file:///tmp/.55BCJ3/6.png)


>After checking all pages. i found username and password

![file:///tmp/.55BCJ3/7.png](file:///tmp/.55BCJ3/7.png)

![file:///tmp/.55BCJ3/8.png](file:///tmp/.55BCJ3/8.png)

![file:///tmp/.55BCJ3/9.png](file:///tmp/.55BCJ3/9.png)

>But the password is encryption so we have to use hashcat to decryption.

```
echo "d033e22ae348aeb5660fc2140aec35850c4da997" >> KnowledgeCheck.txt
```

![file:///tmp/.55BCJ3/11.png](file:///tmp/.55BCJ3/11.png)

>Use --show to Display already cracked hashes

![file:///tmp/.55BCJ3/12.png](file:///tmp/.55BCJ3/12.png)

>now we got username and password. try to login.

![file:///tmp/.55BCJ3/13.png](file:///tmp/.55BCJ3/13.png)

>After exploring the web app i found a vulnerability

![file:///tmp/.55BCJ3/14.png](file:///tmp/.55BCJ3/14.png)

