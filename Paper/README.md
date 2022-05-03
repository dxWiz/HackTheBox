***

Author : dxWiz
Date 04 May 2022

***

First of as usual we will do an nmap scan

    # Nmap 7.92 scan initiated Fri Apr 29 19:23:37 2022 as: nmap -sC -sV -T4 -p- -oN initial.nmap 10.10.11.143
    Nmap scan report for 10.10.11.143
    Host is up (0.024s latency).
    Not shown: 65532 closed tcp ports (conn-refused)
    PORT    STATE SERVICE    VERSION
    22/tcp  open  ssh        OpenSSH 8.0 (protocol 2.0)
    80/tcp  open  http       Apache httpd 2.4.37 ((centos) OpenSSL/1.1.1k mod_fcgid/2.3.9)
    443/tcp open  ssl/https?

    Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    # Nmap done at Fri Apr 29 19:29:47 2022 -- 1 IP address (1 host up) scanned in 370.76 seconds
    
When visit to the website we found nothing but a plain simple HTTP server test page. So the next possible action is to find hidden folder with gobuster or dirb and both of that does not return any interesting result.

Let do a nikto scan


`x-backend-server : office.paper` seems interesting, let's assume it is a vhost and add $IP and office.paper as hostname in `/etc/hosts`


