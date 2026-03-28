# The Hidden Gem

**Category:** Forensics
**Points:** 35

## Overview

An attacker downloaded a payload from a compromised server. The download was captured in a network packet trace. The objective is to reassemble and recover the downloaded file from the packet capture.

**Provided Files:**
- `payload_download.pcap` — Packet capture containing the payload download session

---

## Technical Analysis

Files transferred over unencrypted protocols (HTTP, FTP, TFTP) can be fully reconstructed from a packet capture by reassembling the TCP stream and stripping the protocol headers. Wireshark provides a built-in "Follow TCP/HTTP Stream" feature for this purpose.

Common payload delivery scenarios:
- HTTP GET request fetching a script, executable, or archive
- FTP data transfer of a binary file
- Base64-encoded content delivered via HTTP response body

---

## Solving Approach

### Step 1 — Open the capture in Wireshark

```bash
wireshark payload_download.pcap
```

### Step 2 — Locate the download session

Filter for HTTP traffic and identify the response that contains the payload:

```
http.response
```

Or search for large data transfers:

```
tcp.len > 1000
```

### Step 3 — Export the object via Wireshark

If the transfer used HTTP, use the built-in export function:

```
File → Export Objects → HTTP
```

Select the relevant entry and save the file.

### Step 4 — Reconstruct manually via TCP stream (alternative)

Right-click any packet in the download stream → **Follow → TCP Stream**. Switch the display to **Raw** mode, then save only the server-side response data.

```bash
tshark -r payload_download.pcap -Y "http.response" -T fields -e http.file_data > raw_payload
```

### Step 5 — Inspect the recovered file

```bash
file recovered_payload
xxd recovered_payload | head
strings recovered_payload
```

Identify the file type and examine its contents for the flag or further artifacts.

---

## Expected Answer Format

The answer is the recovered payload file itself or a specific value extracted from the file's content.
