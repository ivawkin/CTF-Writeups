# Lian_Yu - TryHackMe Notes 🏝️

## 🔍 Ports & Services

| Port | Service | Notes |
|------|---------|-------|
| 21   | FTP     | vsftpd 3.0.2 |
| 22   | SSH     | OpenSSH 6.7p1 (Debian 5+deb8u8) |
| 80   | HTTP    | Web server running |
| 111  | RPC     | rpcbind - version 2-4 |
| 58661| RPC     | Possibly related service |

---

## 🕵️ Enumeration Phase

Started poking around the HTTP service on port 80 and found the `/island/` directory.

### `/island/`  
👤 Found a possible username from the HTML: **vigilante** (codename)

The page also mentioned:
- 🧭 A plan involving **Lian_Yu**
- 🕵️ Code word: `vigilante`

Looks like a breadcrumb to follow.

---

### `/island/2100`  
Another HTML doc with a video embedded, titled:

> _"How Oliver Queen finds his way to Lian_Yu"_

Hidden comment in the source hinted at:
> _"you can avail your `.ticket` here but how?"_

---

## 🧠 Gobuster Time

Started fuzzing extensions — `.ticket` turned out interesting.

gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
-u http://lianyu.thm/island/2100/ -x .ticket -k -t 20

Boom 💥 Found:
➡️ /green_arrow.ticket
🚢 Queen’s Gambit Token

The file had a base58-encoded string:

Token: RTy8yhBQdscX
🔓 Decoded to: !#th3h00d
🔗 FTP Access

Based on the token + hints:

Username: vigilante
Password: !#th3h00d

Looks like valid creds for FTP.
💻 SSH Guesswork

Could also be valid SSH creds. Trying combinations:

    vigilante : !#th3h00d

    slade : M3tahuman ← seems like a solid candidate too
