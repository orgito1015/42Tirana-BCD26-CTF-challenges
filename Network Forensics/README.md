# Network Forensics

## Overview

This category contains five network forensics challenges that focus on analyzing specific attack scenarios captured in packet trace files. The challenges cover UDP-based DDoS attack analysis, TCP session hijacking investigation, and malware identification and hashing.

## Challenges

| Challenge | Points | File | Objective |
|---|---|---|---|
| [Network 1](./Network%201/) | 50 | `DD_attack.pcapng` | Identify the DDoS attacker IP address |
| [Network 2](./Network%202/) | 50 | `Jack.pcapng` | Identify the session hijacking victim IP |
| [Network 3](./Network%203%2C%204%2C%205/) | 20 | `chal.pcap` | Identify the IP with the most packet communications |
| [Network 4](./Network%203%2C%204%2C%205/) | 30 | `chal.pcap` | Identify the malware file name |
| [Network 5](./Network%203%2C%204%2C%205/) | 30 | `chal.pcap` | Compute the SHA1 hash of the malware file |

## Required Tools

- Wireshark
- tshark
- VirusTotal (for reputation checks)
- `sha1sum` / `md5sum`
