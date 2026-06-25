# Praktikum 4 - Data Recovery & Artefact Analysis pada Disk Image `hiddenG.img`

## Pendahuluan

Data recovery merupakan salah satu tahapan penting dalam digital forensics yang bertujuan untuk memulihkan kembali file atau artefak digital yang telah dihapus, disembunyikan, maupun mengalami kerusakan. Pada praktikum ini dilakukan proses data carving terhadap sebuah logical disk image bernama `hiddenG.img` menggunakan beberapa tools forensik yang tersedia pada Kali Linux.

Setiap tools memiliki metode analisis yang berbeda sehingga hasil recovery yang diperoleh juga dapat berbeda. Oleh karena itu, investigator perlu membandingkan hasil dari masing-masing tools agar memperoleh artefak digital yang lebih lengkap.

---

## Tujuan Praktikum

Setelah menyelesaikan praktikum ini diharapkan mampu:

- Melakukan data carving menggunakan beberapa tools digital forensics.
- Mengidentifikasi artefak digital yang berhasil dipulihkan.
- Membandingkan hasil recovery dari berbagai tools.
- Menganalisis file hasil recovery sebagai barang bukti digital.
- Menyusun dokumentasi investigasi sederhana berdasarkan hasil praktikum.

---

## Dataset

Image yang digunakan pada praktikum ini adalah:

```text
hiddenG.img
```

Dataset tersebut berisi berbagai artefak digital seperti:

- File yang telah dihapus
- File tersembunyi
- Dokumen
- File gambar
- URL
- Email
- Embedded file

---

## Environment

| Komponen | Keterangan |
|----------|------------|
| Sistem Operasi | Kali Linux |
| Dataset | hiddenG.img |
| Tools | Foremost, PhotoRec, Scalpel, Bulk Extractor, Binwalk |

---

# 1. Data Carving Menggunakan Foremost

## Tujuan

Melakukan proses file carving berdasarkan file header dan footer menggunakan Foremost.

### Command

```bash
sudo foremost hiddenG.img -o foremost_out
```

```bash
cd foremost_out
```

```bash
ls -all
```

![Foremost](../../screenshots/praktikum4/01-foremost.png)

### Analisis

Foremost melakukan proses recovery berdasarkan signature file sehingga mampu menemukan berbagai jenis file yang telah terhapus. Hasil recovery disimpan pada folder `foremost_out`.

---

# 2. Analisis File audit.txt

## Tujuan

Melihat laporan hasil recovery yang dihasilkan oleh Foremost.

### Command

```bash
pico audit.txt
```

![Audit Report](../../screenshots/praktikum4/02-audit-report.png)

### Analisis

File `audit.txt` berisi informasi mengenai proses recovery seperti jumlah file yang ditemukan, offset sektor, tipe file, serta ringkasan proses carving. File ini membantu investigator memahami proses recovery yang telah dilakukan.

---

# 3. Memeriksa File Hasil Recovery

## Tujuan

Mengidentifikasi artefak digital yang berhasil dipulihkan.

### Command

```bash
ls -all jpg
```

```bash
ls -all pdf
```

```bash
ls -all zip
```

![Recovered Files](../../screenshots/praktikum4/03-recovered-files.png)

### Analisis

Folder hasil carving berisi berbagai file yang berhasil dipulihkan berdasarkan kategori. Investigator perlu mengidentifikasi minimal tiga artefak yang berpotensi menjadi barang bukti digital.

---

# 4. Data Recovery Menggunakan PhotoRec

## Tujuan

Melakukan proses deep carving menggunakan PhotoRec.

### Command

```bash
sudo photorec hiddenG.img
```

![PhotoRec](../../screenshots/praktikum4/04-photorec.png)

### Analisis

PhotoRec bekerja tanpa bergantung pada filesystem sehingga mampu melakukan recovery terhadap cluster kosong maupun media penyimpanan yang telah rusak. Hasil recovery biasanya lebih banyak dibandingkan Foremost.

---

# 5. Data Recovery Menggunakan Scalpel

## Tujuan

Melakukan data carving menggunakan Scalpel.

### Konfigurasi

```bash
sudo nano /etc/scalpel/scalpel.conf
```

