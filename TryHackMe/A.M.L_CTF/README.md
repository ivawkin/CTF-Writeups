# A.M.L. CTF Walkthrough 🧠💻


## 📡 Port Scanning with Nmap

Started off with a full port scan:


nmap -p- <IP>

Open Ports:

    80/tcp - HTTP (Apache)

    6498/tcp - OpenSSH

    65524/tcp - Another HTTP service (Apache 2.4.43)

    Not shown: 65,532 closed ports.

🔍 Enumeration
HTTP (port 80) - Feroxbuster to the rescue

Ran Feroxbuster and found a few interesting directories:

    /hidden/whatever → led to our first flag 🎯

🏁 First Flag

Found directly in the source of /hidden/whatever. Easy grab!
🕵️‍♂️ Second Flag - Robots.txt with attitude

In /robots.txt, saw something weird:

User-Agent:*
Disallow:/
User-Agent:a18672860d0510e5ab6699730763b250
Allow:/

👀 That “User-Agent” looked like a hash. Ran it through md5hashing.net and cracked it.

➡️ Second flag found!
Cracking MD5 was the only way here.

🧩 Third Flag

In another HTML source, found this hash:

9fdafbd64c47471a8f54cd3fc64cd312

Used Hashcat with a wordlist to crack it:

hashcat -m 0 hash.txt /path/to/wordlist

🧠 Possible user from clues: candeger
🔐 Hidden Directory

At http://aml.thm:65524/, source had:

<p hidden>its encoded with ba....:ObsJmP173N2X6dOrAgEAL0Vu</p>

Looks like Base62. Decoded it at dcode.fr → got:

/n0th1ng3ls3m4tt3r

Inside, we found another hash:

940d71e8655ac41efb5f8ab850668505b86dd64186a66e57d1483e7f5fe6fd81

Ran John on it (GOST hash format):

john --wordlist=easypeasy.txt --format=gost hash.txt

Cracked to: mypasswordforthatjob 🔓
🖼️ Hidden Image Shenanigans

Saw a sneaky image in the page source:

<img src="binarycodepixabay.jpg" />

Downloaded with wget and extracted with steghide:

steghide extract -sf binarycodepixabay.jpg

🎉 Got a username inside!

Then used CyberChef to decrypt more binary → revealed:

iconvertedmypasswordtobinary

Looks like potential SSH creds ✅
🧍‍♂️ User Flag – But Twisted

Got the user flag, but it seemed rotated:

synt{a0jvgf33zfa0ez4y}

Might need further decoding or reversing — still suspicious!
🚀 Privilege Escalation – Root Time!

linpeas.sh helped us dig up something spicy:

/var/www/.mysecretcronjob.sh

The contents:

#!/bin/bash
# i will run as root

Looks like we can exploit this cronjob to get root access 😈
✅ Status: Pwned.

All flags captured. Mission accomplished.

    CTFs like this are a great reminder that no matter how hidden something looks... it's just a matter of knowing where to look 😉
