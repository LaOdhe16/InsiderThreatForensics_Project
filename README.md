# 🔍 Digital Forensics Lab — Insider Threat Scenario
> Simulation & Analysis of a Data Exfiltration Incident by an Internal Employee

![Forensics](https://img.shields.io/badge/Platform-Ubuntu%2026.04-orange)
![Tool](https://img.shields.io/badge/Analysis-Autopsy%204.23-blue)
![Status](https://img.shields.io/badge/Status-Completed-green)
![Academic](https://img.shields.io/badge/Type-Academic%20Lab-purple)

---

## 📋 Project Description

This project is a complete simulation of an **Insider Threat** scenario in a digital forensics context. The scenario was built on a Ubuntu Desktop 26.04 virtual machine, covering artifact planting, anti-forensic techniques, and a full forensic analysis using the Autopsy Forensic Browser.

The simulated case involves a suspected exfiltration of sensitive company data by an employee (Finance & Admin Staff) prior to submitting a sudden resignation. All data, company names, and identities are **fictional** and created solely for academic purposes.

---

## 🏢 Case Summary

| Attribute | Description |
|---|---|
| **Organization** | PT Maju Sejahtera (fictional) — multifinance company, Bandung |
| **Subject** | Andi Rahman |
| **System Username** | `rahman` |
| **Hostname** | `FIN-WS03` |
| **Position** | Data & Finance Administration Staff |
| **Incident Date** | Sunday, June 14, 2026 (night) |
| **Report Date** | Monday, June 15, 2026 |

### Brief Chronology
On the night of Sunday, June 14, 2026, an employee named Andi Rahman was seen at the office outside working hours. The following day he submitted a sudden resignation with only 1 day's notice (violating the company's 30-day notice policy). Management suspected unusual activity related to a sensitive company data folder (`Finance_Data`). The workstation `FIN-WS03` and an external storage device found at his work area were seized for digital forensic investigation.

---

## 🎯 Examination Objectives

1. Determine whether there is evidence of sensitive company data being copied to an external storage device — including evidence of when the device was mounted/unmounted and its identity.
2. Identify unusual user activity in the period prior to the report being filed (outside normal working hours).
3. Look for indications of anti-forensic / trace-deletion attempts, if any.
4. Perform recovery of data that may have been deleted.
5. Construct a chronological timeline of events based on the artifacts found.
6. Conclude whether the alleged data exfiltration is proven, along with supporting evidence.
7. Extract the `cache_data.tmp` file found on the external storage device, identify its actual file type, and uncover its contents — the clue for opening this file may be hidden somewhere unexpected within the evidence provided.

---

## 📁 Repository Structure

```
Insider-Threat-Forensics/
│
├── README.md                                      ← Main documentation (this file)
│
├── docs/
│   ├── Case_Scenario_PT_Maju_Sejahtera.pdf        ← Case brief handed to the investigator
│   └── Mysterious_Note.txt                        ← Additional clue handed to the investigator
├── notes/
│   ├── timezone_normalization.md                  ← Guide to normalizing timestamps across sources
│   └── analysis_methodology.md                    ← Methodology & ingest module configuration
│
└── evidence/
    └── README_evidence.md                         ← Description of .dd files (hosted externally)
```

---

## 🛠️ Tools & Environment

| Tool | Version | Function |
|---|---|---|
| Oracle VirtualBox | 7.x | VM virtualization platform used to build the scenario |
| Ubuntu Desktop | 26.04 LTS | Incident simulation environment (victim workstation) |
| Autopsy | 4.23 | Primary forensic analysis platform for the `.dd` images |
| The Sleuth Kit (TSK) | Built into Autopsy | ext4 & FAT32 filesystem parsing |
| Kali Linux | Rolling | Supplementary journal log analysis (`journalctl`) |

---

## 🔬 Autopsy Analysis Guide

### Case Setup
1. Open Autopsy → **New Case**
2. Enter case name: `Insider_Threat_FINWS03`
3. **Add Data Source** → Disk Image or VM File → select `FIN-WS03_disk.dd`
4. Enable Ingest Modules:
   - ✅ Recent Activity
   - ✅ File Type Identification
   - ✅ Extension Mismatch Detector
   - ✅ Embedded File Extraction
   - ✅ Keyword Search 
5. Once ingest completes, **Add Data Source** again → select `Flashdisk.dd` as a second source in the same case

---

## 🗝️ Timezone Normalization

Timezone differences across evidence sources are a critical challenge in this analysis:

| Source | Storage Format | Conversion to Scenario Time (WIB) |
|---|---|---|
| **ext4 filesystem** (`FIN-WS03_disk.dd`) | UTC | Autopsy display (WIB) **−7 hours** |
| **FAT32** (`Flashdisk.dd`) | Local time (as-is) | Used directly without conversion |
| **auth.log & journalctl** | UTC+0 | **+7 hours** to obtain WIB |

See `notes/timezone_normalization.md` for the full conversion methodology and worked examples.

---

## 📂 Digital Evidence

| File | Size | Description |
|---|---|---|
| `FIN-WS03_disk.dd` | ~28 GB | Full forensic image of the FIN-WS03 workstation (ext4, Ubuntu 26.04) |
| `Flashdisk.dd` | ~1 GB | Forensic image of the external storage device (FAT32) |

> 📥 **Download**: The `.dd` files are available on [Google Drive](https://drive.google.com/drive/folders/1ekd4_4lBpv01b7sBcfUerY0vs8_WX0Pw?usp=sharing)
>
> ⚠️ Files are not included in the GitHub repository as they exceed the size limit (100MB/file).

---

## ⚠️ Disclaimer

> All names, data, organizations, and identities in this project are **fictional** and created solely for academic purposes in a Digital Forensics & Investigation course. No real personal data was used.

---

## 👤 Author

**Course**: Digital Forensics & Investigation
**Platform**: Ubuntu Desktop 26.04 + Autopsy 4.23 + Oracle VirtualBox

---

*"Traces never lie. Only people try to."*
