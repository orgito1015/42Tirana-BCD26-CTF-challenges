# Network

## Overview

This category contains four packet capture analysis challenges of increasing difficulty. Each challenge requires inspection of a provided `.pcap` file to extract or reconstruct a flag. Topics include protocol analysis, HTTP traffic inspection, DNS record leakage, and reconstruction of obfuscated network sessions.

## Challenges

| Challenge | Points | File | Objective |
|---|---|---|---|
| [Easy Network Challenge](./Easy%20Network%20Challenge/) | 15 | `42tirana_easy.pcap` | Basic packet analysis |
| [Network Medium Challenge](./Network%20Medium%20Challenge/) | 30 | `42tirana_medium.pcap` | HTTP traffic inspection |
| [Network Hard Challenge](./Network%20Hard%20Challenge/) | 40 | `42tirana_hard.pcap` | DNS record leakage analysis |
| [Network Very Hard Challenge](./Network%20Very%20Hard%20Challenge/) | 100 | `42tirana_veryhard.pcap` | Flag recovery from complex session |

## Required Tools

- Wireshark
- tshark (command-line Wireshark)
- Python (`scapy`, `dpkt`)
- `strings`, `xxd`
