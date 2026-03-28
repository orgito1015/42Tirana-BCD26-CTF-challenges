# Feature Toggle

**Category:** Misc
**Points:** 50
**Difficulty:** Medium

## Overview

A feature has been disabled within the application or configuration provided in the archive. The objective is to identify what disables the feature, re-enable it, and retrieve the flag.

**Provided Files:**
- `misc_medium.zip` — Archive containing the challenge content

---

## Technical Analysis

Feature toggles (also called feature flags) are mechanisms that enable or disable functionality at runtime without code changes. Common implementations include:

- Environment variables (e.g., `FEATURE_X=false`)
- Configuration file boolean values
- Conditional checks within source code
- Database or registry flags
- Compiled preprocessor directives

The challenge requires locating the toggle condition and inverting or overriding it.

---

## Solving Approach

### Step 1 — Extract the archive

```bash
unzip misc_medium.zip -d misc_medium/
ls -la misc_medium/
```

### Step 2 — Identify all files and their types

```bash
file misc_medium/*
```

### Step 3 — Search for toggle-related keywords

```bash
grep -rn "false\|disabled\|enable\|toggle\|feature\|flag\|off\|0" misc_medium/
```

### Step 4 — Analyze source code or configuration

If source code is present, trace the control flow to find where the feature is gated. If a configuration file is present, identify the boolean or numeric value that controls the feature.

### Step 5 — Modify the toggle

Change the disabling condition to its enabled state. For example:
- `FEATURE_ENABLED=false` → `FEATURE_ENABLED=true`
- `if (disabled) { ... }` → `if (!disabled) { ... }`

### Step 6 — Execute and retrieve the flag

Run the modified script or application to reveal the flag.

---

## Expected Answer Format

Standard flag format used throughout the competition.
