# Steganography Detection and Hidden Data Extraction Lab

<p align="center">
  <strong>Digital Forensics Analysis with Paraben’s E3, StegExpose, OpenPuff, S-Tools, Xiao, and OpenStego</strong>
</p>

---

## Overview

This lab documents a forensic investigation into the suspected use of steganography on a Windows drive image. The investigation centered on Beverly Gates, an HR manager at Intricate Solutions, Inc. who was suspected of using concealed files as part of unauthorized activity.

Using Paraben’s E3 and several specialized steganography tools, I searched the drive image for known steganography software, exported suspicious image and audio files, identified carrier files containing hidden data, and extracted concealed contents while preserving the original evidence.

> **Training Scenario:** All names, evidence, and investigative activity in this repository are part of an authorized educational lab.

---

## Lab Objectives

- Detect known steganography tools through MD5 hash comparison.
- Identify renamed software even when its filename was changed.
- Export evidence with integrity-verification hash files.
- Scan images for possible least-significant-bit steganography.
- Compare audio files by size and hash value.
- Extract hidden data from BMP, GIF, and WAV carrier files.
- Use passphrases and encryption settings to recover concealed evidence.
- Document the forensic relevance of recovered files.

---

## Skills Demonstrated

- Steganography detection and steganalysis
- Forensic hash comparison
- Evidence export and integrity verification
- Paraben E3 Sorted Files Search
- MD5 hash-database analysis
- Command-line evidence scanning
- Least-significant-bit analysis
- Image and audio carrier examination
- Hidden-file extraction
- Evidence interpretation and reporting

---

## Lab Environment

| Component | Details |
|---|---|
| Workstation | Windows Server 2019 virtual workstation |
| Primary Forensic Tool | Paraben’s E3 |
| Steganalysis Tool | StegExpose |
| Extraction Tools | OpenPuff, S-Tools, Xiao, OpenStego |
| Evidence Source | Beverly Gates Windows drive image |
| Image Formats | BMP and GIF |
| Audio Format | WAV |
| Hash Type | MD5 |

---

## Tools Used

| Tool | Purpose |
|---|---|
| Paraben’s E3 | Drive-image analysis, sorted-file review, hash searching, metadata analysis, and evidence export |
| StegExpose | Detection of possible LSB steganography in image files |
| OpenPuff | Extraction of concealed data from an image carrier |
| S-Tools | Extraction of password-protected hidden data from image or audio carriers |
| Xiao | Extraction of concealed data from a WAV carrier |
| OpenStego | Extraction of hidden data during the independent challenge |
| Microsoft Paint | Visual inspection of a suspicious carrier image |
| Windows Media Player | Playback comparison of two WAV files |

---

# Section 1: Hands-On Investigation

## Part 1: Detect Steganography Software on the Drive Image

I opened the existing E3 case:

```text
C:\Users\Administrator\Documents\E3 Cases\Beverly Gates evidence
```

I attached:

```text
C:\E3\Beverly Gates evidence\Hash Database 1.pdh
```

I opened **Sorted Files Search**, selected the **Hash Databases** tab, moved `Hash Database 1.pdh` into the accepted-databases column, and ran the query.

The search returned:

```text
temp17.exe.exe
```

Although the file had been renamed, its MD5 hash matched the **OpenPuff steganography tool**. This demonstrated why hash-based detection is more reliable than filename analysis alone.

<p align="center">
  <img src="https://github.com/user-attachments/assets/891bc44a-bf51-49f5-b232-93b5288f0238"
       alt="iPhone mobile forensic investigation evidence"
       width="800">
</p>

<p align="center">
  <em>Figure 1: E3 identified `temp17.exe.exe` as OpenPuff despite the renamed filename.</em>
</p>

---

## Part 2: Detect Hidden Data in Image Files

I navigated to:

```text
Beverly Gates evidence
└── BG_evidence
    └── NTFS
        └── Root
            └── Users
                └── Beverly Gates
                    └── Dropbox
                        └── My PC (DROPBOX-RD1DJLI)
                            └── Documents
                                └── Received
```

I exported the `Received` folder from E3. The exported evidence included image files and text files containing MD5 values for integrity verification.

I scanned the folder with StegExpose:

```cmd
java -jar "C:\Program Files (x86)\StegExpose-master\StegExpose.jar" "C:\Users\Administrator\Documents\E3 Cases\Beverly Gates evidence\Received"
```

StegExpose identified:

```text
Lakeview.bmp
```

