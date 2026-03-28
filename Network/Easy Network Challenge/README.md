# Easy Network Challenge

**Category:** Network
**Points:** 15
**Difficulty:** Easy

## Overview

A basic network packet capture is provided. The flag is embedded directly within the network traffic and can be recovered through simple packet inspection.

**Provided Files:**
- `42tirana_easy.pcap` — Packet capture (TCPDUMP format)

---

## Technical Analysis

The challenge description contains the following C source code as a hint:

```c
#include <stdio.h>

int main() { printf("Hello, World!\n"); return 0; }
```

This suggests that the flag may be transmitted as a plaintext string within the capture, analogous to a simple "Hello, World!" output. The transmission is likely unencrypted and directly readable from the packet payload.

---

## Solving Approach

### Step 1 — Open the capture

```bash
wireshark 42tirana_easy.pcap
```

### Step 2 — Search for strings in the payload

Apply a Wireshark display filter to search for readable content:

```
frame contains "flag"
```

Or use tshark to extract all packet payloads as strings:

```bash
tshark -r 42tirana_easy.pcap -T fields -e data.text
```

### Step 3 — Follow TCP/UDP streams

Right-click any data-carrying packet and select **Follow → TCP Stream** (or UDP Stream) to view the full conversation in readable form.

### Step 4 — Search raw bytes for flag pattern

```bash
strings 42tirana_easy.pcap | grep -i "flag\|42tirana\|ctf"
```

---

## Expected Answer Format

Standard flag format used throughout the competition, likely transmitted in plaintext over a simple protocol.
