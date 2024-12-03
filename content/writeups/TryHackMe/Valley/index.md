---
title: Valley Writeup - TryHackMe
date: 2024-12-03
draft: false
tags:
  - Linux
  - Web
  - Easy
---

| Link:      | [https://tryhackme.com/r/room/valleype](https://tryhackme.com/r/room/valleype) |
| ---------- | ------------------------------------------------------------------------------ |
| Difficulty | Easy                                                                           |
| Machine    | Linux                                                                          |

---

This is an easy challenge of THM rated at 120 minute.
# Enumeration

So I started the box I first scanned with NMAP.

### NMAP 
![Untitled](images/Pasted%20image%2020241109012411.png)

Found two services running on most common default port.

On website this was the interface I was greeted with:
![Untitled](images/Pasted%20image%2020241109012508.png)

![Untitled](images/Pasted%20image%2020241109012641.png)

![Untitled](images/Pasted%20image%2020241109012650.png)

So then I started enumerating the directory

![Untitled](images/Pasted%20image%2020241109012714.png)

Found an unusual directory so I checked it!

![Untitled](images/Pasted%20image%2020241109012805.png)

Hmm seems like a hint `remove /dev123....`
Lets check it out

![Untitled](images/Pasted%20image%2020241109012743.png)

Woah! a login page with client side authentication lets see where it redirects to.

![Untitled](images/Pasted%20image%2020241109012930.png)

Hmm a interesting note.
- stop reusing credential hmm... remember those client side authenticated user and pass?
- hm and it says change ftp port to normal 

### Scanning All Ports 

Lets start scanning the all the possible port using Nmap.
we can use `-p-` option to check all the possible port which is **0 to 65535**

![Untitled](images/Pasted%20image%2020241109013234.png)

Hmm~ This must be it the ftp server he mentioned in abnormal port.

Lets try to login with the creds that we got in JS file.

![Untitled](images/Pasted%20image%2020241109013338.png)

:) Logged In!

### Analaysing PCAP File

Lets download those files and see what it contains!

![Untitled](images/Pasted%20image%2020241109013417.png)

So after digging up the PCAP file one by one I found another interesting creds.

![Untitled](images/Pasted%20image%2020241109013445.png)

I am using this website called `apacket.com` which nicely shows the http connections and other connections in a good friendly UI.

So lets try to use that cred in SSH!

![Untitled](images/Pasted%20image%2020241109013602.png)

Ok we logged in and we got our user flag. yay!!!
Noice ~

Now Lets see what we got more in this machine lets try to enumerate so we can get a privilege access. 
I find there is a strange file named **valleyAuthentication**

![Untitled](images/Pasted%20image%2020241109013813.png)

Lets pull that bad boy in our machine and analyse.

Scan it up !

### Reversing The Binary

![Untitled](images/Pasted%20image%2020241109013847.png)

Hmm UPX compressed elf binary huh! lets decompress it :D

![Untitled](images/Pasted%20image%2020241109013917.png)

Ok now its ready to be analysed!

![Untitled](images/Pasted%20image%2020241109013950.png)

Found some hash its a md5 hash (I know it cause the binary contained md5 string)

Lets decrypt it

![Untitled](images/Pasted%20image%2020241109014127.png)

![Untitled](images/Pasted%20image%2020241109014146.png)


So we now know 
**username**: valley
**password**: liberty123

### Privillege Escalation

Lets login into SSH!

![Untitled](images/Pasted%20image%2020241109014247.png)
Logged in! 
I tried `sudo -l` but it didn't have any permission so I started basic enumeration searching for `cron` 
Found it!
Lets check crontab.

![Untitled](images/Pasted%20image%2020241109014414.png)
Interesting file **photosEncrypt** hmm!
Lets see what it got!

![Untitled](images/Pasted%20image%2020241109014445.png)oh a b64encode encryption wow :) (actually its a encoding scheme :nerdemoji: )

Lets try to modify the python package and inject our own code shall we :)

![Untitled](images/Pasted%20image%2020241109014548.png)
We can find the base64.py package in that directory now lets modify it to get a root shell!
Lets start listening for connection in port `1337` using our NCAT.

![Untitled](images/Pasted%20image%2020241109014622.png)

*Bomb has been planted* 


Lets wait :D!

![Untitled](images/Pasted%20image%2020241109014835.png)

Well well well now that was easy ( jk )

THE END!


![Untitled](images/Pasted%20image%2020241109020015.png)

### HEHE

![Untitled](images/Pasted%20image%2020241109014924.png)
