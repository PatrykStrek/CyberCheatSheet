# 06 · Utilities

[← Back to index](README.md)

---

## Wordlists and locations (Kali / Parrot)

| Use | Path |
|---|---|
| Directory fuzzing (small) | `/usr/share/wordlists/dirb/common.txt` |
| Directory fuzzing (medium) | `/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt` |
| Passwords | `/usr/share/wordlists/rockyou.txt` |

`rockyou.txt` ships compressed on Kali; decompress it once:
```bash
sudo gzip -d /usr/share/wordlists/rockyou.txt.gz
```

---

## I need to move a file to or from the target

> **TODO:** `python3 -m http.server` + `wget`/`curl`, `certutil` / `Invoke-WebRequest` on Windows, `nc`, `scp`, SMB server with `impacket-smbserver`.

---

## Useful one-liners

> **TODO:** add one-liners as I actually use them.
