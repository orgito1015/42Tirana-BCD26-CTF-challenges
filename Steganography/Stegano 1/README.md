# Stegano 1

**Category:** Steganography
**Points:** 20
**Difficulty:** Easy

## Overview

The 42 Tirana marketing team created a promotional image. A secret message has been hidden inside the image by an unknown party. The objective is to discover the hidden message.

**Provided Files:**
- `42tirana_steg1.png` — PNG image with hidden content

---

## Technical Analysis

For entry-level steganography challenges, the most common concealment methods are:
- **LSB (Least Significant Bit) steganography**: Data encoded in the least significant bits of pixel color values
- **Metadata embedding**: Data hidden in EXIF or other metadata fields
- **Appended data**: Data appended after the image's end-of-file marker
- **steghide embedding**: Data hidden with steghide (may require a password)

---

## Solving Approach

### Step 1 — Inspect metadata

```bash
exiftool 42tirana_steg1.png
```

Look for unusual fields, comments, or base64-encoded content.

### Step 2 — Check for appended data

```bash
strings 42tirana_steg1.png | tail -50
binwalk 42tirana_steg1.png
```

If `binwalk` detects embedded files, extract them:

```bash
binwalk -e 42tirana_steg1.png
```

### Step 3 — LSB analysis with zsteg

```bash
zsteg 42tirana_steg1.png
zsteg -a 42tirana_steg1.png   # Check all bit planes and channels
```

### Step 4 — steghide extraction

Try extracting with an empty password (many beginner challenges use no password):

```bash
steghide extract -sf 42tirana_steg1.png -p ""
```

### Step 5 — Bit-plane analysis

Open `stegsolve` and cycle through bit planes (R0, G0, B0, etc.) to look for readable patterns in the least significant bit layers.

---

## Expected Answer Format

The hidden message is the flag in standard competition format.
