# 42 Tirana BCD26 CTF Challenges

This repository contains Capture The Flag (CTF) challenges organized across eight categories. Each challenge is designed to test specific skills in digital forensics, network analysis, steganography, and low-level problem-solving.

## Challenge Categories

| Category | Challenges | Total Points |
|---|---|---|
| [Email Analysis](./Email%20Analysis/) | 11 questions | Variable |
| [Forensics](./Forensics/) | 5 challenges | 275 |
| [Logs Analysis](./Logs%20Analysis/) | 5 questions | 220 |
| [Low Level](./Low%20Level/) | 4 questions | 40 |
| [Misc](./Misc/) | 3 challenges | 160 |
| [Network](./Network/) | 4 challenges | 185 |
| [Network Forensics](./Network%20Forensics/) | 5 challenges | 180 |
| [Steganography](./Steganography/) | 6 challenges | 360 |

---

## Email Analysis

Multi-part challenge based on a suspicious MIME email containing hidden attachments with multi-layer encoding and encrypted archives. Requires email parsing, base64 decoding, UU decoding, ZIP extraction, ROT47 decoding, and credential harvesting.

**Files:** `email.txt`, `Email Analysis.txt`

---

## Forensics

A series of digital forensics challenges covering disk imaging, memory analysis, packet capture analysis, and log review. Each sub-challenge provides a binary artifact (image, PCAP, or archive) and a specific question.

**Sub-challenges:**
- [Deleted Evidence](./Forensics/Deleted%20Evidence/) — 60 pts — Disk image, file recovery
- [Initial Access](./Forensics/Initial%20Access/) — 20 pts — Network PCAP, HTTP analysis
- [Memory Secrets](./Forensics/Memory%20Secrets/) — 50 pts — Memory dump, command extraction
- [The Hidden Gem](./Forensics/The%20Hidden%20Gem/) — 35 pts — Payload download recovery
- [The Mastermind](./Forensics/The%20Mastermind/) — 120 pts — Server log analysis

---

## Logs Analysis

A five-part challenge based on Windows Event Log analysis. A compromised Firefox browser installation is examined to determine the vulnerability, payload, and execution chain.

**Files:** `event_logs_firefox.evtx`, `Logs Analysis.txt`

---

## Low Level

Open-source intelligence (OSINT) challenges requiring research and public record analysis related to 42 Tirana and the AADF organization.

**Files:** `Low Level.txt`

---

## Misc

Three miscellaneous challenges of escalating difficulty, each involving a ZIP archive that must be analyzed to find a configuration path, a hidden feature toggle, or decrypted content.

**Sub-challenges:**
- [Config Path](./Misc/Config%20Path/) — 10 pts — Configuration analysis
- [Feature Toggle](./Misc/Feature%20Toggle/) — 50 pts — Feature flag analysis
- [Wrong Key](./Misc/Wrong%20key/) — 100 pts — Decoy/key file analysis

---

## Network

Packet capture analysis challenges of increasing difficulty. Topics include plaintext protocol inspection, HTTP traffic analysis, DNS record leakage, and flag recovery from encrypted or fragmented sessions.

**Sub-challenges:**
- [Easy Network Challenge](./Network/Easy%20Network%20Challenge/) — 15 pts
- [Network Medium Challenge](./Network/Network%20Medium%20Challenge/) — 30 pts
- [Network Hard Challenge](./Network/Network%20Hard%20Challenge/) — 40 pts
- [Network Very Hard Challenge](./Network/Network%20Very%20Hard%20Challenge/) — 100 pts

---

## Network Forensics

Focused network forensics challenges that analyze specific attack scenarios: a UDP DDoS attack, session hijacking, and malware delivery over a captured network session.

**Sub-challenges:**
- [Network 1](./Network%20Forensics/Network%201/) — 50 pts — DDoS attacker identification
- [Network 2](./Network%20Forensics/Network%202/) — 50 pts — Session hijacking victim identification
- [Network 3, 4, 5](./Network%20Forensics/Network%203%2C%204%2C%205/) — 80 pts combined — Traffic analysis and malware hash

---

## Steganography

Challenges involving data hidden within image and audio files. Techniques span LSB steganography, multi-layer encoding, Morse code in audio, and metadata analysis.

**Sub-challenges:**
- [Stegano 1](./Steganography/Stegano%201/) — 20 pts — Image steganography
- [Lost in the Maze](./Steganography/Lost%20in%20the%20Maze/) — 25 pts — Fuzzing / brute-force
- [Checkmate Bits](./Steganography/Checkmate%20Bits/) — 30 pts — Bit-level analysis
- [Old Comm](./Steganography/Old%20Comm/) — 35 pts — Audio / Morse code
- [Hard...or very hard](./Steganography/Hard...or%20very%20hard%20(Not%20completed)/) — 100 pts — JPEG steganography
- [Layers of 42](./Steganography/Layers%20of%2042/) — 150 pts — Multi-layer steganography

---

## Tools Reference

The following tools are commonly required across these challenges:

| Tool | Purpose |
|---|---|
| Wireshark / tshark | Packet capture analysis |
| Volatility | Memory forensics |
| Autopsy / Sleuth Kit | Disk forensics |
| steghide, zsteg, stegsolve | Steganography extraction |
| Audacity, Sonic Visualiser | Audio steganography |
| 7-Zip | Encrypted archive extraction |
| Python (base64, hashlib) | Encoding / decoding / hashing |
| EventViewer / evtx-dump | Windows Event Log parsing |
| ExifTool | File metadata inspection |
