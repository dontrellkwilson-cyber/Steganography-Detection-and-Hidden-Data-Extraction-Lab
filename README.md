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

## Section 1: Hands-On Investigation

### Part 1: Detect Steganography Software on the Drive Image

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

Although the file had been renamed, its MD5 hash matched the known OpenPuff entry in the attached hash database. This demonstrated that exact known-file hash matching can identify a renamed executable when its contents remain unchanged.

<p align="center">
  <img src="https://github.com/user-attachments/assets/891bc44a-bf51-49f5-b232-93b5288f0238"
       alt="Paraben E3 identifying the renamed OpenPuff executable through an MD5 hash match"
       width="800">
</p>

<p align="center">
  <em>Figure 1: E3 identified `temp17.exe.exe` as OpenPuff despite the renamed filename.</em>
</p>

---

### Part 2: Detect Hidden Data in Image Files

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

StegExpose flagged the following file as a suspected hidden-data carrier:

```text
Lakeview.bmp
```

<p align="center">
  <img src="https://github.com/user-attachments/assets/5d2afd9d-dbb6-414b-963b-1d7d3dfa087d"
       alt="StegExpose command-line results flagging Lakeview BMP as a suspected carrier"
       width="800">
</p>

<p align="center">
  <em>Figure 2: StegExpose flagging `Lakeview.bmp` as a suspected hidden-data carrier.</em>
</p>

---

#### Visual Inspection of the Carrier Image

I opened `Lakeview.bmp` in Microsoft Paint. The image appeared normal during visual inspection. This showed that visual review alone was not enough to determine whether the file contained concealed data.

<p align="center">
  <img src="https://github.com/user-attachments/assets/04bb999b-7018-4a75-ab31-98f154e522f1"
       alt="Microsoft Paint displaying the visually normal Lakeview BMP carrier image"
       width="800">
</p>

<p align="center">
  <em>Figure 3: The suspicious `Lakeview.bmp` carrier file displayed in Microsoft Paint.</em>
</p>

---

### Part 3: Extract Hidden Data with OpenPuff

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
       alt="OpenPuff configuration used to extract hidden data from Lakeview BMP"
       width="32%">
  <img src="https://github.com/user-attachments/assets/0b48a828-8a36-4e14-832a-7410b70f265e"
       alt="OpenPuff successfully extracting locations txt from the Lakeview carrier"
       width="32%">
  <img src="https://github.com/user-attachments/assets/2d2e7012-d5a0-4d28-bb87-fd6adb40b7c0"
       alt="Extracted locations txt file displaying Chicago-area coordinates"
       width="32%">
</p>

<p align="center">
  <em>Figure 4: The contents of the hidden file extracted from `Lakeview.bmp` using OpenPuff.</em>
</p>

#### Investigative Significance

The extracted `locations.txt` file contained latitude and longitude coordinates linked to Chicago-area streets, businesses, landmarks, and intersections. Its recovery from `Lakeview.bmp` confirmed that the carrier contained concealed geographic data.

Investigators could compare the locations with known vehicle-theft sites, recovered vehicles, surveillance records, and other case evidence. The coordinates alone did not prove criminal activity, but they provided geographic leads for further investigation.

---

## Section 2: Applied Learning

### Part 1: Detect Additional Steganography Software

I reopened the Beverly Gates E3 case and attached:

```text
C:\E3\Beverly Gates Evidence\Hash Database 2.pdh
```

I ran another Sorted Files Search. The query returned three results, including two files with the same hash associated with the same tool.

| Search Result | Description | Hash Relationship |
|---|---|---|
| `f_0002ef` | Open **Show Links** to identify the tool description | Same MD5 and file size as `123rt.exe.exe`; strongly indicates the same file content under a different name |
| `123rt.exe.exe` | Same description as `f_0002ef` because the MD5 hashes match | Same MD5 and file size as `f_0002ef`; strongly indicates duplicate content |
| `funnycat.exe.exe` | Open **Show Links** to identify the separate tool description | Different MD5 and file size; separate file or steganography tool |

<p align="center">
  <img src="https://github.com/user-attachments/assets/8985a75b-d1a0-4fb2-8089-b83396aa86b3"
       alt="Paraben E3 displaying additional known-file hash matches from Hash Database 2"
       width="800">
</p>