Aktifkan tipe file:

```text
jpg
pdf
zip
png
```

### Menjalankan Scalpel

```bash
sudo scalpel hiddenG.img -o scalpel_out
```

![Scalpel](../../screenshots/praktikum4/05-scalpel.png)

### Analisis

Scalpel merupakan pengembangan dari Foremost dengan konfigurasi yang lebih fleksibel serta performa yang lebih baik untuk proses recovery file berdasarkan signature.

---

# 6. Analisis Artefak Menggunakan Bulk Extractor

## Tujuan

Mengekstrak artefak penting tanpa melakukan recovery file secara penuh.

### Command

```bash
bulk_extractor -o bulk_out hiddenG.img
```

![Bulk Extractor](../../screenshots/praktikum4/06-bulk-extractor.png)

### Analisis

Bulk Extractor mengekstrak berbagai artefak digital seperti alamat email, URL, nomor telepon, kata sandi, hingga wordlist. Artefak tersebut sangat membantu dalam proses investigasi tanpa harus membuka seluruh file hasil recovery.

---

# 7. Analisis Output Bulk Extractor

## Tujuan

Melakukan pemeriksaan terhadap artefak hasil ekstraksi.

### File yang Dianalisis

- email.txt
- url.txt
- telephone.txt
- wordlist.txt
- jpeg_carved/

![Bulk Output](../../screenshots/praktikum4/07-bulk-output.png)

### Analisis

Artefak yang ditemukan dapat memberikan informasi mengenai komunikasi digital, website yang pernah diakses, maupun informasi lain yang berkaitan dengan aktivitas pengguna.

---

# 8. Analisis Embedded File Menggunakan Binwalk

## Tujuan

Mendeteksi file yang disisipkan di dalam image.

### Command

```bash
binwalk hiddenG.img
```

![Binwalk Scan](../../screenshots/praktikum4/08-binwalk-scan.png)

### Analisis

Binwalk melakukan scanning terhadap signature binary untuk menemukan file yang tersembunyi maupun embedded di dalam image.

---

# 9. Ekstraksi Embedded File

## Tujuan

Melakukan ekstraksi otomatis terhadap file yang ditemukan oleh Binwalk.

### Command

```bash
binwalk -e hiddenG.img
```

![Binwalk Extract](../../screenshots/praktikum4/09-binwalk-extract.png)

### Analisis

Seluruh file hasil ekstraksi akan disimpan pada folder `_hiddenG.img.extracted`. Investigator dapat melakukan analisis lebih lanjut terhadap file tersebut untuk menemukan kemungkinan bukti digital yang disembunyikan.

---

# Perbandingan Hasil Tools

| Tools | Fungsi Utama | Hasil |
|---------|--------------|--------|
| Foremost | File carving berbasis signature | Recovery file dasar |
| PhotoRec | Deep file carving | Jumlah file recovery lebih banyak |
| Scalpel | Signature carving modern | Recovery lebih cepat dan fleksibel |
| Bulk Extractor | Artefact extraction | Email, URL, wordlist, telephone |
| Binwalk | Embedded file detection | File tersembunyi dalam image |

---

# Artefak Digital yang Berhasil Ditemukan

Selama praktikum berlangsung berhasil diperoleh berbagai artefak digital, antara lain:

- File gambar.
- Dokumen.
- File ZIP.
- Alamat email.
- URL.
- Nomor telepon.
- Wordlist.
- Embedded file.
- Metadata hasil carving.

Artefak tersebut dapat digunakan sebagai barang bukti digital maupun sebagai dasar analisis lanjutan dalam proses investigasi.

---

# Kesimpulan

Pada praktikum ini dilakukan proses data recovery terhadap image forensik `hiddenG.img` menggunakan lima tools yang memiliki pendekatan berbeda. Hasil praktikum menunjukkan bahwa setiap tools memiliki kelebihan masing-masing, mulai dari recovery file, ekstraksi artefak, hingga pendeteksian file tersembunyi.

Dengan membandingkan hasil dari Foremost, PhotoRec, Scalpel, Bulk Extractor, dan Binwalk, investigator dapat memperoleh bukti digital yang lebih lengkap sehingga proses investigasi menjadi lebih komprehensif.