<p align="center">
  <img src="https://github.com/user-attachments/assets/5d2afd9d-dbb6-414b-963b-1d7d3dfa087d"
       alt="iPhone mobile forensic investigation evidence"
       width="800">
</p>

<p align="center">
  <em>Figure 2: StegExpose identified `Lakeview.bmp` as a suspected hidden-data carrier.</em>
</p>

---

## Visual Inspection of the Carrier Image

I opened `Lakeview.bmp` in Microsoft Paint. The image appeared normal during visual inspection, demonstrating that hidden data can be embedded without an obvious visible change.

<p align="center">
  <img src="https://github.com/user-attachments/assets/04bb999b-7018-4a75-ab31-98f154e522f1"
       alt="iPhone mobile forensic investigation evidence"
       width="800">
</p>

<p align="center">
  <em>Figure 3: The suspicious `Lakeview.bmp` carrier file displayed in Microsoft Paint.</em>
</p>

---

## Part 3: Extract Hidden Data with OpenPuff

I opened the `ReadMe` file in the exported `Received` folder and recorded the passphrase:

```text
landmarks
```

I launched OpenPuff and selected **Unhide Data**. I entered the recovered passphrase in the Cryptography A field, disabled the B and C fields, added `Lakeview.bmp` as the carrier, selected the maximum bitmap bit-setting option, and extracted the hidden file to the desktop.

| Field | Finding |
|---|---|
| Carrier | `Lakeview.bmp` |
| Extraction Tool | OpenPuff |
| Passphrase | `landmarks` |
| Extracted Filename | `locations.txt` |
| File Type | Text file (`.txt`) |
| Contents | A list of latitude and longitude coordinates associated with Chicago-area landmarks, intersections, businesses, and street addresses. |

<p align="center">
  <img src="https://github.com/user-attachments/assets/5ff34376-f03e-45d8-9075-6123863578b9"
       alt="iPhone mobile forensic investigation evidence"
       width="32%">
  <img src="https://github.com/user-attachments/assets/0b48a828-8a36-4e14-832a-7410b70f265e"
       alt="iPhone mobile forensic investigation evidence"
       width="32%">
  <img src="https://github.com/user-attachments/assets/2d2e7012-d5a0-4d28-bb87-fd6adb40b7c0"
       alt="iPhone mobile forensic investigation evidence"
       width="32%">
</p>

<p align="center">
  <em>Figure 4: The contents of the hidden file extracted from `Lakeview.bmp` using OpenPuff.</em>
</p>

### Investigative Significance

```text
The extracted locations.txt file contained latitude and longitude coordinates linked to Chicago-area streets, businesses, landmarks, and intersections. The fact that this information was hidden inside Lakeview.bmp showed that geographic data was intentionally concealed rather than stored as a normal text file.

Investigators could compare these locations with known vehicle-theft sites, recovered vehicles, surveillance records, and other evidence from the case. The coordinates alone did not prove criminal activity, but the list of concealed locations could help identify places associated with the suspect’s movements or planned activity.
```

---

# Section 2: Applied Learning

## Part 1: Detect Additional Steganography Software

I reopened the Beverly Gates E3 case and attached:

```text
C:\E3\Beverly Gates Evidence\Hash Database 2.pdh
```

I ran another Sorted Files Search. The query returned three results, including two files with the same hash associated with the same tool.

| Search Result | Description | Hash Relationship |
|---|---|---|
| `f_0002ef` | Open **Show Links** to identify the tool description | Same MD5 and file size as `123rt.exe.exe`; the files are identical copies with different names |
| `123rt.exe.exe` | Same description as `f_0002ef` because the MD5 hashes match | Same MD5 and file size as `f_0002ef`; duplicate file |
| `funnycat.exe.exe` | Open **Show Links** to identify the separate tool description | Different MD5 and file size; separate file or steganography tool |

<p align="center">
  <img src="https://github.com/user-attachments/assets/8985a75b-d1a0-4fb2-8089-b83396aa86b3"
       alt="iPhone mobile forensic investigation evidence"
       width="800">
</p>

  <em>Figure 5: The second E3 hash search and the description of the identified steganography software.</em>
</p>

---

## Part 2: Detect Hidden Data in Image and Audio Files

I navigated to:

```text
Beverly Gates evidence
└── BG_evidence
    └── NTFS
        └── Root
            └── Users
                └── Beverly Gates
                    └── Dropbox
                        └── My PC (DESKTOP-RD1DJLJ)
                            └── Documents
                                └── Sent
```

