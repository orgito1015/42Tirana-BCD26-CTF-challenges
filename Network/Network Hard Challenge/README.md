# Network Hard Challenge

**Category:** Network
**Points:** 40
**Difficulty:** Hard

## Overview

A client visits `42tirana.al`, and something is leaking from DNS records. The objective is to inspect DNS traffic in the packet capture and recover information disclosed through DNS responses or DNS-based covert channels.

**Provided Files:**
- `42tirana_hard.pcap` — Packet capture (TCPDUMP format)

---

## Technical Analysis

DNS can leak sensitive information in several ways:
- **DNS TXT records**: Arbitrary text data can be stored and queried
- **DNS subdomain enumeration**: Subdomains may reveal internal resource names or encoded data
- **DNS tunneling**: Data exfiltrated by encoding it in DNS query/response hostnames or TXT record values
- **Zone transfer (AXFR)**: Full domain zone data exposed via misconfigured DNS server

When a flag is hidden in DNS records, it is typically encoded in a TXT record or split across multiple subdomain query labels.

---

## Solving Approach

### Step 1 — Open the capture and filter DNS

```bash
wireshark 42tirana_hard.pcap
```

Apply the display filter:

```
dns
```

### Step 2 — Inspect DNS query and response records

Examine all DNS query types, particularly:
- `TXT` records (often contain readable or encoded data)
- `A`, `AAAA`, `CNAME` records for subdomains of `42tirana.al`
- `ANY` queries that return multiple record types

```bash
tshark -r 42tirana_hard.pcap -Y "dns" -T fields \
  -e dns.qry.name \
  -e dns.resp.type \
  -e dns.txt
```

### Step 3 — Look for encoded subdomains

If subdomains contain base64-like or hex strings, they may represent DNS tunneling:

```bash
tshark -r 42tirana_hard.pcap -Y "dns.qry.type == 1" -T fields -e dns.qry.name | \
  grep -v "42tirana.al$" | sort | uniq
```

### Step 4 — Decode TXT record content

```bash
tshark -r 42tirana_hard.pcap -Y "dns.resp.type == 16" -T fields -e dns.txt
```

If the value is base64-encoded:

```bash
echo "<txt_record_value>" | base64 -d
```

### Step 5 — Reconstruct split data

If the flag is split across multiple DNS requests (a common tunneling technique), order the queries by time and concatenate the relevant portions:

```bash
tshark -r 42tirana_hard.pcap -Y "dns" -T fields -e frame.time -e dns.qry.name | sort
```

---

## Expected Answer Format

Standard flag format used throughout the competition, likely found within a DNS TXT record or reconstructed from DNS query subdomains.
