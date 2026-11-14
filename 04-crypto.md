# 04 · Crypto & Password Cracking

[← Back to index](README.md)

---

## I have a hash

### Identify the hash type
> **TODO:** `hashid` / `name-that-hash`, recognising prefixes (`$1$`, `$6$`, `$2y$`…) and lengths.

### Crack it with John the Ripper
John detects many formats automatically and is the quickest option for hashes extracted with the `*2john` tools.
```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
john --show hash.txt
```
- `--wordlist`: dictionary attack with the given list.
- `--format=<format>`: force the format when auto-detection picks the wrong one.
- `--show`: print cracked passwords. John saves results in `~/.john/john.pot`, so running the same hash again prints nothing: use `--show`.

### Crack it with Hashcat
Hashcat needs the hash mode (`-m`) explicitly and is much faster on a GPU.
```bash
hashcat -m <mode> -a 0 hash.txt /usr/share/wordlists/rockyou.txt
hashcat -m <mode> hash.txt --show
```
- `-a 0`: straight dictionary attack (the default).
- Find a mode: `hashcat --help | grep -i <name>`.

| Hash | Mode |
|---|---|
| MD5 | `0` |
| SHA-1 | `100` |
| SHA-256 | `1400` |
| md5crypt (`$1$`) | `500` |
| sha512crypt (`$6$`, Linux `/etc/shadow`) | `1800` |
| bcrypt (`$2a$`, `$2b$`, `$2y$`) | `3200` |
| NTLM (Windows SAM / NTDS) | `1000` |
| NetNTLMv2 (captured with Responder) | `5600` |

> NTLM and NetNTLMv2 are **not** the same: NTLM is the stored hash (usable for pass-the-hash), NetNTLMv2 is a challenge-response captured from the network (can only be cracked).

---

## I have a password-protected file

### Convert it to a crackable hash
The `*2john` tools extract the hash from the file so John (or Hashcat) can attack it.
```bash
zip2john file.zip > zip.hash
ssh2john id_rsa > ssh.hash
john --wordlist=/usr/share/wordlists/rockyou.txt zip.hash
```
- On some distros the script is `/usr/share/john/ssh2john.py` instead of `ssh2john`.
- After cracking an SSH key: `chmod 600 id_rsa` before using it, or SSH will refuse it.

---

## I have encoded or encrypted text

### Identify and decode encodings (CyberChef)
> **TODO:** Base64/Base32/hex/URL recognition, CyberChef "Magic", chaining recipes.

### Classical ciphers
> **TODO:** Caesar/ROT13, Vigenère, XOR, substitution; dcode.fr.

### RSA in CTFs
> **TODO:** small `e`, factorable `n` (factordb), common modulus, `RsaCtfTool`.