The folder contained five GIF images, two WAV files, and supporting system artifacts. I exported the folder and scanned it with StegExpose.

### Suspicious Image Finding

```text
dB9olster.gif
```

<p align="center">
  <img src="https://github.com/user-attachments/assets/60275f79-d489-4521-9d8a-36b3f17749d6"
       alt="iPhone mobile forensic investigation evidence"
       width="800">
</p>

<p align="center">
  <em>Figure 6: StegExpose identified the GIF image suspected of containing concealed data.</em>
</p>

---

## WAV File Comparison

I listened to both WAV files in Windows Media Player. They sounded the same, but E3 showed different file sizes and MD5 hashes. The larger WAV file was considered the stronger hidden-data candidate.

| WAV File | Size | MD5 | Assessment |
|---|---:|---|---|
| `laugh_x.wav` | `14,204 bytes` | `35D2A83D90A91FBBF70B256B78B39D90` | Baseline or smaller audio file |
| `laugh_x1.wav` | `14,205 bytes` | `5D5286FEA9F45946A15F986FDE0A5E0C` | Suspected carrier file because it is slightly larger and has a different MD5 hash |

<p align="center">
  <img src="https://github.com/user-attachments/assets/ba5b2b1d-5b15-4516-a190-a10ed4a4d500"
       alt="iPhone mobile forensic investigation evidence"
       width="800">
</p>

<p align="center">
  <em>Figure 7: The WAV files appeared to contain the same audio but had different sizes and MD5 hashes.</em>
</p>

---

## Part 3: Extract Hidden Data with S-Tools

I opened `key.txt` from the Sent folder. The file contained passphrases and references to algorithms, including Triple DES and RC2.

I loaded the suspicious image into S-Tools, selected **Reveal**, entered the matching passphrase and algorithm, and saved the extracted file.

| Field | Finding |
|---|---|
| Carrier Image | `dB9olser.gif` |
| Extraction Tool | S-Tools |
| Passphrase | `besttool` |
| Algorithm | `Triple DES` |
| Extracted Filename | `What is tor.docx` |
| Contents | A document explaining Tor, including how it provides anonymous communication, hides IP addresses, supports access to the dark web, works with VPNs, and routes encrypted traffic through multiple systems. |

<p align="center">
  <img src="https://github.com/user-attachments/assets/51bf97e1-b1f1-44fc-9841-916319de57a9"
       alt="iPhone mobile forensic investigation evidence"
       width="24%">
  <img src="https://github.com/user-attachments/assets/e8403332-eced-4f5c-bf05-09cca0254678"
       alt="iPhone mobile forensic investigation evidence"
       width="24%">
  <img src="https://github.com/user-attachments/assets/9bac79a2-ea94-4697-be4a-f0f3ee079b03"
       alt="iPhone mobile forensic investigation evidence"
       width="24%">
  <img src="https://github.com/user-attachments/assets/81323ccf-983c-4087-ae9c-1fa9d44435a3"
       alt="iPhone mobile forensic investigation evidence"
       width="24%">
</p>

<p align="center">
  <em>Figure 8: The contents of the hidden file extracted using S-Tools.</em>
</p>

---

## Extract Hidden Data with Xiao

I opened Xiao, selected **Extract Files**, loaded the suspected WAV carrier, entered the correct password, and saved the output as:

```text
WAV_extract
```

| Field | Finding |
|---|---|
| Carrier Audio | `laugh_x1.wav` |
| Extraction Tool | Xiao |
| Password | `partners` |
| Saved Output | `WAV_extract` |
| Contents | A hidden text file approximately `1.094 KB` in size. The exact text is not visible in these screenshots and must be confirmed by opening `WAV_extract` in Notepad. |

<p align="center">
  <img src="https://github.com/user-attachments/assets/d95484cc-1292-4ce9-96bc-e7e1942fb11f"
       alt="iPhone mobile forensic investigation evidence"
       width="800">
</p>

<p align="center">
  <em>Figure 9: The contents of the hidden file extracted from the WAV carrier using Xiao.</em>
</p>

### Investigative Significance

```text
The file extracted from dB9olser.gif was a document explaining Tor, anonymous communication, IP-address concealment, VPN use, and access to the dark web. Its presence inside a hidden image could indicate an interest in concealing online activity or communicating anonymously.

The second hidden file was extracted from laugh_x1.wav using Xiao and the password partners. Although the exact text still needs to be confirmed by opening WAV_extract, the recovery of a concealed text file from an audio carrier showed that multiple file types were being used to hide information. Together, these findings demonstrated deliberate use of steganography and supported further examination of the recovered files and the suspect’s online activity.

```

