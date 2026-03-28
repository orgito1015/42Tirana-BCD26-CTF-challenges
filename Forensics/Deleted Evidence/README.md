# Deleted Evidence

**Category:** Forensics
**Points:** 60

## Overview

An attacker accessed a workstation and attempted to delete incriminating files. A forensic image of the disk was captured before the attacker could fully cover their tracks. The objective is to recover the deleted evidence.

**Provided Files:**
- `forensic_disk.img` — Raw disk image of the compromised workstation

---

## Technical Analysis

Deleted files on a filesystem are not immediately overwritten; the directory entry is marked as deleted, but the data blocks remain on disk until reallocated. Standard forensic tools can scan for these orphaned blocks and reconstruct file content.

**Filesystem considerations:**
- Common desktop filesystems (NTFS, ext4, FAT32) handle deletion differently
- NTFS marks the MFT entry as unallocated; the actual data clusters persist
- Tools such as Autopsy and The Sleuth Kit can enumerate deleted entries and carve data

---

## Solving Approach

### Step 1 — Inspect the disk image

```bash
file forensic_disk.img
fdisk -l forensic_disk.img
```

Identify the partition table and filesystem type.

### Step 2 — Mount or analyze with The Sleuth Kit

```bash
# List filesystem information
fsstat -i raw forensic_disk.img

# List all files including deleted entries
fls -r -d forensic_disk.img
```

The `-d` flag lists only deleted files. Note the inode numbers of any interesting entries.

### Step 3 — Recover deleted files

```bash
# Extract a file by its inode number
icat forensic_disk.img <inode_number> > recovered_file
```

Alternatively, use Autopsy to browse the image graphically:

```bash
autopsy &
# Open forensic_disk.img and navigate to the Deleted Files section
```

### Step 4 — Carve unallocated space

If directory entries have been wiped, use file carving to recover data based on magic bytes:

```bash
foremost -t all -i forensic_disk.img -o /output/
```

### Step 5 — Examine recovered content

Inspect any recovered text files, documents, or archives for the flag.

---

## Expected Answer Format

The flag is embedded within the recovered deleted file content.
