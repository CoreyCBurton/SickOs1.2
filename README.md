# SickOs1.2
this repo is based on [SickOS](https://www.vulnhub.com/entry/sickos-12,144/). 

# Setup 
I am using VMware. The creator said he had issues on virtualbox which works perfectly for me, I pefer VMware. I am also using Kali linux. 

# Reconnaissance 
Using the command ``` netdiscover -i eth0 ```, I was able to find the ip for SickOs1.2 which is listed below 

```
192.168.1.142   00:0c:29:10:88:6e      2     120  VMware, Inc. 
```
Typing in that ip in the browser, the website shows 

<img width="1192" alt="Screen Shot 2021-04-22 at 12 31 52 PM" src="https://user-images.githubusercontent.com/81980702/115762191-d0351c80-a368-11eb-91f3-2a7778ae2b10.png">

Viewing the page source, this is what is shown below. 
```
<html>

<img src="blow.jpg">

</html>

<!-- NOTHING IN HERE ///\\\ -->>>>
````
Running ``` nmap -A -T4 -p- 192.168.1.142 ```, I get this text below. ***Breakdown of flags*** -A = Enables OS detection, version detection, script scanning, and trace route -T4 = Sets timmimg template -p = port range  

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
#Brute attack

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
