# Memory Secrets

**Category:** Forensics
**Points:** 50

## Overview

A memory snapshot was captured while the attacker was actively operating on a compromised system. The objective is to locate the specific command used to exfiltrate data.

**Provided Files:**
- `memory_dump.raw` — Raw physical memory capture

---

## Technical Analysis

Data exfiltration commands commonly appear as:
- Running processes with command-line arguments (e.g., `curl`, `wget`, `nc`, `scp`, PowerShell transfers)
- Command history stored in process memory
- Arguments passed to interpreters (cmd.exe, powershell.exe, bash)

Volatility is the standard tool for structured analysis of raw memory images. It can enumerate processes, extract command-line arguments, recover browser history, and more.

---

## Solving Approach

### Step 1 — Determine the memory image profile

```bash
python2 vol.py -f memory_dump.raw imageinfo
```

Note the suggested profile (e.g., `Win10x64_19041`, `WinXPSP2x86`, `LinuxUbuntu18.04x64`).

### Step 2 — List running processes

```bash
python2 vol.py -f memory_dump.raw --profile=<PROFILE> pslist
```

Identify any suspicious processes (unusual names, processes spawned from cmd.exe or PowerShell).

### Step 3 — Extract command-line arguments

```bash
python2 vol.py -f memory_dump.raw --profile=<PROFILE> cmdline
```

This lists every process along with its full command-line invocation. Search for data transfer utilities or encoded payloads.

### Step 4 — Inspect process memory for strings

If the exfiltration command is not visible in `cmdline`, search process memory directly:

```bash
python2 vol.py -f memory_dump.raw --profile=<PROFILE> memdump -p <PID> -D /output/
strings /output/<PID>.dmp | grep -i "curl\|wget\|nc\|ftp\|scp\|powershell"
```

### Step 5 — Check console history (Windows)

```bash
python2 vol.py -f memory_dump.raw --profile=<PROFILE> consoles
```

This plugin recovers the input/output history of console sessions.

---

## Expected Answer Format

The answer is the complete command line used to exfiltrate data, including all arguments and the destination address or path.
