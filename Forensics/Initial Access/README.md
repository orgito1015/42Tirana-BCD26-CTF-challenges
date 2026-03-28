# Initial Access

**Category:** Forensics
**Points:** 20

## Overview

An attacker gained initial access to a network by submitting a malicious HTTP request. A packet capture was recorded during the intrusion. The objective is to identify the first malicious HTTP request made by the attacker.

**Provided Files:**
- `network_capture.pcap` — Packet capture of network traffic during the incident

---

## Technical Analysis

Initial access via HTTP commonly takes the form of:
- Web application exploitation (SQL injection, command injection, path traversal)
- Upload of a malicious file via an HTTP POST request
- Exploitation of a vulnerable endpoint (e.g., a CVE-specific URI pattern)

The first malicious request is typically distinguishable by anomalous URI patterns, unusual HTTP methods, suspicious User-Agent strings, or payloads in request parameters.

---

## Solving Approach

### Step 1 — Open the capture in Wireshark

```bash
wireshark network_capture.pcap
```

### Step 2 — Filter for HTTP traffic

Apply a Wireshark display filter to isolate HTTP requests:

```
http.request
```

### Step 3 — Review HTTP requests chronologically

Sort by time and examine each GET and POST request. Look for:
- URI paths that contain shell metacharacters (`;`, `|`, `&`, `../`)
- Encoded payloads in query parameters (URL-encoded or base64)
- Requests to non-standard paths or administrative endpoints

### Step 4 — Identify the first malicious request

The first anomalous HTTP request in the timeline is the initial access vector. Note the full URI, HTTP method, source IP, and any request body content.

### Step 5 — Extract using tshark (optional)

```bash
tshark -r network_capture.pcap -Y "http.request" -T fields \
  -e frame.time -e ip.src -e http.request.method -e http.request.full_uri
```

---

## Expected Answer Format

The answer is the full HTTP request URI or the specific malicious parameter/payload value from the first attacker request.
