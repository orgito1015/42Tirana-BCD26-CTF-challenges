# Network Very Hard Challenge

**Category:** Network
**Points:** 100
**Difficulty:** Very Hard

## Overview

A complex network capture is provided. The challenge description states: "The situation is getting worse and worse. Flags are missing." This indicates that flags have been deliberately fragmented, obfuscated, or distributed across multiple protocol layers or sessions within the capture, requiring advanced reconstruction techniques.

**Provided Files:**
- `42tirana_veryhard.pcap` — Packet capture (TCPDUMP format)

---

## Technical Analysis

At this difficulty level, flags are typically hidden using one or more of the following techniques:

- **IP fragmentation**: Flag split across fragmented IP packets requiring reassembly
- **Steganography in network data**: Flag encoded in packet timing, TTL values, or sequence numbers
- **Encrypted sessions**: TLS/SSL traffic that can be decrypted if the session keys are available
- **Protocol tunneling**: Flag carried within an outer protocol (DNS over HTTPS, ICMP tunneling, HTTP over uncommon port)
- **Multi-session reconstruction**: Flag distributed across multiple TCP or UDP sessions
- **Custom binary protocol**: Non-standard protocol requiring manual parsing

---

## Solving Approach

### Step 1 — Enumerate all protocols and sessions

Get a high-level overview of the capture:

```bash
tshark -r 42tirana_veryhard.pcap -q -z io,phs
tshark -r 42tirana_veryhard.pcap -q -z conv,tcp
```

Identify any unusual protocols, high-entropy payloads, or anomalous session counts.

### Step 2 — Check for IP fragmentation

```bash
tshark -r 42tirana_veryhard.pcap -Y "ip.flags.mf == 1 || ip.frag_offset > 0"
```

If fragmented packets are found, Wireshark will reassemble them automatically in the IP layer view.

### Step 3 — Analyze ICMP traffic

ICMP is frequently used for covert channels. Inspect ICMP data payloads:

```bash
tshark -r 42tirana_veryhard.pcap -Y "icmp" -T fields -e icmp.data
```

### Step 4 — Inspect sequence numbers and header fields

In steganography-in-headers attacks, the flag may be encoded in:
- TCP sequence numbers
- IP ID fields
- TTL values
- TCP window size

```bash
tshark -r 42tirana_veryhard.pcap -T fields \
  -e ip.id -e ip.ttl -e tcp.seq -e tcp.window_size
```

### Step 5 — Carve all TCP streams

Export all TCP stream data for inspection:

```bash
for stream in $(tshark -r 42tirana_veryhard.pcap -q -z conv,tcp | awk '/^[0-9]/{print $1}'); do
  tshark -r 42tirana_veryhard.pcap -Y "tcp.stream == $stream" -w /tmp/stream_$stream.pcap
  tshark -r /tmp/stream_$stream.pcap -T fields -e data.text 2>/dev/null
done
```

### Step 6 — Decode payloads

Try common decoding methods on each extracted payload:

```bash
# Base64
echo "<payload>" | base64 -d

# Hex
echo "<payload>" | xxd -r -p

# ROT13
echo "<payload>" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

### Step 7 — Reconstruct the flag

If the flag is distributed across multiple packets or sessions, order all candidate fragments by sequence number, session identifier, or timestamp, then concatenate to form the complete flag.

---

## Expected Answer Format

Standard flag format used throughout the competition.