<p align="center">
  <em>Figure 5: The second E3 hash search and the descriptions associated with the identified files.</em>
</p>

---

### Part 2: Detect Hidden Data in Image and Audio Files

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

The folder contained five GIF images, two WAV files, and supporting system artifacts. I exported the folder and scanned the image files with StegExpose.

#### Suspicious Image Finding

```text
dB9olster.gif
```

<p align="center">
  <img src="https://github.com/user-attachments/assets/60275f79-d489-4521-9d8a-36b3f17749d6"
       alt="StegExpose results flagging dB9olster GIF as a suspected carrier"
       width="800">
</p>

<p align="center">
  <em>Figure 6: StegExpose identified the GIF image suspected of containing concealed data.</em>
</p>

---

#### WAV File Comparison

I listened to both WAV files in Windows Media Player. They sounded the same, but E3 showed different file sizes and MD5 hashes. The differences proved that the files were not identical. Because `laugh_x1.wav` was slightly larger, I selected it as the first candidate for extraction testing.

| WAV File | Size | MD5 | Assessment |
|---|---:|---|---|
| `laugh_x.wav` | `14,204 bytes` | `35D2A83D90A91FBBF70B256B78B39D90` | Baseline or smaller audio file |
| `laugh_x1.wav` | `14,205 bytes` | `5D5286FEA9F45946A15F986FDE0A5E0C` | Selected for extraction testing because it differed in size and hash |

<p align="center">
  <img src="https://github.com/user-attachments/assets/ba5b2b1d-5b15-4516-a190-a10ed4a4d500"
       alt="Paraben E3 comparing the sizes and MD5 hashes of two WAV files"
       width="800">
</p>

<p align="center">
  <em>Figure 7: The WAV files appeared to contain the same audio but had different sizes and MD5 hashes.</em>
</p>

---

### Part 3: Extract Hidden Data with S-Tools

I opened `key.txt` from the Sent folder. The file contained passphrases and references to algorithms, including Triple DES and RC2.

I loaded the suspicious image into S-Tools, selected **Reveal**, entered the matching passphrase and algorithm, and saved the extracted file.

| Field | Finding |
|---|---|
| Carrier Image | `dB9olster.gif` |
| Extraction Tool | S-Tools |
| Passphrase | `besttool` |
| Algorithm | `Triple DES` |
| Extracted Filename | `What is tor.docx` |
| Contents | A document explaining Tor, including how it provides anonymous communication, hides IP addresses, supports access to the dark web, works with VPNs, and routes encrypted traffic through multiple systems. |

<p align="center">
  <img src="https://github.com/user-attachments/assets/51bf97e1-b1f1-44fc-9841-916319de57a9"
       alt="S-Tools displaying the suspected GIF carrier"
       width="24%">
  <img src="https://github.com/user-attachments/assets/e8403332-eced-4f5c-bf05-09cca0254678"
       alt="S-Tools Reveal dialog configured with the recovered passphrase and Triple DES"
       width="24%">
  <img src="https://github.com/user-attachments/assets/9bac79a2-ea94-4697-be4a-f0f3ee079b03"
       alt="S-Tools extracting the hidden What is tor document from the GIF carrier"
       width="24%">
  <img src="https://github.com/user-attachments/assets/81323ccf-983c-4087-ae9c-1fa9d44435a3"
       alt="Extracted document discussing Tor and anonymous communication"
       width="24%">
</p>

<p align="center">
  <em>Figure 8: The contents of the hidden file extracted using S-Tools.</em>
</p>

---

### Extract Hidden Data with Xiao

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
       alt="Xiao successfully extracting WAV_extract from laugh_x1 WAV"
       width="800">
</p>

<p align="center">
  <em>Figure 9: Xiao successfully extracting the hidden file `WAV_extract` from the WAV carrier.</em>
</p>

#### Investigative Significance

The file extracted from `dB9olster.gif` was a document discussing Tor, anonymous communication, IP-address concealment, VPN use, and access to the dark web. Its forensic relevance came from being concealed inside the GIF and from its relationship to the surrounding case evidence. The presence of information about Tor or VPNs alone would not establish unauthorized or criminal activity.

