# Config Path

**Category:** Misc
**Points:** 10
**Difficulty:** Easy

## Overview

The archive contains a misconfigured application or script where the default configuration path is incorrect. The objective is to identify the wrong path, determine the correct value, and extract the flag.

**Provided Files:**
- `misc_easy.zip` — Archive containing the challenge content

---

## Technical Analysis

Configuration path errors commonly appear in:
- Hardcoded absolute paths in scripts or binaries
- Incorrect relative path references in configuration files
- Environment variable mismatches (e.g., `$HOME`, `$PATH`, `$XDG_CONFIG_HOME`)

In CTF challenges of this type, the flag is typically revealed after correcting the path or reading the configuration file from the right location.

---

## Solving Approach

### Step 1 — Extract the archive

```bash
unzip misc_easy.zip -d misc_easy/
ls -la misc_easy/
```

### Step 2 — Identify the content type

```bash
file misc_easy/*
```

Determine whether the contents are scripts, binaries, configuration files, or a combination.

### Step 3 — Search for path references

```bash
grep -r "config\|path\|conf\|etc\|home\|usr" misc_easy/ --include="*"
```

Identify any hardcoded or incorrect file paths.

### Step 4 — Correct the path and run or inspect

If the content is a script, modify the incorrect path to the correct value and execute it. If it is a configuration file, look for the flag at the corrected path location.

### Step 5 — Retrieve the flag

The flag is either printed by the corrected script, stored in the file at the correct path, or embedded in the configuration content.

---

## Expected Answer Format

Standard flag format used throughout the competition.
