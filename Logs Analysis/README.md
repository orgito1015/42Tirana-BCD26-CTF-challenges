# Logs Analysis

## Overview

This challenge series focuses on a compromised Firefox browser installation on a Windows system. Windows Event Logs (`.evtx`) captured during the incident are provided for analysis. The objective is to determine the vulnerability or technique used, identify the malicious payload, and reconstruct the execution chain.

**Provided Files:**
- `event_logs_firefox.evtx` — Windows Event Log file from the affected system
- `Logs Analysis.txt` — Challenge questions

---

## Questions

1. **(20 pts)** The browser has been compromised with a well-known vulnerability or technique. What is it?
2. **(20 pts)** What was the payload file created by the malware to start the Firefox browser and compromise the machine?
3. **(30 pts)** What was the full command line used to launch the code in the payload?
4. **(50 pts)** What is the MD5 of the abnormal file or process that runs when the user opens Firefox or adds a new tab?
5. **(100 pts)** What was the creation time of the payload file that compromised Firefox? (Full UTC time)

---

## Technical Analysis

Firefox browser compromise via Windows Event Logs typically involves:

- **DLL hijacking / DLL side-loading**: A malicious DLL placed in the browser's directory or search path is loaded instead of a legitimate library.
- **Browser extension persistence**: A malicious extension or startup script is injected into the Firefox profile.
- **AutoRun/NTFSAlternateDataStream abuse**: A malicious file masquerades as a legitimate browser component.

Windows Event Logs relevant to this investigation:
- **Event ID 4688** — Process creation (includes command-line arguments if audit policy enables it)
- **Event ID 7045 / 7040** — Service installation or modification
- **Event ID 4663** — File object access
- **Event ID 11** — FileCreate (Sysmon)
- **Event ID 1** — Process creation (Sysmon)

---

## Solving Approach

### Step 1 — Parse the EVTX file

Use a suitable parser to convert the binary EVTX format to readable output:

```bash
# Using python-evtx
python3 -m evtx.evtx_dump event_logs_firefox.evtx > events.xml

# Or using evtx_dump (Rust tool)
evtx_dump -f jsonl event_logs_firefox.evtx > events.jsonl
```

Alternatively, open the file directly in Windows Event Viewer or in Chainsaw/Hayabusa for structured analysis.

### Step 2 — Identify the compromise technique (Question 1)

Search for process creation events that involve Firefox:

```bash
grep -i "firefox" events.xml | grep -i "CommandLine\|Image\|ParentImage"
```

Look for non-standard parent processes spawning Firefox, or Firefox loading unexpected modules.

### Step 3 — Identify the payload file (Question 2)

Search for file creation events associated with Firefox startup:

```bash
grep -i "EventID.*11\|FileCreate" events.xml | grep -i "firefox"
```

The payload file is typically created in the Firefox installation directory, AppData profile directory, or a temporary path.

### Step 4 — Extract the full command line (Question 3)

Locate Event ID 4688 or Sysmon Event ID 1 entries for the payload execution:

```bash
grep -i "EventID.*4688\|EventID.*1" events.xml -A 20 | grep -i "CommandLine"
```

Record the complete `CommandLine` field value.

### Step 5 — Compute MD5 of the abnormal process/file (Question 4)

If Sysmon logs are present, Event ID 1 includes a `Hashes` field with MD5, SHA1, and SHA256 values:

```bash
grep -i "Hashes\|MD5" events.xml | grep -i "firefox"
```

### Step 6 — Determine the payload file creation time (Question 5)

File creation time is recorded in Event ID 11 (Sysmon FileCreate) or can be inferred from the event timestamp. The answer must be expressed as a full UTC timestamp.

```bash
grep -i "FileCreate\|EventID.*11" events.xml -A 10 | grep -i "firefox\|payload"
```

---

## Expected Answer Format

| # | Answer Format |
|---|---|
| 1 | Name of vulnerability or technique (e.g., "DLL hijacking") |
| 2 | Full file name with extension |
| 3 | Complete command-line string |
| 4 | 32-character MD5 hex string |
| 5 | Full UTC timestamp (e.g., `2024-01-15 08:32:17 UTC`) |
