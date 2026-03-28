# Network 1 — DDoS Attack Analysis

**Category:** Network Forensics
**Points:** 50

## Overview

A severe UDP-based Distributed Denial of Service (DDoS) attack has degraded the performance of an Ubuntu server. A packet capture was collected during the attack. The objective is to analyze the capture and identify the IP address of the attacker targeting the Ubuntu server via UDP.

**Provided Files:**
- `DD_attack.pcapng` — Wireshark-format packet capture of the DDoS event

---

## Technical Analysis

UDP flood attacks involve sending a large volume of UDP packets to random or specific ports on the target host, exhausting its bandwidth and processing capacity. Key indicators include:

- High UDP packet rate from a single source IP
- Uniform or random destination UDP ports
- Small or fixed packet size (common in amplification attacks)
- ICMP "Port Unreachable" responses from the target

In a UDP flood, the attacker source IP is typically the single address with the highest UDP packet send rate toward the victim.

---

## Solving Approach

### Step 1 — Open the capture

```bash
wireshark DD_attack.pcapng
```

### Step 2 — Filter UDP traffic

```
udp
```

Observe the volume and distribution of UDP packets.

### Step 3 — Analyze IP conversation statistics

```
Statistics → Conversations → UDP tab
```

Sort by packet count or bytes. The IP with the highest outbound UDP packet count toward the server is the attacker.

```bash
tshark -r DD_attack.pcapng -q -z conv,udp | sort -rn -k3 | head -20
```

### Step 4 — Identify the target (Ubuntu server)

The server IP is the destination address receiving the highest volume of UDP traffic. Cross-reference with ICMP port unreachable responses:

```bash
tshark -r DD_attack.pcapng -Y "icmp.type == 3 && icmp.code == 3" -T fields -e ip.src
```

### Step 5 — Confirm the attacker IP

The attacker is the source IP sending the flood of UDP packets to the server:

```bash
tshark -r DD_attack.pcapng -Y "udp" -T fields -e ip.src | sort | uniq -c | sort -rn | head -5
```

---

## Expected Answer Format

The answer is the IPv4 address of the attacker (e.g., `192.168.x.x`).
