# Layers of 42

**Category:** Steganography
**Points:** 150
**Difficulty:** Very Hard

## Overview

An image recovered from a compromised workstation was previously seen at a 42 Tirana event. Investigators believe the attacker concealed a secret message using multiple layers of steganography. The objective (described as "???") requires the solver to independently determine the approach, suggesting that the challenge is intentionally open-ended with no single prescribed method.

**Provided Files:**
- `42tirana_stego_veryhard.png` — PNG image with multi-layer hidden content

---

## Technical Analysis

Multi-layer steganography means the hidden content has been processed through two or more concealment methods applied sequentially. Common layering approaches include:

1. **Layer 1**: Base steganographic embedding (e.g., LSB)
2. **Layer 2**: The extracted content is itself encoded (e.g., base64, hex, ROT13, XOR cipher)
3. **Layer 3**: The second-layer output contains another embedded file

Other possibilities:
- An image embedded within an image (nested steganography)
- A password-protected steghide embed where the password is hidden in LSB data
- A ZIP or RAR archive appended to the PNG with encrypted content
- Color channel manipulation (e.g., only the alpha channel contains data)
- Steganography in pixel coordinates rather than color values

---

## Solving Approach

### Step 1 — Exhaustive static analysis

```bash
file 42tirana_stego_veryhard.png
exiftool 42tirana_stego_veryhard.png
strings 42tirana_stego_veryhard.png
binwalk 42tirana_stego_veryhard.png
```

### Step 2 — LSB extraction (all bit planes and channels)

```bash
zsteg -a 42tirana_stego_veryhard.png 2>/dev/null
```

Save any identified payloads:

```bash
zsteg -e "b1,rgb,lsb,xy" 42tirana_stego_veryhard.png > layer1_output
file layer1_output
```

### Step 3 — Bit-plane visualization

Use stegsolve to inspect all 24 bit planes (R0–R7, G0–G7, B0–B7). Document any planes that appear to contain structured patterns.

### Step 4 — Extract embedded files

```bash
binwalk -e 42tirana_stego_veryhard.png
foremost -i 42tirana_stego_veryhard.png -o ./carved/
```

Check if the extracted content is a nested image, archive, or encoded text.

### Step 5 — Steghide extraction

```bash
steghide extract -sf 42tirana_stego_veryhard.png -p ""
stegcracker 42tirana_stego_veryhard.png /usr/share/wordlists/rockyou.txt
```

### Step 6 — Decode each extracted layer

Apply standard decoding methods to each extracted artifact in sequence:

```bash
# Try base64
cat layer1_output | base64 -d > layer2_output

# Try hex
cat layer1_output | xxd -r -p > layer2_output

# Try XOR with key
python3 -c "
data = open('layer1_output','rb').read()
key = b'42tirana'
out = bytes(data[i] ^ key[i % len(key)] for i in range(len(data)))
open('layer2_output','wb').write(out)
"
```

### Step 7 — Repeat for each layer

Each decoded layer may itself be an image, an archive, or encoded text. Continue extracting and decoding until the final flag is revealed.

### Step 8 — Color channel alpha analysis

```python
from PIL import Image
import numpy as np

img = Image.open("42tirana_stego_veryhard.png").convert("RGBA")
pixels = np.array(img)

# Inspect alpha channel
alpha = pixels[:, :, 3]
print("Alpha unique values:", np.unique(alpha))
# If alpha contains non-255 values, extract them as data
```

---

## Expected Answer Format

The flag follows the standard competition format and is recovered after completing the full multi-layer extraction chain.
