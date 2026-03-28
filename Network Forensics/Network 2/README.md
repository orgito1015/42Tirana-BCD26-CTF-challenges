# Network 2 — Session Hijacking Analysis

**Category:** Network Forensics
**Points:** 50

## Overview

An attacker performed session hijacking against a machine within the `172.30.10.0/24` subnet. A packet capture file (`Jack.pcapng`) was obtained from the victim machine. The objective is to analyze the capture and determine the IP address of the victim machine targeted by the attacker.

**Provided Files:**
- `Jack.pcapng` — Wireshark-format packet capture from the victim machine

---

## Technical Analysis

Session hijacking involves an attacker intercepting and taking over an active TCP session established by another user. Common techniques include:

- **TCP sequence number prediction**: The attacker predicts or sniffs valid TCP sequence numbers and injects forged packets
- **ARP spoofing / Man-in-the-middle**: The attacker poisons the ARP cache to redirect traffic through their machine
- **Cookie theft**: The attacker obtains a valid session cookie and reuses it to impersonate the victim

Indicators in a packet capture:
- ARP requests/responses mapping a single IP to multiple MAC addresses (ARP spoofing)
- TCP RST injections targeting an active session
- Duplicate ACK or sequence number anomalies
- TCP packets from an unexpected source IP with valid sequence numbers

The victim is the machine whose session was hijacked, i.e., the original session owner whose traffic was intercepted.

---

## Solving Approach

### Step 1 — Open the capture

```bash
wireshark Jack.pcapng
```

### Step 2 — Inspect the subnet

Filter for the `172.30.10.0/24` subnet to limit scope:

```
ip.addr == 172.30.10.0/24
```

### Step 3 — Detect ARP anomalies

Look for duplicate ARP replies mapping one IP to multiple MACs:

```
arp
```

In Wireshark, duplicate ARP replies are flagged with an "ARP: Duplicate IP address" info message. The IP being duplicated is the victim's IP.

```bash
tshark -r Jack.pcapng -Y "arp" -T fields -e arp.src.hw_mac -e arp.src.proto_ipv4 | sort | uniq -c | sort -rn
```

### Step 4 — Identify TCP session anomalies

Filter for TCP resets or injected packets:

```
tcp.flags.reset == 1
```

The IP address whose sessions receive unexpected RST packets or forged sequence numbers is the victim.

### Step 5 — Correlate with HTTP session data

If the hijacking involves a web session, inspect HTTP cookies and session tokens. Look for the same session token appearing from two different source IPs.

```bash
tshark -r Jack.pcapng -Y "http" -T fields -e ip.src -e http.cookie | sort
```

---

## Expected Answer Format

The answer is the IPv4 address of the victim machine within the `172.30.10.0/24` subnet.
