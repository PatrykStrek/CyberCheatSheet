# CyberCheatSheet

A quick-reference guide covering the core tools, commands, and concepts learned across the Hack The Box Starting Point tiers (Tier 0 - Tier 2).

---

## 1. Reconnaissance & Scanning

### Nmap (Network Mapper)
The fundamental tool for discovering open ports and running services.
* **Basic Scan:** `nmap <IP>`
* **Service Version Scan:** `nmap -sV <IP>`
* **Default Script Scan:** `nmap -sC <IP>`
* **All-in-One (Aggressive):** `nmap -p- -sV -sC -A <IP>`
  * `-p-`: Scans all 65,535 ports.
  * `-sV`: Probes open ports to determine service/version info.
  * `-sC`: Runs default nmap scripts.
  * `-A`: Enables OS detection, version detection, script scanning, and traceroute.

### Web Directory Enumeration
Finding hidden pages and directories on web servers.
* **Gobuster:** `gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt`
* **ffuf:** `ffuf -u http://<IP>/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt`

---

## 2. Service Enumeration & Access

### Telnet (Port 23)
Unencrypted text communications. Often left open with default or no credentials.
* **Connect:** `telnet <IP>`

### FTP - File Transfer Protocol (Port 21)
Often allows "Anonymous" access.
* **Connect:** `ftp <IP>`
* **Anonymous Login:** Use username `anonymous` and a blank password.
* **Commands:**
  * `ls` / `dir`: List files
  * `get <filename>`: Download a file to your machine
  * `put <filename>`: Upload a file

### SMB - Server Message Block (Ports 139, 445)
Windows file sharing (also on Linux via Samba).
* **List Shares:** `smbclient -L \\\\<IP>\\` (Leave password blank if prompted)
* **Connect to a Share:** `smbclient \\\\<IP>\\<ShareName>`
* **Download files:** `get <filename>`

### Redis (Port 6379)
In-memory database.
* **Connect:** `redis-cli -h <IP>`
* **Commands:**
  * `info`: Get server information
  * `keys *`: List all keys
  * `get <key_name>`: Read the contents of a specific key

### MySQL / MariaDB (Port 3306)
Relational database management.
* **Connect (No Password):** `mysql -h <IP> -u root`
* **Connect (With Password):** `mysql -h <IP> -u root -p`
* **Commands:**
  * `SHOW DATABASES;`
  * `USE <database_name>;`
  * `SHOW TABLES;`
  * `SELECT * FROM <table_name>;`

### WinRM (Port 5985/5986)
Windows Remote Management. Used to get a shell on Windows when credentials are known.
* **Connect using Evil-WinRM:** `evil-winrm -i <IP> -u <username> -p <password>`

---

## 3. Web Exploitation Basics

### Admin Panel Bypasses
* **Default Credentials:** Always try `admin:admin`, `admin:password`, `root:root`.
* **Basic SQL Injection (Login Bypass):** `admin' OR '1'='1` (Use as the username, leave password blank or repeat the payload).

### Burp Suite Fundamentals
* Intercepting traffic to modify cookies, parameters, or headers.
* **IDOR (Insecure Direct Object Reference):** Modifying a parameter (like `user_id=1` to `user_id=2`) to access another user's data.
* **Cookie Manipulation:** Decoding Base64 cookies or changing role values (e.g., `role=user` to `role=admin`).

---

## 4. Password & Hash Cracking

### Extracting Hashes from Files
* **Zip Files:** `zip2john file.zip > zip.hash`
* **SSH Keys:** `ssh2john id_rsa > ssh.hash`

### John The Ripper
* **Crack a hash:** `john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt`
* **Show cracked passwords:** `john --show hash.txt`

### Hashcat
* **Crack NTLMv2 or MD5:** `hashcat -m <hash_type_number> hash.txt /usr/share/wordlists/rockyou.txt`

---

## 5. Privilege Escalation (Linux)

Once you have a low-privileged shell on a Linux machine, check these to become root.

### The Basics
* **Check current user:** `whoami`
* **Check your permissions:** `sudo -l` (Shows what commands you can run as root without a password)

### SUID Binaries
Find files owned by root that execute with root privileges.
* **Find Command:** `find / -type f -perm -4000 2>/dev/null`
* **GTFOBins:** If you find an unusual SUID binary (or a binary in `sudo -l`), look it up on [GTFOBins](https://gtfobins.github.io/) for exploit commands.

### Upgrading a "Dumb" Shell
If your reverse shell is unstable or lacks basic features (like clear, tab-autocomplete):
1. `python3 -c 'import pty; pty.spawn("/bin/bash")'`
2. `export TERM=xterm`
3. Hit `Ctrl+Z` to background the shell.
4. `stty raw -echo; fg`

---

## 6. Important Wordlists & Locations (Kali / Parrot)

* **Directory fuzzing (Small):** `/usr/share/wordlists/dirb/common.txt`
* **Directory fuzzing (Medium):** `/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`
* **Passwords (The Holy Grail):** `/usr/share/wordlists/rockyou.txt` *(Note: you may need to unzip it first: `sudo gzip -d /usr/share/wordlists/rockyou.txt.gz`)*