The second hidden file was extracted from `laugh_x1.wav` using Xiao and the password `partners`. Although the exact text still needed to be confirmed by opening `WAV_extract`, the successful recovery established that the WAV file functioned as a carrier. Together, the findings showed that multiple carrier types were used to conceal data and supported further examination of the recovered files.

---

## Section 3: Challenge and Analysis

### Part 1: Detect More Hidden Data

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
       alt="StegExpose results flagging chicago BMP and chicago1 BMP during the challenge"
       width="800">
</p>

---

### Part 2: Extract More Hidden Data with OpenStego

I used the cipher keys stored in the Section 3 folder to extract the hidden content from both carrier images using OpenStego.

#### First Extracted File

| Field | Finding |
|---|---|
| Carrier | `chicago.bmp` |
| Cipher Key | `bigdeal` using `AES128` |
| Extracted File | `location_florida_snow.png` |
| Contents | A map image marking the Garfield Park Conservatory area in Chicago, including nearby gardens, athletic fields, and Interstate 290. |

<p align="center">
  <img src="https://github.com/user-attachments/assets/131622ad-0ad3-4417-9a09-e3db1a9cb17a"
       alt="OpenStego extracted map image marking the Garfield Park Conservatory area"
       width="800">
</p>

<p align="center">
  <em>Figure 10: The first hidden file extracted from the Section 3 evidence using OpenStego.</em>
</p>

#### Second Extracted File

| Field | Finding |
|---|---|
| Carrier | `chicago1.bmp` |
| Cipher Key | `Mondayevening` using `AES128` |
| Extracted File | `location_fenty.png` |
| Contents | A map image marking Jackson Park Football Fields and the nearby Jackson Park Community Farm in Chicago. |

<p align="center">
  <img src="https://github.com/user-attachments/assets/89a8e97e-06b5-4223-a411-f9c2ccc9d0fe"
       alt="OpenStego extracted map image marking the Jackson Park Football Fields area"
       width="800">
</p>

<p align="center">
  <em>Figure 11: The second hidden file extracted from the Section 3 evidence using OpenStego.</em>
</p>

---

## Evidence Summary

| Evidence | Tool | Result | Significance |
|---|---|---|---|
| `temp17.exe.exe` | E3 and Hash Database 1 | Exact MD5 match to the known OpenPuff entry | Identified renamed steganography software when the file contents remained unchanged |
| `Lakeview.bmp` | StegExpose and OpenPuff | Flagged for review and confirmed as a carrier through extraction | Contained concealed geographic data |
| `locations.txt` | OpenPuff | Coordinate list recovered from `Lakeview.bmp` | Provides geographic leads for comparison with other case evidence |
| `f_0002ef`, `123rt.exe.exe`, and `funnycat.exe.exe` | E3 and Hash Database 2 | Three known-file hash matches | Identified additional files associated with steganography tools |
| `dB9olster.gif` | StegExpose and S-Tools | `What is tor.docx` recovered | Demonstrates password-protected image-based concealment |
| `laugh_x1.wav` | E3 and Xiao | `WAV_extract` recovered | Confirms audio-based concealment; exact text still requires review |
| `chicago.bmp` | StegExpose and OpenStego | `location_florida_snow.png` recovered | Independent challenge extraction containing a mapped location |
| `chicago1.bmp` | StegExpose and OpenStego | `location_fenty.png` recovered | Independent challenge extraction containing a second mapped location |

---

## Key Findings

### Renaming Did Not Defeat Hash Detection

The file `temp17.exe.exe` did not reveal its actual purpose through its filename. The MD5 hash database identified it as OpenPuff.

### Normal-Looking Files Can Carry Hidden Data

`Lakeview.bmp` appeared to be an ordinary image in Microsoft Paint. StegExpose flagged it for review, and the successful OpenPuff extraction confirmed that it contained concealed data.

### Audio Files Require Metadata Comparison

The WAV files sounded the same, but their sizes and MD5 hashes were different. This showed that playback alone was insufficient and that metadata comparison could help identify files requiring additional extraction testing.

### Multiple Tools Were Required

The investigation required E3, StegExpose, OpenPuff, S-Tools, Xiao, and OpenStego because no single tool covered every carrier type and extraction method.

---

> **Ethical Use Notice:** This repository documents an authorized educational forensic investigation. Steganography tools and evidence-extraction techniques should only be used on systems and data for which proper authorization has been granted.
