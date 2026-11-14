# 03 · Forensics

[← Back to index](README.md)

General rule: **work on a copy** of the evidence and record its hash before you start, so you can prove it was not modified.
```bash
sha256sum evidence.img > evidence.img.sha256
sha256sum -c evidence.img.sha256     # verify later
```

---

## I have a disk image

### Analyse it with Autopsy
Autopsy is a GUI on top of The Sleuth Kit: it parses the file system, recovers deleted files and extracts artifacts automatically.
1. **New Case** → name and output folder.
2. **Add Data Source → Disk Image or VM File** (raw/`dd`, E01, VHD, VMDK…).
3. Select the **ingest modules**. Most useful ones:
   - **Recent Activity**: browser history, recent documents, USB devices, installed programs (from the registry).
   - **Hash Lookup**: flags known-good / known-bad files using hash sets.
   - **File Type Identification** + **Extension Mismatch Detector**: finds files whose extension lies about their content.
   - **Embedded File Extractor**: opens ZIPs, Office documents, etc.
   - **Picture Analyzer**: EXIF metadata (GPS, camera, dates).
   - **Keyword Search**: indexes text so you can search for strings and regex (emails, URLs, `password`…).
   - **Data Source Integrity**: computes/verifies the image hash.
4. Review the results in the left tree:
   - **Views → File Types / Deleted Files / File Size**.
   - **Data Artifacts** and **Analysis Results** (older versions: **Results → Extracted Content**).
   - **Keyword Hits**.
5. **Tools → Timeline** to see what happened around a suspicious time.
6. **Tag** relevant files and generate a report (**Generate Report**).

### Analyse it from the command line (The Sleuth Kit)
> **TODO:** `mmls` (partitions), `fls` (list files, including deleted), `icat` (extract by inode), `tsk_recover`.

---

## I have a network capture (.pcap / .pcapng)

### Get an overview first (Wireshark)
Before reading packets one by one, find out which protocols and hosts are in the capture.
- **Statistics → Protocol Hierarchy**: which protocols appear and in what proportion.
- **Statistics → Conversations**: who talks to whom and how much data (sort by bytes).
- **Statistics → Endpoints**: list of IPs/hosts.

### Filter the traffic (display filters)
Display filters reduce thousands of packets to the ones that matter.
```text
ip.addr == 10.10.10.10              # traffic to/from a host
tcp.port == 80                      # a specific port
http.request                        # only HTTP requests
http.request.method == "POST"       # forms, logins, uploads
dns                                 # domain lookups
ftp || ftp-data                     # FTP commands and transferred data
frame contains "password"           # raw string search in any packet
tcp.flags.syn == 1 && tcp.flags.ack == 0   # connection attempts (port scans)
!(arp || dns)                       # hide noise
```

### Read a whole conversation
**Right-click a packet → Follow → TCP Stream** (or HTTP Stream). Shows the full exchange as text, which is where commands, credentials and file contents become readable. The filter `tcp.stream eq <n>` jumps between streams.

### Extract transferred files
**File → Export Objects → HTTP** (also SMB, TFTP, IMF). Lists files sent over those protocols and lets you save them.

### Find cleartext credentials
**Tools → Credentials** lists credentials Wireshark detected in protocols such as FTP, HTTP basic auth or Telnet.

### Analyse it from the command line (tshark)
> **TODO:** `tshark -r capture.pcap -Y '<filter>'`, extracting fields with `-T fields -e`, `--export-objects`.

---

## I have a memory dump

### Volatility 3 – first commands
Volatility rebuilds the processes, network connections and files that were in RAM when the dump was taken.
```bash
vol -f mem.raw windows.info                 # OS version, confirms the dump is readable
vol -f mem.raw windows.pslist               # running processes
vol -f mem.raw windows.pstree               # processes as a tree (spot odd parents)
vol -f mem.raw windows.cmdline              # command line of each process
vol -f mem.raw windows.netscan              # network connections and listening ports
vol -f mem.raw windows.filescan | grep -i <name>                      # find a file in memory
vol -f mem.raw -o out/ windows.dumpfiles --virtaddr <address>         # extract it
```
- `-f`: memory image · `-o`: output folder for extracted files (goes **before** the plugin).
- `vol` is the command when installed with pip; from the repo it is `python3 vol.py`.
- `windows.pstree` + `windows.cmdline` are usually the fastest way to spot a malicious process.

> **TODO:** `windows.malfind`, `windows.hashdump`, registry plugins, Linux dumps (need symbol tables).

---

## I have logs

> **TODO:** Linux `auth.log` / `journalctl`, Windows Event Logs (key Event IDs), Apache/Nginx access logs, `grep`/`awk` patterns.

---

## I have a file that hides something (metadata & steganography)

### Metadata
> **TODO:** `exiftool`, `strings`, `file`.

### Embedded files and steganography
> **TODO:** `binwalk -e`, `steghide extract`, `zsteg`, checking magic bytes with `xxd`.
