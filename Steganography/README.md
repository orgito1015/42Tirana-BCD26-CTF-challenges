# Steganography

## Overview

This category contains six steganography challenges in which data has been concealed within image and audio files. Techniques used range from simple LSB embedding to multi-layer steganography and audio-based Morse code encoding.

## Challenges

| Challenge | Points | File | Technique |
|---|---|---|---|
| [Stegano 1](./Stegano%201/) | 20 | `42tirana_steg1.png` | Image steganography (LSB or metadata) |
| [Lost in the Maze](./Lost%20in%20the%20Maze/) | 25 | `zzz.png` | Brute-force / fuzzing |
| [Checkmate Bits](./Checkmate%20Bits/) | 30 | `42tirana_steg2.png` | Bit-level analysis |
| [Old Comm](./Old%20Comm/) | 35 | `old_comm.wav` | Audio — Morse code |
| [Hard...or very hard](./Hard...or%20very%20hard%20(Not%20completed)/) | 100 | `DSC_7421.JPG` | JPEG steganography |
| [Layers of 42](./Layers%20of%2042/) | 150 | `42tirana_stego_veryhard.png` | Multi-layer steganography |

## Required Tools

- `steghide` — Password-based steganography extraction
- `zsteg` — LSB and other steganographic analysis for PNG/BMP
- `stegsolve` — Bit-plane and color channel analysis (Java)
- `binwalk` — Embedded file detection and extraction
- `exiftool` — Metadata analysis
- `strings` — Embedded string extraction
- Audacity / Sonic Visualiser — Audio spectrogram and waveform analysis
- CyberChef — Multi-step encoding/decoding
