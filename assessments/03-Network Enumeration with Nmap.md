# Enumeration with Nmap

> All ans with practical of Enumaration with nmap Module

# Host Discovery 
## Question
> Based on the last result, find out which operating system it belongs to. Submit the name of the operating system as result?.

## Host Discovery with TTL value
### Common TTL values (useful in pentesting):
> TTL (Time To Live) is a timer value included in packets sent over networks that tells the recipient how long to hold or use the packet before discarding and expiring the data (packet). TTL values are different for different Operating Systems. So, you can determine the OS based on the TTL value. You can get the TTL value by pinging an address. Here is the output got by pinging “subinsb.com” on my system :

>64 → Linux / Unix

>128 → Windows

>255 → Network devices (routers, switches)


nmap -sn 10.10.10.10/24 
> Run this command and you will get TTL Value
```
sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace --disable-arp-ping
```
> Output
```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 00:12 CEST
SENT (0.0107s) ICMP [10.10.14.2 > 10.129.2.18 Echo request (type=8/code=0) id=13607 seq=0] IP [ttl=255 id=23541 iplen=28 ]
RCVD (0.0152s) ICMP [10.129.2.18 > 10.10.14.2 Echo reply (type=0/code=0) id=13607 seq=0] IP [ttl=128 id=40622 iplen=28 ]
Nmap scan report for 10.129.2.18
Host is up (0.086s latency).
MAC Address: DE:AD:00:00:BE:EF
Nmap done: 1 IP address (1 host up) scanned in 0.11 seconds
```

## Answer

>  Windows

# Port Scanning

## Question

> Find all open TCP ports on your target. Submit the total number of found TCP ports as the answer.


```
nmap -v -sT -Pn 10.129.93.246
```

<img width="378" height="169" alt="image" src="https://github.com/user-attachments/assets/d3610543-293a-4468-b0b1-1966d9c658bc" />
<br/>

## Answer
>  7
# Host Scanning
## Question

>  Enumerate the hostname of your target and submit it as the answer. (case-sensitive)
<br/>

> i scan all 7 ports one by one then i get the hostname

```
nmap 10.129.4.81 -p 445 -sV
```

<img width="778" height="213" alt="image" src="https://github.com/user-attachments/assets/deae1170-06ca-4248-adc0-e7e5badd0bc6" />


## Answer
> NIX-NMAP-DEFAULT

# Saving the Results

## Question 
> Perform a full TCP port scan on your target and create an HTML report. Submit the number of the highest port as the answer.
<img width="1023" height="851" alt="image" src="https://github.com/user-attachments/assets/a23414be-9bcf-48ba-943a-e10521371431" />
<br/>

> i haven't created an HtML file because i already  got the port in previouse scan 

## Answer

> 31337

# Service Enumeration

## Question

> Enumerate all ports and their services. One of the services contains the flag you have to submit as the answer.

```
nmap -p 31337 -sV 10.129.4.81
```

<img width="637" height="242" alt="image" src="https://github.com/user-attachments/assets/9d6d13fd-3338-44a5-8b49-ba4ad56f331a" />

## Answer
> HTB{pr0F7pDv3r510nb4nn3r}


# Nmap Scripting Engine

## Question
> Use NSE and its scripts to find the flag that one of the services contain and submit it as the answer.
## hint 
> Based on the hint targeting web services, HTTP NSE scripts were used to enumerate the web server, revealing the flag inside robots.txt.
>The Nmap NSE http-enum script was used to enumerate common web paths on the HTTP service

<img width="592" height="154" alt="image" src="https://github.com/user-attachments/assets/90e1f1c7-1041-4615-9446-3325ae679af9" />

## Answer
> HTB{873nniuc71bu6usbs1i96as6dsv26}

# Firewall and IDS/IPS Evasion - Easy Lab
## Question
>  Our client wants to know if we can identify which operating system their provided machine is running on. Submit the OS name as the answer.

> I am trying with TTl value 

```
nmap -sn -oA host -PE --packet-trace --disable-arp-ping 10.129.5.114
```
> Since reply TTL is 63, it is likely:
> Linux/Unix-based system (default TTL 64)
> But this is only an assumption (1 hop difference).

>performs a service/version detection scan on the target

```
nmap -sV -Pn 10.129.5.114
```
<img width="720" height="117" alt="image" src="https://github.com/user-attachments/assets/701f4eee-8743-4022-8cfb-be4ff8efc677" />

## Answer
> Ubuntu

# Firewall and IDS/IPS Evasion - Medium Lab
## Question
> fter the configurations are transferred to the system, our client wants to know if it is possible to find out our target's DNS server version. Submit the DNS server version of the target as the answer.

> Run this to detect a DNS server and attempt to identify its version/identity using NSID.
```
nmap -sU -p 53 --script=dns-nsid 10.129.5.119
```
<img width="525" height="190" alt="image" src="https://github.com/user-attachments/assets/f6fb377d-fe3b-4eae-af7c-a3d2f765e403" />

>A UDP scan was performed on port 53 using the Nmap NSE script dns-nsid to enumerate the DNS service and retrieve the Name Server Identifier (NSID). This may expose information about the DNS server software and configuration.

## Answer 
> HTB{GoTtgUnyze9Psw4vGjcuMpHRp}

# Firewall and IDS/IPS Evasion - Hard Lab
## Question
> Now our client wants to know if it is possible to find out the version of the running services. Identify the version of service our client was talking about and submit the flag as the answer.

```
