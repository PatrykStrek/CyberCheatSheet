# CyberCheatSheet

Personal quick-reference for solving Hack The Box machines and CTF challenges, with a focus on **web exploitation** and **forensics**.

It started as notes from the Hack The Box Starting Point tiers and grows as I practise. It is organised by **situation** ("what do I do if I find X?") rather than by tool, so it can be used in the middle of a challenge.

---

## Contents

| File | Covers |
|---|---|
| [01 · Reconnaissance & Enumeration](01-recon.md) | Port scanning with nmap, web directory fuzzing, enumerating FTP, Telnet, SMB, Redis, MySQL |
| [02 · Web Exploitation](02-web.md) | Burp Suite basics, login bypass (default creds, SQLi), IDOR, cookie manipulation |
| [03 · Forensics](03-forensics.md) | Disk images (Autopsy), network captures (Wireshark), memory dumps (Volatility 3) |
| [04 · Crypto & Password Cracking](04-crypto.md) | John the Ripper, Hashcat modes, cracking protected ZIPs and SSH keys |
| [05 · Exploitation & Privilege Escalation](05-exploitation-privesc.md) | Reverse shells, shell stabilisation, Evil-WinRM, Linux privesc (sudo, SUID) |
| [06 · Utilities](06-utilities.md) | Wordlists, file transfer, one-liners |

### Quick jumps
- [I only have an IP address](01-recon.md#i-only-have-an-ip-address)
- [There is a login form](02-web.md#there-is-a-login-form)
- [I have a network capture](03-forensics.md#i-have-a-network-capture-pcap--pcapng)
- [I have a hash](04-crypto.md#i-have-a-hash)
- [My shell is unstable](05-exploitation-privesc.md#my-shell-is-unstable)

---

## How to read it

Each entry follows the same format:

````markdown
### Situation or goal
One line: what the command does and why you would use it.
```bash
command <PLACEHOLDER>
```
- `-option`: what it changes.
````

- Values in `<ANGLE_BRACKETS>` are placeholders: `<IP>` is the target, `<LHOST>` / `<LPORT>` are your machine's IP and port.
- **`> TODO:`** marks topics I have not practised yet. They are filled in only after I have used them for real, so everything else here has been tested by me.

List all pending topics:
```bash
grep -rn "TODO" *.md
```

---

## Legal disclaimer

This repository is for **educational purposes only**. The techniques described must only be used against systems you own or have **explicit written permission** to test, such as Hack The Box, TryHackMe, CTF competitions or your own lab. Unauthorised access to computer systems is illegal.

In line with the [Hack The Box platform rules](https://help.hackthebox.com/en/articles/12325897-hack-the-box-platform-rules), this repository contains **no flags, passwords or solutions** for active machines or challenges, only general techniques.
