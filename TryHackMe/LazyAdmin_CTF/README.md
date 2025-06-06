# TryHackMe: LazyAdmin CTF Walkthrough

## Overview

This box involves a CMS called **SweetRice**, which is running on a web server. We'll use basic enumeration and a known exploit to gain access, then escalate to root.

---

## Open Ports

- **22** - SSH  
- **80** - HTTP (Web Server)

---

## Initial Enumeration

### Website Observations

- Visiting `http://lazyadmin.thm` reveals a **Basic CMS SweetRice** site.
- It shows a "site is building" message – a sign that the CMS is not fully configured.

### Directory Brute-Forcing

Using `feroxbuster` or `dirb` reveals:

- `http://lazyadmin.thm/content/as/`
- `http://lazyadmin.thm/content/`

---

## Exploitation

### Step 1: Vulnerability

We find that the site is using **SweetRice CMS**, which has a known vulnerability:
- 📎 [Exploit-DB 40718](https://www.exploit-db.com/exploits/40718)

This allows us to upload files if we find an admin panel or a misconfigured directory.

---

### Step 2: Database Backup Found

We find a backup file here:
- `http://lazyadmin.thm/content/inc/mysql_backup/`

Inside the backup, we find login information:

admin : 42f749ade7f9e195bf475f37a44cafcb

Step 3: Crack the Hash

Using hashes.com, we decrypt the hash:

    Password: Password123

Getting Access

With the manager : Password123 credentials, we can log in and upload a reverse shell to the ads directory (found during enumeration).

    Upload reverse shell (e.g., PHP).

    Start a listener on your machine:

    nc -nvlp 1234

    Visit the shell file in your browser – and we have a shell!

Privilege Escalation

To become root:

    Create a copy script:

echo "cp /bin/bash /tmp/rootbash; chmod +s /tmp/rootbash" > /etc/copy.sh

Run this command as sudo:

sudo /usr/bin/perl /home/itguy/backup.pl

Then gain root access:

    /tmp/rootbash -p

