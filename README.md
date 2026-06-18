# 🔍 Digital Forensics Lab — Insider Threat Scenario
> Simulasi & Analisis Insiden Eksfiltrasi Data oleh Karyawan Internal

![Forensics](https://img.shields.io/badge/Platform-Ubuntu%2026.04-orange)
![Tool](https://img.shields.io/badge/Analysis-Autopsy%204.23-blue)
![Status](https://img.shields.io/badge/Status-Completed-green)
![Academic](https://img.shields.io/badge/Type-Academic%20Lab-purple)

---

## 📋 Deskripsi Project

Project ini merupakan simulasi lengkap skenario **Insider Threat** dalam konteks forensik digital. Skenario dibangun dari nol di atas virtual machine Ubuntu Desktop 26.04, mencakup proses penanaman artefak, teknik anti-forensik, hingga analisis forensik menyeluruh menggunakan Autopsy Forensic Browser.

Kasus yang disimulasikan adalah dugaan eksfiltrasi data sensitif perusahaan oleh seorang karyawan (Staff Finance & Admin) sebelum mengajukan pengunduran diri mendadak. Seluruh data, nama perusahaan, dan identitas bersifat **fiktif** dan dibuat semata-mata untuk keperluan akademik.

---

## 🏢 Ringkasan Kasus

| Atribut | Keterangan |
|---|---|
| **Organisasi** | PT Maju Sejahtera (fiktif) — perusahaan multifinance, Bandung |
| **Subjek** | Andi Rahman |
| **Username Sistem** | `rahman` |
| **Hostname** | `FIN-WS03` |
| **Jabatan** | Staff Administrasi Data & Keuangan |
| **Tanggal Insiden** | Minggu, 14 Juni 2026 (malam) |
| **Tanggal Pelaporan** | Senin, 15 Juni 2026 |

### Kronologi Singkat
Pada malam Minggu 14 Juni 2026, karyawan bernama Andi Rahman terlihat berada di kantor di luar jam kerja. Keesokan harinya ia mengajukan resign mendadak dengan notice hanya 1 hari (melanggar kebijakan 30 hari). Manajemen mencurigai adanya aktivitas tidak wajar terkait folder data sensitif perusahaan (`Finance_Data`). Workstation `FIN-WS03` dan sebuah media penyimpanan eksternal disita untuk investigasi forensik digital.

---

## 🎯 Tujuan Pemeriksaan

1. Tentukan apakah ada bukti penyalinan data sensitif perusahaan ke media penyimpanan eksternal — termasuk bukti waktu pemasangan/pelepasan device dan identitas device tersebut.
2. Identifikasi aktivitas pengguna yang tidak wajar pada periode sebelum laporan diajukan (di luar jam kerja normal).
3. Cari indikasi upaya penghapusan jejak/anti-forensik, jika ada.
4. Lakukan recovery terhadap data yang mungkin sudah dihapus.
5. Susun timeline kronologis kejadian berdasarkan artefak yang ditemukan.
6. Simpulkan apakah dugaan eksfiltrasi data terbukti, beserta bukti pendukungnya.
7. Ekstrak file `cache_data.tmp` yang ditemukan pada media penyimpanan eksternal, identifikasi tipe file sesungguhnya, dan ungkap isinya — petunjuk untuk membuka file tersebut mungkin tersembunyi di tempat yang tidak terduga pada barang bukti yang diserahkan.

---

## 📁 Struktur Repositori

```
Insider-Threat-Forensics/
│
├── README.md                          ← Dokumentasi utama (file ini)
│
├── docs/
│   ├── Skenario_Kasus_PT_Maju_Sejahtera.pdf     ← Lembar soal untuk investigator
│   ├── Jawaban_Tujuan_Pemeriksaan.pdf            ← Laporan analisis lengkap
│   └── Catatan_Misterius.txt                     ← Clue tambahan untuk investigator
│
├── screenshots/
│   ├── 01_flashdisk_cache_data_tmp.png           ← Temuan cache_data.tmp di Autopsy
│   ├── 02_journalctl_flash_disk_detected.png     ← Log kernel deteksi USB
│   ├── 03_extension_mismatch_encryption.png      ← Extension mismatch & encryption detected
│   ├── 04_flashdisk_timestamp.png                ← Timestamp cache_data.tmp di FAT32
│   ├── 05_bash_history_empty.png                 ← .bash_history size=0
│   ├── 06_timestomping_data_nasabah.png          ← Anomali timestamp (timestomping)
│   ├── 07_authlog_sysupdate_commands.png         ← auth.log rekam perintah pelaku
│   ├── 08_authlog_cp_command.png                 ← Perintah cp ke flashdisk di auth.log
│   └── 09_timeline_autopsy.png                   ← Timeline view di Autopsy
│
├── notes/
│   ├── normalisasi_timezone.md        ← Panduan normalisasi timezone antar sumber bukti
│   └── build_scenario.md              ← Catatan teknis membangun skenario di VM
│
└── evidence/
    └── README_evidence.md             ← Keterangan file .dd (tidak disertakan di repo)
```

---

## 🛠️ Tools & Environment

### Virtualisasi & OS
| Tool | Versi | Fungsi |
|---|---|---|
| Oracle VirtualBox | 7.x | Platform virtualisasi VM |
| Ubuntu Desktop | 26.04 LTS | Environment simulasi insiden (VM korban) |
| Kali Linux | Rolling | Analisis log journal & verifikasi artefak |

### Forensik
| Tool | Versi | Fungsi |
|---|---|---|
| Autopsy | 4.23 | Analisis utama image .dd |
| The Sleuth Kit (TSK) | Built-in Autopsy | Analisis filesystem ext4 & FAT32 |
| journalctl | Built-in Linux | Baca binary journal log |

### Lainnya
| Tool | Fungsi |
|---|---|
| VBoxManage | Hot-plug simulasi USB virtual (SATA hot-pluggable) |
| shred | Simulasi secure-delete (anti-forensik) |
| zip | Buat arsip terenkripsi (cache_data.tmp) |
| setfattr / getfattr | Tanam extended attributes (xattr) pada file |

---

## ⚙️ Cara Mereproduksi Skenario

> ⚠️ File image `.dd` tidak disertakan di repositori ini karena ukurannya melebihi batas GitHub (FIN-WS03_disk.dd ±28GB, Flashdisk.dd ±1GB). File tersedia di Google Drive (link di bagian bawah).

### Prasyarat
- Oracle VirtualBox terinstal di host (Windows/Linux/macOS)
- VM Ubuntu Desktop 26.04 (fresh install, minimal 30GB disk)
- RAM minimal 2GB untuk VM
- PATH VBoxManage sudah ditambahkan (Windows: `C:\Program Files\Oracle\VirtualBox`)

### Fase 0 — Persiapan Awal
```bash
# Buat user pelaku
sudo adduser rahman
sudo usermod -aG sudo rahman

# Login sebagai rahman
# Nonaktifkan sinkronisasi waktu
sudo timedatectl set-ntp false
sudo date -s "2026-05-15 10:00:00"

# Buat struktur folder & file data sensitif
mkdir -p ~/Documents/Finance_Data ~/Pictures ~/Downloads

cat > ~/Documents/Finance_Data/Data_Nasabah_Q2_2026.csv << 'EOF'
Nama,NIK,No_Rekening,Saldo_Pinjaman
Budi Santoso,3201xxxxxxxxxx01,1234567890,15000000
Siti Aminah,3201xxxxxxxxxx02,1234567891,22000000
Joko Prasetyo,3201xxxxxxxxxx03,1234567892,8700000
Rina Wulandari,3201xxxxxxxxxx04,1234567893,17500000
EOF

cat > ~/Documents/Finance_Data/Laporan_Keuangan_Internal_Q2_2026.txt << 'EOF'
LAPORAN KEUANGAN INTERNAL - Q2 2026 (RAHASIA)
PT Maju Sejahtera
Pendapatan Bunga: Rp 1.250.000.000
Beban Operasional: Rp 870.000.000
Margin Bersih: 30,4%
EOF
```

### Fase 1 — Login & Aktivitas Normal (19:00)
```bash
sudo date -s "2026-06-14 19:00:00"
cd ~/Documents/Finance_Data
echo "# review rutin" >> Laporan_Keuangan_Internal_Q2_2026.txt
```

### Fase 2 — Motif (19:20)
```bash
sudo date -s "2026-06-14 19:20:00"
cat > ~/Documents/Draft.txt << 'EOF'
Draft balasan email:
Terima kasih atas tawaran posisi Finance Manager di perusahaan Anda.
Saya tertarik dan akan mempersiapkan beberapa hal sebelum bergabung.
EOF
```

### Fase 3 — Copy ke Folder Tersembunyi (19:45)
```bash
sudo date -s "2026-06-14 19:45:00"
mkdir -p ~/.config/.sysupdate
cp -r ~/Documents/Finance_Data/* ~/.config/.sysupdate/
```

### Fase 4 — Kompres & Samarkan Ekstensi (20:00)
```bash
sudo date -s "2026-06-14 20:00:00"
sudo apt install -y zip
cd ~/.config/.sysupdate
zip -er cache_data.zip Data_Nasabah_Q2_2026.csv Laporan_Keuangan_Internal_Q2_2026.txt
# Masukkan password: rahasia123
mv cache_data.zip cache_data.tmp
```

### Fase 5 — Pasang Media Eksternal & Copy (20:15)
```bash
sudo date -s "2026-06-14 20:15:00"
```

Dari **host Windows** (VM harus running):
```cmd
"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" storageattach "Insider_Threat" ^
  --storagectl "SATA" --port 1 --device 0 ^
  --type hdd --medium "PATH\USB_Flashdisk.vdi"
```

Di dalam guest:
```bash
dmesg | tail -20
sudo mkfs.vfat -n "FLASHDISK" /dev/sdb
sudo mkdir -p /media/arahman/FLASHDISK
sudo mount /dev/sdb /media/arahman/FLASHDISK
sudo cp ~/.config/.sysupdate/cache_data.tmp /media/arahman/FLASHDISK/
sync
```

### Fase 6 — Lepas Media Eksternal (20:30)
```bash
sudo date -s "2026-06-14 20:30:00"
sudo umount /media/arahman/FLASHDISK
```

Dari **host Windows**:
```cmd
"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" storageattach "Insider_Threat" ^
  --storagectl "SATA" --port 1 --device 0 --type hdd --medium none
```

### Fase 7 — Anti-Forensik (20:45)
```bash
sudo date -s "2026-06-14 20:45:00"

# Secure delete folder tersembunyi
shred -vfz -n 3 ~/.config/.sysupdate/*
rm -rf ~/.config/.sysupdate

# Timestomping
touch -t 202601010800 ~/Documents/Finance_Data/Data_Nasabah_Q2_2026.csv

# Hapus bash history
history -c
cat /dev/null > ~/.bash_history
unset HISTFILE
```

### Fase 8 — Bersihkan Browser (21:00)
```
Buka Firefox → Settings → Privacy & Security → Clear Data → Everything → Clear
```

### Fase 9 — Surat Resign (21:15)
```bash
sudo date -s "2026-06-14 21:15:00"
cat > ~/Documents/Surat_Pengunduran_Diri.txt << 'EOF'
Kepada HRD PT Maju Sejahtera,
Dengan ini saya mengajukan pengunduran diri efektif segera.
Hormat saya,
Andi Rahman
EOF
```

### Fase 10 — Finalisasi
```bash
sudo date -s "2026-06-15 10:00:00"
sudo timedatectl set-ntp true
sudo shutdown now
```

### Imaging Disk ke .dd (dari host Windows)
```cmd
rem Disk utama workstation
"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" clonemedium ^
  "PATH\Insider_Threat.vdi" "PATH\FIN-WS03_disk.dd" --format RAW

rem Flashdisk virtual
"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" clonemedium ^
  "PATH\USB_Flashdisk.vdi" "PATH\Flashdisk.dd" --format RAW
```

---

## 🔬 Panduan Analisis di Autopsy

### Setup Case
1. Buka Autopsy → **New Case**
2. Isi nama case: `Insider_Threat_FINWS03`
3. **Add Data Source** → Disk Image or VM File → pilih `FIN-WS03_disk.dd`
4. Aktifkan Ingest Modules:
   - ✅ Recent Activity
   - ✅ File Type Identification
   - ✅ Extension Mismatch Detector
   - ✅ Embedded File Extraction
   - ✅ Keyword Search (tambahkan keyword: `Finance`, `Nasabah`, `sysupdate`, `Rahasia`, `cache_data`, `Pengunduran`)
5. Setelah selesai, **Add Data Source** lagi → pilih `Flashdisk.dd`

### Lokasi Artefak Kunci

| Artefak | Lokasi di Autopsy |
|---|---|
| Data sensitif asli | `vol5/home/rahman/Documents/Finance_Data/` |
| Draft tawaran kerja (motif) | `vol5/home/rahman/Documents/Draft.txt` |
| Surat resign | `vol5/home/rahman/Documents/Surat_Pengunduran_Diri.txt` |
| Bash history (kosong) | `vol5/home/rahman/.bash_history` |
| Log USB/kernel | `vol5/var/log/journal/<machine-id>/` |
| Auth log (perintah sudo) | `vol5/var/log/auth.log` |
| File eksfiltrasi di flashdisk | `Flashdisk.dd/vol1/cache_data.tmp` |

---

## 📊 Ringkasan Temuan

### Tujuan 1: Eksfiltrasi ke Media Eksternal ✅ TERBUKTI
- **20:15:34 WIB** — Device "Flash Disk 8GB" terdeteksi (`/dev/sdb`) via `journalctl -k`
- **20:16:14 WIB** — Filesystem FAT32 "FLASHDISK" dibuat
- **20:16:52–53 WIB** — File `cache_data.tmp` ditulis ke flashdisk
- **20:30:45–56 WIB** — Device dilepas paksa (tanpa eject)
- File `cache_data.tmp` terdeteksi sebagai **ZIP terenkripsi** (Extension Mismatch + Encryption Detected)
- Isi arsip: `Data_Nasabah_Q2_2026.csv` + `Laporan_Keuangan_Internal_Q2_2026.txt`

### Tujuan 2: Aktivitas di Luar Jam Kerja ✅ TERBUKTI
- Seluruh aktivitas berlangsung **Minggu malam, 14 Juni 2026 (19:20–21:15 WIB)**
- Di luar hari kerja (Senin–Jumat) dan jam kerja normal (08:00–17:00)

### Tujuan 3: Anti-Forensik ✅ DITEMUKAN
| Teknik | Bukti |
|---|---|
| Bash history wiping | `.bash_history` size=0, mtime ~20:45 WIB |
| Timestomping | `Data_Nasabah_Q2_2026.csv` — mtime (1 Jan 2026) < crtime (15 Mei 2026) |
| Secure delete | Folder `.sysupdate` tidak dapat direcovery |

### Tujuan 4: Recovery Data ✅ PARSIAL
- Folder `.sysupdate` **tidak dapat direcovery** (secure-deleted)
- Namun **`auth.log` merekam seluruh perintah** yang dijalankan dari dalam folder tersebut, termasuk perintah `cp cache_data.tmp` ke flashdisk

### Tujuan 5: Timeline Kronologis

| Waktu (WIB) | Peristiwa | Sumber Bukti |
|---|---|---|
| 15 Mei 2026, 10:00 | File `Data_Nasabah_Q2_2026.csv` dibuat (baseline) | File Metadata (crtime) |
| 14 Jun, 19:20:07 | `Draft.txt` dibuat (motif — tawaran kerja kompetitor) | File Metadata (mtime) |
| 14 Jun, ~19:45 | Folder `.sysupdate` dibuat, data Finance disalin & dikompres | auth.log |
| 14 Jun, 20:15:34 | "Flash Disk 8GB" terpasang sebagai `/dev/sdb` | journalctl -k |
| 14 Jun, 20:16:14 | Flashdisk diformat FAT32 (`mkfs.vfat -n FLASHDISK`) | auth.log + Flashdisk.dd |
| 14 Jun, 20:16:52–53 | `cache_data.tmp` disalin ke flashdisk | auth.log + Flashdisk.dd |
| 14 Jun, 20:30:30 | Flashdisk di-unmount | auth.log |
| 14 Jun, 20:30:45–56 | Media dilepas paksa | journalctl -k |
| 14 Jun, 20:45:23–35 | Anti-forensik: shred, bash_history kosong, timestomping | File Metadata + bash_history |
| 14 Jun, ~21:00 | Riwayat browser dibersihkan | Browser artifacts minim |
| 14 Jun, 21:15:05 | `Surat_Pengunduran_Diri.txt` dibuat | File Metadata (mtime) |
| 15 Jun, 08:00 | Resign mendadak diajukan (notice 1 hari) | Konteks kasus |
| 15 Jun, 10:00 | Workstation & flashdisk disita | Konteks kasus |

### Tujuan 6: Kesimpulan ✅ EKSFILTRASI TERBUKTI
Dugaan eksfiltrasi data sensitif perusahaan oleh pengguna `rahman` **TERBUKTI** berdasarkan bukti digital dari empat sumber independen: bukti eksfiltrasi (journalctl + Flashdisk.dd + auth.log), pola aktivitas mencurigakan di luar jam kerja, tiga indikasi anti-forensik, dan artefak motif (Draft.txt + Surat Resign).

### Tujuan 7: Membuka cache_data.tmp
1. Extract `cache_data.tmp` dari Autopsy (klik kanan → Extract File)
2. Rename ke `cache_data.zip`
3. Buka dengan 7-Zip/WinRAR → diminta password
4. Periksa `Catatan_Misterius.txt` yang diserahkan bersama barang bukti
5. Gunakan `dir /r Catatan_Misterius.txt` di CMD → temukan Alternate Data Stream `:kunci`
6. Baca isi stream: `more < "Catatan_Misterius.txt:kunci"` → **`rahasia123`**
7. Gunakan password tersebut untuk membuka arsip → isi terkonfirmasi

---

## 🗝️ Normalisasi Timezone

Perbedaan timezone adalah tantangan kritis dalam analisis ini:

| Sumber | Format Penyimpanan | Konversi ke WIB Skenario |
|---|---|---|
| **ext4 filesystem** (`FIN-WS03_disk.dd`) | UTC | Autopsy display (WIB) **−7 jam** |
| **FAT32** (`Flashdisk.dd`) | Local time (as-is) | Langsung digunakan tanpa konversi |
| **auth.log & journalctl** | UTC+0 | **+7 jam** untuk mendapat WIB |

---

## 🔧 Setup USB Virtual (VirtualBox Hot-Plug)

Karena tidak menggunakan flashdisk fisik, USB disimulasikan menggunakan **SATA hot-plug** di VirtualBox:

```cmd
rem 1. Buat virtual disk 1GB
VBoxManage createmedium disk --filename "PATH\USB_Flashdisk.vdi" --size 1024 --format VDI

rem 2. Attach ke port 1 dengan hotpluggable
VBoxManage storageattach "Insider_Threat" --storagectl "SATA" ^
  --port 1 --device 0 --type hdd ^
  --medium "PATH\USB_Flashdisk.vdi" --hotpluggable on

rem 3. Set model & serial agar terlihat seperti flashdisk
VBoxManage setextradata "Insider_Threat" ^
  "VBoxInternal/Devices/ahci/0/Config/Port1/ModelNumber" "Flash Disk 8GB"
VBoxManage setextradata "Insider_Threat" ^
  "VBoxInternal/Devices/ahci/0/Config/Port1/SerialNumber" "AA00112233445566"

rem 4. Kosongkan port (siap untuk hot-plug saat VM running)
VBoxManage storageattach "Insider_Threat" --storagectl "SATA" ^
  --port 1 --device 0 --type hdd --medium none
```

Saat VM running, attach/detach dengan command yang sama menggunakan `--medium "PATH\USB_Flashdisk.vdi"` atau `--medium none`.

---

## 📂 Barang Bukti

| File | Ukuran | Keterangan |
|---|---|---|
| `FIN-WS03_disk.dd` | ~28 GB | Image forensik workstation FIN-WS03 (ext4, Ubuntu 26.04) |
| `Flashdisk.dd` | ~1 GB | Image forensik media penyimpanan eksternal (FAT32) |

> 📥 **Download**: File `.dd` tersedia di [Google Drive](https://drive.google.com/drive/folders/1ekd4_4lBpv01b7sBcfUerY0vs8_WX0Pw?usp=sharing)
> 
> ⚠️ File tidak disertakan di repositori GitHub karena melebihi batas ukuran (100MB/file).

---

## ⚠️ Disclaimer

> Seluruh nama, data, organisasi, dan identitas dalam project ini bersifat **fiktif** dan dibuat semata-mata untuk keperluan akademik dalam mata kuliah Forensik Digital & Investigasi. Tidak ada data pribadi nyata yang digunakan. Teknik-teknik yang didokumentasikan di sini hanya boleh dipraktikkan dalam lingkungan lab yang terkontrol dan dengan izin yang sesuai.

---

## 👤 Author

**Name**: Forensik Digital & Investigasi    
**Platform**: Ubuntu Desktop 26.04 + Autopsy 4.23 + Oracle VirtualBox

---

*"Jejak tidak pernah berbohong. Hanya manusia yang mencoba."*
