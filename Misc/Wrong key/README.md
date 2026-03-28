# Wrong Key

**Category:** Misc
**Points:** 100
**Difficulty:** Hard

## Overview

The archive contains a decoy along with an encrypted file that requires the correct key file to decrypt. The challenge description ("Decoy") indicates that an obvious or first-found key is intentionally misleading. The objective is to identify the genuine key and use it to decrypt the protected content.

**Provided Files:**
- `misc_hard_keyfile.zip` — Archive containing an encrypted file and one or more key candidates

---

## Technical Analysis

Key-file-based encryption is common in tools such as:
- **VeraCrypt / TrueCrypt**: Volumes requiring a specific key file in addition to (or instead of) a password
- **OpenSSL**: AES or RSA encryption requiring a key file
- **GPG**: Asymmetric encryption with a key file
- **Custom XOR or stream ciphers**: Where the key file provides the keystream

The presence of a decoy key file means the archive likely contains multiple key candidates, only one of which successfully decrypts the ciphertext. Identifying the correct key requires either trial-and-error or analyzing metadata (file size, hash, modification time) of the key candidates.

---

## Solving Approach

### Step 1 — Extract the archive

```bash
unzip misc_hard_keyfile.zip -d misc_hard/
ls -la misc_hard/
```

### Step 2 — Identify all files

```bash
file misc_hard/*
xxd misc_hard/* | head -20
```

Determine the encryption method and identify the encrypted file versus the key file(s).

### Step 3 — Identify decoy vs. genuine key

Compare the key file candidates:

```bash
md5sum misc_hard/*
sha256sum misc_hard/*
ls -la misc_hard/
```

Look for differences in file size, hash values, or metadata that distinguish the genuine key from the decoy.

### Step 4 — Attempt decryption with each key

Try each key file candidate with the appropriate decryption tool. For example, with OpenSSL:

```bash
openssl enc -d -aes-256-cbc -in encrypted_file -out decrypted -kfile keyfile_candidate
```

Or for XOR-based encryption:

```python
with open("ciphertext", "rb") as c, open("keyfile", "rb") as k:
    ct = c.read()
    key = k.read()
    plaintext = bytes(ct[i] ^ key[i % len(key)] for i in range(len(ct)))
    print(plaintext)
```

### Step 5 — Validate and retrieve the flag

The successful decryption will produce readable output containing the flag.

---

## Expected Answer Format

Standard flag format used throughout the competition.
