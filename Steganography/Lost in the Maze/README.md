# Lost in the Maze

**Category:** Steganography
**Points:** 25
**Difficulty:** Easy–Medium

## Overview

An image named `zzz.png` is provided. The challenge description contains the hint "Fuzzzzing..." and shows the expected flag format `flag{xxxxxxxxx}`. The term "fuzzing" in this context suggests that the hidden content requires a brute-force or wordlist-based approach to extract — likely a password-protected steganographic payload.

**Provided Files:**
- `zzz.png` — PNG image containing a hidden payload

**Flag Format:** `flag{xxxxxxxxx}`

---

## Technical Analysis

When a steganography challenge references "fuzzing," it typically indicates:
- A password-protected `steghide` embed requiring wordlist brute-force
- A hidden file that requires multiple extraction attempts with different parameters
- Pixel-level noise patterns that respond to specific color filter combinations

The flag format `flag{xxxxxxxxx}` suggests the payload is a short string once decoded.

---

## Solving Approach

### Step 1 — Basic inspection

```bash
file zzz.png
exiftool zzz.png
strings zzz.png | grep -i "flag"
binwalk zzz.png
```

### Step 2 — Attempt steghide with common passwords

```bash
steghide extract -sf zzz.png -p ""
steghide extract -sf zzz.png -p "password"
steghide extract -sf zzz.png -p "steghide"
steghide extract -sf zzz.png -p "flag"
```

### Step 3 — Brute-force steghide with a wordlist

```bash
# Using stegcracker
stegcracker zzz.png /usr/share/wordlists/rockyou.txt

# Manual loop
while IFS= read -r pass; do
    steghide extract -sf zzz.png -p "$pass" 2>/dev/null && echo "Password: $pass" && break
done < /usr/share/wordlists/rockyou.txt
```

### Step 4 — zsteg LSB analysis

If no steghide payload is found, analyze with zsteg:

```bash
zsteg -a zzz.png 2>/dev/null | grep -i "flag\|text"
```

### Step 5 — Examine output

Once the hidden file is extracted, read its contents:

```bash
cat extracted_file
strings extracted_file
```

---

## Expected Answer Format

`flag{xxxxxxxxx}` — a short alphanumeric string within the flag brackets.
