# Hard...or very hard

**Category:** Steganography
**Points:** 100
**Difficulty:** Hard
**Status:** Not completed

## Overview

A JPEG photograph (`DSC_7421.JPG`) depicting a flower is provided. The challenge hints that the flower conceals something else. This is a JPEG steganography challenge where hidden data is embedded within the compressed image using a method that survives JPEG's lossy compression or is embedded in a lossless component.

**Provided Files:**
- `DSC_7421.JPG` — JPEG photograph

---

## Technical Analysis

JPEG steganography differs from PNG-based techniques because JPEG uses lossy compression, which destroys data embedded in raw pixel values. Common JPEG steganographic methods include:

- **steghide**: Embeds data in the DCT (Discrete Cosine Transform) coefficients of the JPEG file; survives compression; may require a password
- **OpenStego**: Java-based tool that embeds data in the frequency domain
- **jphide / jpseek**: Older JPEG steganography tool that hides data in the DCT coefficients
- **Metadata hiding**: Data appended after the JPEG End-of-Image (EOI) marker (`0xFF 0xD9`) or embedded in EXIF fields
- **OutGuess**: Tool that modifies DCT coefficients while preserving the statistical distribution

---

## Solving Approach

### Step 1 — Metadata and structure inspection

```bash
exiftool DSC_7421.JPG
strings DSC_7421.JPG | grep -i "flag\|hidden\|secret"
```

Check EXIF fields including Comment, UserComment, ImageDescription, and GPS data.

### Step 2 — Check for appended data

JPEG files end with the `0xFF 0xD9` marker. Any data after this marker is appended content:

```bash
binwalk DSC_7421.JPG
binwalk -e DSC_7421.JPG
```

```python
with open("DSC_7421.JPG", "rb") as f:
    data = f.read()
eoi = data.rfind(b'\xff\xd9')
if eoi < len(data) - 2:
    print("Appended data found:", data[eoi+2:])
```

### Step 3 — steghide extraction

```bash
# Try without a password
steghide extract -sf DSC_7421.JPG -p ""

# Try common passwords
for pass in "" "flower" "hidden" "42tirana" "secret"; do
    steghide extract -sf DSC_7421.JPG -p "$pass" 2>/dev/null && echo "Found with: $pass"
done
```

### Step 4 — jphide / outguess extraction

```bash
jpseek DSC_7421.JPG output.txt
outguess -r DSC_7421.JPG output.txt
```

### Step 5 — Brute-force steghide

If a password is required and common attempts fail:

```bash
stegcracker DSC_7421.JPG /usr/share/wordlists/rockyou.txt
```

### Step 6 — DCT coefficient analysis

Use the `stegdetect` tool to identify which steganographic method was used:

```bash
stegdetect DSC_7421.JPG
```

This will report the likelihood of jphide, outguess, jsteg, or other JPEG steganography being present.

---

## Expected Answer Format

The hidden content extracted from the JPEG file is the flag in standard competition format.
