# The Mastermind

**Category:** Forensics
**Points:** 120

## Overview

A compressed archive of server logs has been recovered from the incident. The logs contain a final clue left by the attacker. The objective is to analyze the server logs and extract the hidden flag.

**Provided Files:**
- `server_logs.tar` — Compressed archive of server log files

---

## Technical Analysis

Server logs (web server, application, authentication, or system logs) can reveal attacker behavior such as:
- Injected commands or code in request URIs
- Base64 or hex-encoded strings in HTTP parameters or User-Agent headers
- Structured flag values matching a known pattern (e.g., `flag{...}`)

Attackers sometimes embed flags or hidden data in log entries through deliberate interaction with the server (e.g., crafted requests that cause specific strings to be logged).

---

## Solving Approach

### Step 1 — Extract the archive

```bash
tar -xf server_logs.tar
ls -la
```

Identify the log files present (e.g., `access.log`, `error.log`, `auth.log`).

### Step 2 — Identify the log format

Inspect the first few lines of each log file to determine the format:

```bash
head -20 access.log
```

Common formats include Apache Combined Log Format, Nginx access log, and syslog.

### Step 3 — Search for flag patterns

```bash
grep -r "flag{" .
grep -r "CTF" .
grep -rE "[a-f0-9]{32}" .   # MD5-like strings
```

### Step 4 — Search for encoded content

If no plaintext flag is found, look for encoded entries:

```bash
# Extract base64-like strings from log lines
grep -oE '[A-Za-z0-9+/]{20,}={0,2}' access.log | while read s; do
    echo "$s" | base64 -d 2>/dev/null
done

# Hex strings
grep -oE '(\\x[0-9a-fA-F]{2}){4,}' access.log
```

### Step 5 — Analyze anomalous entries

Sort and compare request frequencies to find outliers. An attacker may have used a unique User-Agent or URI path to signal the flag location:

```bash
awk '{print $7}' access.log | sort | uniq -c | sort -rn | head -20
```

### Step 6 — Reconstruct and decode

If the flag is split across multiple log entries or encoded across request sequences, reconstruct it by ordering entries chronologically and concatenating relevant fields.

---

## Expected Answer Format

The flag follows the standard format used throughout the competition.
