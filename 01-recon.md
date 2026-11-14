# 01 · Reconnaissance & Enumeration

[← Back to index](README.md)

Placeholders: `<IP>` = target, `<PORT>` = target port.

---

## I only have an IP address

### Step 1 – Find every open TCP port (fast)
Scan all 65,535 ports quickly so nothing is missed; service detection comes later and only on what is open.
```bash
nmap -p- --min-rate 5000 -T4 <IP> -oN allports.txt
```
- `-p-`: all 65,535 TCP ports (default is only the top 1,000).
- `--min-rate 5000`: send at least 5,000 packets/s. Fine in labs; too aggressive for real networks.
- `-T4`: faster timing template.
- `-oN <file>`: save normal output so you don't have to rescan.
- `-Pn`: add it if the host seems down (common on Windows targets that block ping).

### Step 2 – Identify services on the open ports
Run version detection and default scripts only on the ports found in step 1.
```bash
nmap -p <PORT1>,<PORT2> -sC -sV <IP> -oN targeted.txt
```
- `-sV`: probes each port to identify the service and its version.
- `-sC`: runs the default NSE scripts (e.g. reports anonymous FTP, page titles, SMB info).
- `-A`: = `-sV -sC -O --traceroute`. Use it **instead of** `-sC -sV`, not together with them.

Optional: build the port list automatically.
```bash
ports=$(nmap -p- --min-rate 5000 -T4 <IP> | grep '^[0-9]' | cut -d'/' -f1 | paste -sd,)
nmap -p "$ports" -sC -sV <IP> -oN targeted.txt
```

---

## There is a web server (80/443/8080…)

### The site redirects to a domain name (e.g. `http://example.htb`)
The server uses name-based virtual hosting, so your machine must resolve that name to the target IP.
```bash
echo "<IP> example.htb" | sudo tee -a /etc/hosts
```

### Find hidden directories and files
Brute-force paths with a wordlist to discover content that isn't linked from the site.
```bash
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt
ffuf -u http://<IP>/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
```
- `gobuster -x php,txt,html`: also try each word with these extensions.
- `ffuf` replaces the keyword `FUZZ` with each word in the list.
- `ffuf -fs <size>`: hide responses of a given size (useful when every path returns the same "not found" page with code 200).
- Bigger lists: see [06-utilities.md](06-utilities.md#wordlists-and-locations-kali--parrot).

### Find subdomains / virtual hosts
> **TODO:** vhost fuzzing with `ffuf -H "Host: FUZZ.<domain>"` and filtering by response size, DNS subdomain enumeration.

---

## I found a specific port open

### FTP (21)
Often allows anonymous login; `nmap -sC` reports it as `ftp-anon: Anonymous FTP login allowed`.
```bash
ftp <IP>
```
- Anonymous login: user `anonymous`, blank password.
- `ls` / `dir`: list files · `get <file>`: download · `put <file>`: upload.

### Telnet (23)
Unencrypted remote login, frequently left with default or empty credentials.
```bash
telnet <IP>
```
- Try common users (`root`, `admin`) with a blank password.

### SMB (139/445)
Windows file sharing (Samba on Linux). First check which shares are readable without credentials.
```bash
smbclient -L //<IP>/ -N
smbclient //<IP>/<ShareName> -N
```
- `-L`: list shares.
- `-N`: null session (no password prompt).
- Inside a share: `ls`, `get <file>`.

### Redis (6379)
In-memory key-value database, sometimes exposed without authentication.
```bash
redis-cli -h <IP>
```
- `info`: server information · `info keyspace`: which databases contain keys.
- `select <n>`: switch to database `n` (default is 0).
- `keys *`: list all keys · `get <key>`: read a key.

### MySQL / MariaDB (3306)
Check whether the database accepts `root` without a password.
```bash
mysql -h <IP> -u root        # no password
mysql -h <IP> -u root -p     # prompts for password
```
```sql
SHOW DATABASES;
USE <database>;
SHOW TABLES;
SELECT * FROM <table>;
```

### WinRM (5985/5986)
Remote shell on Windows, but only with valid credentials → see [05-exploitation-privesc.md](05-exploitation-privesc.md#i-have-credentials-and-winrm-is-open).
