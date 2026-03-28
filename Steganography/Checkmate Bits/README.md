# Checkmate Bits

**Category:** Steganography
**Points:** 30
**Difficulty:** Medium

## Overview

The image `42tirana_steg2.png` appears normal at first glance, but the real hidden content is not visible to the naked eye. The challenge title "Checkmate Bits" and the hint "the real move is not visible" both allude to bit-level analysis — specifically, steganographic data embedded in specific bit planes of the image.

**Provided Files:**
- `42tirana_steg2.png` — PNG image with bit-level steganographic content

---

## Technical Analysis

"Checkmate Bits" references chess (checkmate) and bit planes. In bit-plane steganography, data is hidden in specific bit positions (bit 0 = LSB, bit 7 = MSB) of each color channel (Red, Green, Blue, Alpha). When these bit planes are isolated and visualized, the hidden content becomes apparent.

Common variants:
- **LSB steganography**: Data hidden in bit 0 of R, G, B channels
- **Bit-plane extraction**: Data encoded across non-trivial bit planes
- **zsteg patterns**: Patterns visible with specific `zsteg` modes

---

## Solving Approach

### Step 1 — Run zsteg

```bash
zsteg 42tirana_steg2.png
zsteg -a 42tirana_steg2.png 2>/dev/null | grep -v "^$" | head -50
```

This outputs all detected steganographic patterns with their decoding. Look for readable strings, flag patterns, or file magic bytes.

### Step 2 — Bit-plane analysis with stegsolve

Open the image in stegsolve (Java GUI tool) and cycle through all bit planes:
- Red plane 0 (LSB)
- Green plane 0 (LSB)
- Blue plane 0 (LSB)
- XOR between color channels

```bash
java -jar stegsolve.jar
```

### Step 3 — Python pixel extraction

Extract and visualize individual bit planes programmatically:

```python
from PIL import Image
import numpy as np

img = Image.open("42tirana_steg2.png").convert("RGB")
pixels = np.array(img)

# Extract LSB of red channel
lsb_r = (pixels[:, :, 0] & 1) * 255
Image.fromarray(lsb_r.astype(np.uint8)).save("lsb_r.png")
```

### Step 4 — Extract embedded data

If zsteg identifies a steganographic payload, extract it:

```bash
zsteg -e "b1,rgb,lsb,xy" 42tirana_steg2.png > extracted_data
file extracted_data
strings extracted_data
```

---

## Expected Answer Format

The flag is embedded within the bit planes of the image and follows the standard competition flag format.
