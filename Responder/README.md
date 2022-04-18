***

Author : dxWiz  
Date : 12 Apr 2022  
Level : Very Easy  

***

First of all connect to Hack The Box network via VPN and spanw an instance of the machine.

# Task 1

How many TCP ports are open on the machine?

    └─$ nmap 10.129.92.2 -p- -oN initial.nmap
    Starting Nmap 7.92 ( https://nmap.org ) at 2022-04-18 10:31 EDT
    Stats: 0:00:18 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
    Connect Scan Timing: About 2.09% done; ETC: 10:46 (0:14:02 remaining)
    Nmap scan report for 10.129.92.2
    Host is up (0.28s latency).
    Not shown: 65533 filtered tcp ports (no-response)
    PORT     STATE SERVICE
    80/tcp   open  http
    5985/tcp open  wsman

    Nmap done: 1 IP address (1 host up) scanned in 540.06 seconds


# Task 2

When visiting the web service using the IP address, what is the domain that we are being redirected to?

# Task 3

Which scripting language is being used on the server to generate webpages?

# Task 4

What is the name of the URL parameter which is used to load different language versions of the webpage?

# Task 5

Which of the following values for the `page` parameter would be an example of exploiting a Local File Include (LFI) vulnerability: "french.html", "//10.10.14.6/somefile", "../../../../../../../../windows/system32/drivers/etc/hosts", "minikatz.exe"

# Task 6

Which of the following values for the `page` parameter would be an example of exploiting a Remote File Include (RFI) vulnerability: "french.html", "//10.10.14.6/somefile", "../../../../../../../../windows/system32/drivers/etc/hosts", "minikatz.exe"

# Task 7

What does NTLM stand for?

# Task 8

Which flag do we use in the Responder utility to specify the network interface?

# Task 9

There are several tools that take a NetNTLMv2 challenge/response and try millions of passwords to see if any of them generate the same response. One such tool is often referred to as `john`, but the full name is what?.

# Task 10

What is the password for the administrator user?

# Task 11

We'll use a Windows service (i.e. running on the box) to remotely access the Responder machine using the password we recovered. What port TCP does it listen on?

# Submit Flag

Submit root flag 
