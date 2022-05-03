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

    └─$ nikto -host 10.10.11.143 -o nikto.txt                                                                      1 ⨯
    - Nikto v2.1.6
    ---------------------------------------------------------------------------
    + Target IP:          10.10.11.143
    + Target Hostname:    10.10.11.143
    + Target Port:        80
    + Start Time:         2022-05-03 17:04:12 (GMT-4)
    ---------------------------------------------------------------------------
    + Server: Apache/2.4.37 (centos) OpenSSL/1.1.1k mod_fcgid/2.3.9
    + The anti-clickjacking X-Frame-Options header is not present.
    + The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
    + Uncommon header 'x-backend-server' found, with contents: office.paper
    + The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
    + Retrieved x-powered-by header: PHP/7.2.24
    + Allowed HTTP Methods: GET, POST, OPTIONS, HEAD, TRACE 
    + OSVDB-877: HTTP TRACE method is active, suggesting the host is vulnerable to XST
    + OSVDB-3092: /manual/: Web server manual found.

    + OSVDB-3268: /icons/: Directory indexing found.
    + OSVDB-3268: /manual/images/: Directory indexing found.
    + OSVDB-3233: /icons/README: Apache default file found.

    + 8699 requests: 0 error(s) and 11 item(s) reported on remote host
    + End Time:           2022-05-03 17:30:48 (GMT-4) (1596 seconds)
    ---------------------------------------------------------------------------
    + 1 host(s) tested

`x-backend-server : office.paper` seems interesting, let's assume it is a vhost and add $IP and office.paper as hostname in `/etc/hosts`

Let's browse to `http://office.paper` and we can see that it is a forum page for a paper company. `Wappalyzer` indicate that the webpage is powered by `Wordpress 5.2.3`, probably it will have some vulnerabilities with this version? `searchsploit` is the way to find out

    └─$ searchsploit wordpress 5.2.3 -w
    ---------------------------------------------------------------------- --------------------------------------------
     Exploit Title                                                        |  URL
    ---------------------------------------------------------------------- --------------------------------------------
    WordPress Core 5.2.3 - Cross-Site Host Modification                   | https://www.exploit-db.com/exploits/47361
    WordPress Core < 5.2.3 - Viewing Unauthenticated/Password/Private Pos | https://www.exploit-db.com/exploits/47690
    WordPress Core < 5.3.x - 'xmlrpc.php' Denial of Service               | https://www.exploit-db.com/exploits/47800
    WordPress Plugin DZS Videogallery < 8.60 - Multiple Vulnerabilities   | https://www.exploit-db.com/exploits/39553
    WordPress Plugin iThemes Security < 7.0.3 - SQL Injection             | https://www.exploit-db.com/exploits/44943
    WordPress Plugin Rest Google Maps < 7.11.18 - SQL Injection           | https://www.exploit-db.com/exploits/48918
    ---------------------------------------------------------------------- --------------------------------------------
    Shellcodes: No Results
    Papers: No Results

[This link](https://www.exploit-db.com/exploits/47690) has some interesting tricks to reveal hidden content and with that we found a secret registration link to the paper company's employee chat system. Let's register that link hostname to /etc/hosts before we proceed further.

After registration, we are in the chat system and by going through the messages we knkow that we are able to interact with the bot to do two important things. To get files and to list files.