---

# Section 3: Challenge and Analysis

## Part 1: Detect More Hidden Data

I independently analyzed:

```text
C:\Steganography Evidence\Section 3
```

I ran StegExpose and identified two files suspected of containing concealed data.

| Suspected Carrier | Result |
|---|---|
| `chicago.bmp` | Hidden-data indicators detected; StegExpose estimated approximately `114,062 bytes` of concealed data |
| `chicago1.bmp` | Hidden-data indicators detected; StegExpose estimated approximately `57,942 bytes` of concealed data |

<p align="center">
  <img src="https://github.com/user-attachments/assets/62c3349a-8384-4526-a196-1b02a8ed3ea7"
       alt="iPhone mobile forensic investigation evidence"
       width="800">
</p>

---

## Part 2: Extract More Hidden Data with OpenStego

I used the cipher keys stored in the Section 3 folder to extract the hidden content from both carrier images using OpenStego.

### First Extracted File

| Field | Finding |
|---|---|
| Carrier | `chicago.bmp` |
| Cipher Key | `bigdeal` using `AES128` |
| Extracted File | `location_florida_snow.png` |
| Contents | A map image marking the Garfield Park Conservatory area in Chicago, including nearby gardens, athletic fields, and Interstate 290. |

<p align="center">
  <img src="https://github.com/user-attachments/assets/131622ad-0ad3-4417-9a09-e3db1a9cb17a"
       alt="iPhone mobile forensic investigation evidence"
       width="800">
</p>

<p align="center">
  <em>Figure 10: The first hidden file extracted from the Section 3 evidence using OpenStego.</em>
</p>

### Second Extracted File

| Field | Finding |
|---|---|
| Carrier | `chicago1.bmp` |
| Cipher Key | `Mondayevening` using `AES128` |
| Extracted File | `location_fenty.png` |
| Contents | A map image marking Jackson Park Football Fields and the nearby Jackson Park Community Farm in Chicago. |

<p align="center">
  <img src="https://github.com/user-attachments/assets/89a8e97e-06b5-4223-a411-f9c2ccc9d0fe"
       alt="iPhone mobile forensic investigation evidence"
       width="800">
</p>

<p align="center">
  <em>Figure 11: The second hidden file extracted from the Section 3 evidence using OpenStego.</em>
</p>

---

# Evidence Summary

| Evidence | Tool | Result | Significance |
|---|---|---|---|
| `temp17.exe.exe` | E3 and Hash Database 1 | Identified as OpenPuff | Shows steganography software was present despite renaming |
| `Lakeview.bmp` | StegExpose | Suspected hidden-data carrier | Required extraction with OpenPuff |
| OpenPuff output | OpenPuff | Hidden file recovered | Contents may support the investigation |
| Additional software files | E3 and Hash Database 2 | Three hash matches | Shows additional steganography-tool activity |
| Suspicious GIF | StegExpose and S-Tools | Hidden file recovered | Demonstrates image-based concealment |
| Suspicious WAV | E3 and Xiao | Hidden file recovered | Demonstrates audio-based concealment |
| Section 3 carrier 1 | StegExpose and OpenStego | Hidden file recovered | Independent challenge result |
| Section 3 carrier 2 | StegExpose and OpenStego | Hidden file recovered | Independent challenge result |

---

# Key Findings

## Renaming Did Not Defeat Hash Detection

The file `temp17.exe.exe` did not reveal its actual purpose through its filename. The MD5 hash database identified it as OpenPuff.

## Normal-Looking Files Can Carry Hidden Data

`Lakeview.bmp` appeared to be an ordinary image in Microsoft Paint. StegExpose and OpenPuff showed that it could still contain concealed data.

## Audio Files Require Metadata Comparison

The WAV files sounded the same, but their sizes and MD5 hashes were different. This showed the importance of metadata and hash comparison instead of relying only on playback.

## Multiple Tools Were Required

The investigation required E3, StegExpose, OpenPuff, S-Tools, Xiao, and OpenStego because no single tool covered every carrier type and extraction method.

---

> **Ethical Use Notice:** This repository documents an authorized educational forensic investigation. Steganography tools and evidence-extraction techniques should only be used on systems and data for which proper authorization has been granted.+
