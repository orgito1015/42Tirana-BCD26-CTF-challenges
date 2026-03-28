# Network Medium Challenge

**Category:** Network
**Points:** 30
**Difficulty:** Medium

## Overview

An HTTP-based network capture is provided. The challenge description states: "HTTP is not always safe. Focus." This indicates that sensitive data — likely the flag — is transmitted in cleartext over HTTP and must be identified within the packet stream.

**Provided Files:**
- `42tirana_medium.pcap` — Packet capture (TCPDUMP format)

---

## Technical Analysis

HTTP traffic is transmitted in plaintext, making it fully visible in packet captures. Flags or sensitive data may appear in:
- HTTP GET request URIs or query parameters
- HTTP POST request bodies (form data, JSON, XML)
- HTTP response bodies
- HTTP headers (Authorization, Cookie, X-Custom-Header)
- Basic Authentication credentials (base64-encoded in the Authorization header)

---

## Solving Approach

### Step 1 — Open the capture and filter HTTP

```bash
wireshark 42tirana_medium.pcap
```

Apply the display filter:

```
http
```

### Step 2 — Inspect HTTP request and response content

Look for flag-like strings in:
- GET/POST request URIs
- Request body (for POST requests)
- Response body content

```bash
tshark -r 42tirana_medium.pcap -Y "http" -T fields \
  -e http.request.full_uri \
  -e http.request.method \
  -e http.response.code \
  -e http.file_data
```

### Step 3 — Follow HTTP streams

Right-click a relevant HTTP packet → **Follow → HTTP Stream** to view the full request/response exchange.

### Step 4 — Decode encoded content

If the flag appears base64-encoded (e.g., in an Authorization or Cookie header):

```bash
echo "<base64_string>" | base64 -d
```

### Step 5 — Export HTTP objects

If the flag is embedded in a downloaded file:

```
File → Export Objects → HTTP
```

Save and inspect any returned HTML, JSON, text, or binary files.

---

## Expected Answer Format

Standard flag format used throughout the competition.
