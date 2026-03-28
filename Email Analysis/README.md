# Email Analysis

## Overview

This challenge series presents a MIME-formatted email containing two attachments with multi-layer encoding. The goal is to extract and decode all embedded content, recover protected archives, and retrieve user credential data hidden within the final payload.

**Provided Files:**
- `email.txt` — Raw MIME email
- `Email Analysis.txt` — Challenge questions

---

## Questions

1. What was the email address Ilir corresponded with?
2. List email attachment files that Ilir has sent. Provide file names with extension.
3. What is the MD5 hash for `info.txt` after extracted from the email?
4. One of the attached files is a TEXT file. What is the content of that text file (in human-readable format)?
5. What is the password used to open the other file?
6. How many files are inside the ZIP archive?
7. What was the encoder type used to conceal the newly discovered files?
8. What is the password phrase inside the content of the newly discovered TEXT file (in human-readable format)?
9. What is the name of the file that possibly contains the user account information?
10. What language is used to hide the possible banking information?
11. Provide the first captured username and password in the list found from the banking information.

---

## Technical Analysis

### Email Structure

The `email.txt` file is a standard MIME multipart message with the following headers:

```
From:    Ilir Daka <kristi@ctfd.com>
To:      Bledi <bledi@offsec.com>
Subject: Your Invoice
Date:    Thu, 22 May 2025 12:00:24 +0800
```

The message uses `Content-Type: multipart/mixed` and contains two attachments, both encoded in base64:

| Attachment | Size | Encoding |
|---|---|---|
| `info.txt` | 83 bytes | base64 (outer) + UUencoding (inner) |
| `proposal.zip` | 2213 bytes | base64 |

### Decoding Chain

```
email.txt
├── info.txt (base64 encoded in MIME)
│   └── [base64 decode] → UU-encoded content
│       └── [UU decode] → plaintext passphrase
└── proposal.zip (base64 encoded in MIME)
    └── [base64 decode] → encrypted ZIP (password from info.txt)
        ├── profile.txt (ROT47 encoded)
        │   └── [ROT47 decode] → passphrase for technical.zip
        └── technical.zip (encrypted, password from profile.txt)
            └── cyberworld.txt (base64 encoded HTML)
                └── [base64 decode] → HTML table with user credentials
```

---

## Solving Approach

### Step 1 — Parse the MIME email

Open `email.txt` and identify the two base64-encoded attachments: `info.txt` and `proposal.zip`. Extract the base64 payload for each.

```python
import base64

# info.txt base64 payload (from email.txt line 30)
info_b64 = "PjFXKUU4NzBAPSZBRSgnIUE8Vy1XO1cpRCgmRVMoJi1BPSYtSCs2VUUKYA=="
info_raw = base64.b64decode(info_b64)
# Result: b">1W)E870@=&AE('!A<W-W;W)D(&ES(&-A=&-H+6UE\n`"
```

### Step 2 — Compute MD5 of `info.txt`

The MD5 is computed on the raw bytes obtained after base64 decoding:

```python
import hashlib
md5 = hashlib.md5(info_raw).hexdigest()
# Result: 6e0104c8679bfa6f4dc26f1d26ae96c2
```

### Step 3 — UU-decode `info.txt`

The decoded bytes follow the UU-encoding format. The first byte (`>`) encodes the line length (30 bytes). Apply UU decoding:

```python
line = info_raw.split(b'\n')[0]
length = line[0] - 32  # 30 bytes

result = bytearray()
i = 1
while i + 3 <= len(line):
    a = (line[i] - 32) & 0x3F
    b = (line[i+1] - 32) & 0x3F
    c = (line[i+2] - 32) & 0x3F
    d = (line[i+3] - 32) & 0x3F
    result.append((a << 2) | (b >> 4))
    result.append(((b & 0xf) << 4) | (c >> 2))
    result.append(((c & 0x3) << 6) | d)
    i += 4

plaintext = bytes(result[:length])
# Result: b"Great the password is catch-me"
```

### Step 4 — Extract `proposal.zip`

Base64-decode the `proposal.zip` payload from the email and save it to disk. Open the ZIP using the password `catch-me`:

```bash
7z e -pcatch-me proposal.zip
```

Contents:
- `profile.txt` — encrypted text file
- `technical.zip` — encrypted nested archive

### Step 5 — Decode `profile.txt` (ROT47)

The plaintext content of `profile.txt` is encoded with ROT47. ROT47 rotates all printable ASCII characters (0x21–0x7E) by 47 positions:

```python
def rot47(text):
    result = ""
    for c in text:
        if 33 <= ord(c) <= 126:
            result += chr((ord(c) - 33 + 47) % 94 + 33)
        else:
            result += c
    return result

ciphertext = "xu *~& $&rrt$$u&{{* #tps %wx$[ *~& p#t v~~s %~ v~ (x%w !p$$(~#s x} rp!x%p{ {x%%t#$i (t|pztx%"
print(rot47(ciphertext))
# Result: "IF YOU SUCCESSFULLY READ THIS, YOU ARE GOOD TO GO WITH PASSWORD IN CAPITAL LITTERS: WEMAKEIT"
```

### Step 6 — Extract `technical.zip`

Use the passphrase from `profile.txt` (`WEMAKEIT`) to open `technical.zip`:

```bash
7z e -pWEMAKEIT technical.zip
```

Contents:
- `cyberworld.txt` — base64-encoded HTML document
- `cyberworld/` — directory

### Step 7 — Decode `cyberworld.txt`

Base64-decode `cyberworld.txt` to obtain an HTML document embedded within a JavaScript `document.write()` call. The HTML contains a table of user credentials.

```python
import base64
with open("cyberworld.txt") as f:
    data = f.read().strip()
html = base64.b64decode(data).decode("utf-8")
# Result: <script type="text/javascript">document.write('<body>...<table>...credentials...</table>...')
```

---

## Answers

| # | Question | Answer |
|---|---|---|
| 1 | Email address Ilir corresponded with | `bledi@offsec.com` |
| 2 | Attachment file names | `info.txt`, `proposal.zip` |
| 3 | MD5 hash of `info.txt` (extracted) | `6e0104c8679bfa6f4dc26f1d26ae96c2` |
| 4 | Content of `info.txt` (human-readable) | `Great the password is catch-me` |
| 5 | Password to open `proposal.zip` | `catch-me` |
| 6 | Files inside `proposal.zip` | `2` (`profile.txt` and `technical.zip`) |
| 7 | Encoder type used in `profile.txt` | ROT47 |
| 8 | Password phrase in `profile.txt` | `WEMAKEIT` |
| 9 | File with user account information | `cyberworld.txt` |
| 10 | Language used to hide banking information | JavaScript |
| 11 | First username and password | Username: `andrian2014` / Password: `!234%^78` |
