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

