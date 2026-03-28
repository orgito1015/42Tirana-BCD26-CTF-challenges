# Network 3, 4, 5 — Traffic Analysis and Malware Identification

**Category:** Network Forensics
**Points:** 20 + 30 + 30 = 80

## Overview

Three linked challenges share a single packet capture file. The challenges progress from basic traffic statistics to malware identification and hash computation.

**Provided Files:**
- `chal.pcap` — Packet capture (TCPDUMP format)

---

## Challenge 3 — Most Active Communicator (20 pts)

### Question

Which IP address has performed the most packet communications?

### Technical Analysis

Packet volume per IP can be computed by aggregating both source and destination packet counts. The IP with the highest combined count is the most active communicator.

### Solving Approach

#### Step 1 — Compute per-IP packet counts

```bash
tshark -r chal.pcap -T fields -e ip.src | sort | uniq -c | sort -rn | head -10
tshark -r chal.pcap -T fields -e ip.dst | sort | uniq -c | sort -rn | head -10
```

#### Step 2 — Use Wireshark statistics

```
Statistics → Endpoints → IPv4 tab
```

Sort by total packets (Tx Packets + Rx Packets). The IP at the top is the answer.

```bash
tshark -r chal.pcap -q -z endpoints,ip | sort -rn -k2 | head -10
```

### Expected Answer

An IPv4 address.

---

## Challenge 4 — Malware File Name (30 pts)

### Question

This IP has a bad reputation and a file that is malware. What is the name of that file?

### Technical Analysis

The IP identified in Challenge 3 (or a related IP with known bad reputation) served or transmitted a malicious file. The file is likely transferred over HTTP and can be extracted from the packet capture. Cross-referencing the IP with threat intelligence sources (e.g., VirusTotal, AbuseIPDB) will confirm the malicious reputation.

### Solving Approach

#### Step 1 — Identify the malicious IP

Use the IP from Challenge 3 or filter for HTTP traffic to identify file transfer sessions:

```bash
tshark -r chal.pcap -Y "http.request.method == \"GET\"" -T fields \
  -e ip.src -e ip.dst -e http.request.full_uri
```

#### Step 2 — Extract transferred files

```
Wireshark → File → Export Objects → HTTP
```

Examine the file names of all exported objects.

#### Step 3 — Identify the malware file

Submit extracted file hashes to VirusTotal to confirm malicious classification. The file with a positive detection rate is the malware.

```bash
sha256sum <exported_file>
# Submit hash to https://www.virustotal.com/
```

### Expected Answer

The filename with extension of the malware file.

---

## Challenge 5 — Malware SHA1 Hash (30 pts)

### Question

What is the SHA1 hash of that malware file?

### Technical Analysis

After identifying and extracting the malware file in Challenge 4, compute its SHA1 hash.

### Solving Approach

#### Step 1 — Ensure the file is correctly extracted

Re-export the malware file from the PCAP if not already saved:

```
Wireshark → File → Export Objects → HTTP → Save <malware_filename>
```

#### Step 2 — Compute SHA1

```bash
sha1sum <malware_filename>
```

#### Step 3 — Verify

Cross-reference the computed SHA1 with VirusTotal to confirm it matches the known malware sample.

### Expected Answer

A 40-character lowercase hexadecimal SHA1 hash string.
