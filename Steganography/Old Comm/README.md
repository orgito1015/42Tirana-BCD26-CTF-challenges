# Old Comm

**Category:** Steganography
**Points:** 35
**Difficulty:** Medium

## Overview

An audio file `old_comm.wav` is provided. The challenge description states: "B.K — The sound consists of short and long tones..." The initials "B.K" stand for Baudot-Kocher or, more likely in this context, are a reference to the operator style of Morse code transmission. The description explicitly confirms that the audio encodes a message using short (dot) and long (dash) tones.

**Provided Files:**
- `old_comm.wav` — WAV audio file containing Morse code

**Flag Format:** `flag{...}`

---

## Technical Analysis

Morse code is an encoding scheme that represents characters using sequences of short signals (dots, `.`) and long signals (dashes, `-`). In audio form:
- A **short tone** (dit) typically lasts one unit
- A **long tone** (dah) typically lasts three units
- A **short silence** separates symbols within a character
- A **longer silence** separates characters
- An even longer silence separates words

The audio must be analyzed to identify the tone patterns, then decoded against the International Morse Code alphabet.

---

## Solving Approach

### Step 1 — Visualize the audio waveform

Open `old_comm.wav` in Audacity:

```bash
audacity old_comm.wav
```

Zoom in on the waveform to identify individual short and long tones. Alternatively, view the spectrogram:

```
View → Show Spectrogram
```

### Step 2 — Identify tone boundaries

Note the start and end time of each tone burst. Categorize each burst as:
- **Short (dot)**: Duration ≈ 1 unit
- **Long (dash)**: Duration ≈ 3 units

Group tones into characters using silence gaps as delimiters.

### Step 3 — Transcribe the Morse sequence

Write down the sequence of dots and dashes for each character group. For example:
```
... -.-- -.-- . -
```

### Step 4 — Decode using the Morse alphabet

Reference the International Morse Code table:

| Letter | Code | | Letter | Code |
|---|---|---|---|---|
| A | `.-` | | N | `-.` |
| B | `-...` | | O | `---` |
| C | `-.-.` | | P | `.--.` |
| D | `-..` | | Q | `--.-` |
| E | `.` | | R | `.-.` |
| F | `..-.` | | S | `...` |
| G | `--.` | | T | `-` |
| H | `....` | | U | `..-` |
| I | `..` | | V | `...-` |
| J | `.---` | | W | `.--` |
| K | `-.-` | | X | `-..-` |
| L | `.-..` | | Y | `-.--` |
| M | `--` | | Z | `--..` |

Digits: `0` = `-----`, `1` = `.----`, ..., `9` = `----.`

### Step 5 — Automate with a Morse decoder tool

If manual transcription is impractical due to audio length, use an automated decoder:

```bash
# morse2text (if available)
morse2text < morse_transcription.txt

# Or use an online tool such as morsecode.world
```

---

## Expected Answer Format

`flag{<decoded_morse_message>}` — the decoded text wrapped in the standard flag format.
