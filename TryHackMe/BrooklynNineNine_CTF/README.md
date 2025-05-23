# TryHackMe: NineNine CTF Walkthrough

## Overview

This CTF from TryHackMe involves enumeration via anonymous FTP, HTML source code analysis, steganography, and basic privilege escalation to gain initial user access via SSH.

---

## Recon

### Open Ports Discovered (via rustscan):
- **21** – FTP (Anonymous login allowed)
- **22** – SSH
- **80** – HTTP

---

## Enumeration

### FTP Access

- Anonymous login permitted
- Found file: `note_to_jake.txt`

#### Contents:
> Jake, please change your password. It is too weak and Holt will be mad if someone hacks into the Nine Nine.

🧠 **Users Identified**:
- Jake
- Amy
- Holt

---

### Web Enumeration

Used **feroxbuster**:

feroxbuster -u http://target-ip

    Only default pages found: index.html and html/

Source Code Review

Examined index.html and found this comment:

<!-- Have you ever heard of steganography? -->

🔍 Hinting at steganography — concealing information inside images.
Steganography & Password Discovery

Used stegseek with rockyou wordlist:

stegseek myimage.jpg /usr/share/wordlists/rockyou.txt

📄 Extracted file: note.txt
Contents:

Holt's Password:
fluffydog12@ninenine

SSH Access

Used Holt’s credentials to SSH in:

ssh holt@target-ip

✅ Successfully logged in

🧾 user.txt flag obtained!
Next Step

🔍 Begin privilege escalation to root.
Summary
Stage	Method/Tool	Outcome
Port Scanning	rustscan	Discovered FTP, SSH, HTTP
FTP Enumeration	Anonymous login	Found note hinting weak password
Web Inspection	feroxbuster + HTML source	Steganography hint
Steganography Crack	stegseek + rockyou	Retrieved Holt's password
SSH Access	Password login as holt	User flag captured
