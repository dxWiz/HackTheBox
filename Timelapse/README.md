
└─$ nmap -sC -sV -Pn 10.10.11.152 -oN initial.nmap

    # Nmap 7.92 scan initiated Wed Apr 27 10:30:54 2022 as: nmap -sC -sV -Pn -oN initial.nmap 10.10.11.152
    Nmap scan report for 10.10.11.152
    Host is up (0.019s latency).
    Not shown: 989 filtered tcp ports (no-response)
    PORT     STATE SERVICE           VERSION
    53/tcp   open  domain            Simple DNS Plus
    88/tcp   open  kerberos-sec      Microsoft Windows Kerberos (server time: 2022-04-27 22:31:05Z)
    135/tcp  open  msrpc             Microsoft Windows RPC
    139/tcp  open  netbios-ssn       Microsoft Windows netbios-ssn
    389/tcp  open  ldap              Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
    445/tcp  open  microsoft-ds?
    464/tcp  open  kpasswd5?
    593/tcp  open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
    636/tcp  open  ldapssl?
    3268/tcp open  ldap              Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
    3269/tcp open  globalcatLDAPssl?
    Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

    Host script results:
    |_clock-skew: 7h59m59s
    | smb2-security-mode: 
    |   3.1.1: 
    |_    Message signing enabled and required
    | smb2-time: 
    |   date: 2022-04-27T22:31:13
    |_  start_date: N/A

    Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    # Nmap done at Wed Apr 27 10:31:49 2022 -- 1 IP address (1 host up) scanned in 55.41 seconds

smbclient -L //10.10.11.152 -N

      Sharename       Type      Comment
      ---------       ----      -------
      ADMIN$          Disk      Remote Admin
      C$              Disk      Default share
      IPC$            IPC       Remote IPC
      NETLOGON        Disk      Logon server share 
      Shares          Disk      
      SYSVOL          Disk      Logon server share 
    Reconnecting with SMB1 for workgroup listing.
    do_connect: Connection to 10.10.11.152 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
    Unable to connect with SMB1 -- no workgroup available

Connect to `Shares` folder
`└─$ smbclient \\\\10.10.11.152\\Shares -N`

Once you are in, `mget` all the files in Shares folder. Use `prompt` to get rid of the yes/no confirmation. 

    smb: \> help prompt
    HELP prompt:
      toggle prompting for filenames for mget and mput
