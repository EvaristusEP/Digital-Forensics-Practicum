# Praktikum 3 - Recover Data from a Linux Memory Dump

## Pendahuluan

Memory dump merupakan salinan isi RAM pada suatu waktu tertentu yang digunakan sebagai salah satu sumber bukti dalam digital forensics. Karena RAM bersifat volatile, data yang tersimpan akan hilang ketika sistem dimatikan. Oleh sebab itu, proses akuisisi dan analisis memory dump harus dilakukan sesegera mungkin agar artefak penting masih dapat dipulihkan.

Pada praktikum ini dilakukan proses recovery data dari sebuah file memory dump Linux menggunakan PhotoRec yang merupakan bagian dari paket TestDisk. Hasil recovery nantinya dapat digunakan sebagai bahan investigasi lanjutan untuk menemukan dokumen, gambar, browser cache, maupun artefak digital lainnya.

---

## Skenario

Dalam sebuah operasi yang dipimpin oleh FBI, tujuh orang berhasil diamankan dalam kasus penyelundupan narkoba. Polisi menyita sebuah komputer yang diduga digunakan sebagai media komunikasi para pelaku.

Investigator menduga komputer tersebut masih menyimpan berbagai artefak penting seperti riwayat website, percakapan, aktivitas media sosial, hingga credential yang tersimpan di dalam RAM. Oleh karena itu dilakukan proses recovery terhadap memory dump Linux untuk memperoleh bukti digital yang masih dapat dipulihkan.

---

## Tujuan Praktikum

Pada praktikum ini dilakukan proses recovery data dari sebuah memory dump Linux. Setelah menyelesaikan praktikum ini diharapkan mampu:

- Menginstal tool PhotoRec/TestDisk.
- Melakukan recovery file dari memory dump.
- Memahami konsep file carving.
- Menyimpan hasil recovery untuk analisis lanjutan.
- Mengidentifikasi artefak digital yang berhasil dipulihkan.

---

## Environment

| Komponen | Keterangan |
|----------|------------|
| Sistem Operasi | Ubuntu Linux |
| Memory Dump | Linux.mem |
| Tools | TestDisk, PhotoRec |
| Output | Folder Recovery |

---

# 1. Mendapatkan Hak Akses Root

## Tujuan

Mengaktifkan hak akses administrator agar seluruh proses recovery dapat dijalankan tanpa kendala permission.

### Command

```bash
sudo su
```

Password

```text
toor
```

![Root Access](../../screenshots/praktikum3/01-root-access.png)

### Analisis

Hak akses root diperlukan agar investigator dapat mengakses seluruh file sistem dan menjalankan proses recovery terhadap memory dump.

---

# 2. Menginstal TestDisk / PhotoRec

## Tujuan

Menginstal tool recovery data yang digunakan selama praktikum.

### Command

```bash
apt install testdisk
```

![Install TestDisk](../../screenshots/praktikum3/02-install-testdisk.png)

### Analisis

PhotoRec merupakan bagian dari paket TestDisk yang digunakan untuk melakukan recovery file berdasarkan signature data tanpa bergantung pada struktur filesystem.

---

# 3. Menjalankan PhotoRec

## Tujuan

Memulai proses recovery terhadap memory dump Linux.

### Command

```bash
photorec Linux.mem
```

![Run PhotoRec](../../screenshots/praktikum3/03-run-photorec.png)

### Analisis

PhotoRec akan membaca file memory dump kemudian melakukan proses file carving untuk menemukan artefak yang masih dapat dipulihkan.

---

# 4. Memilih Menu Proceed

## Tujuan

Melanjutkan proses analisis terhadap file memory dump.

### Langkah

- Pilih **Proceed**
- Tekan **Enter**

![Proceed](../../screenshots/praktikum3/04-proceed.png)

### Analisis

Tahap ini mengonfirmasi bahwa file memory dump telah dikenali dan siap diproses oleh PhotoRec.

---

# 5. Analisis Partition

## Tujuan

Mengidentifikasi struktur media yang sedang dianalisis.

![Unknown Partition](../../screenshots/praktikum3/05-partition.png)

### Analisis

Karena file yang dianalisis merupakan memory dump, PhotoRec akan menampilkan tipe partisi **Unknown**. Hal tersebut merupakan kondisi normal karena RAM tidak memiliki filesystem tradisional seperti hard disk.

---

# 6. Memilih Search

## Tujuan

Memulai proses pencarian file yang dapat dipulihkan.

![Search](../../screenshots/praktikum3/06-search.png)

### Analisis

Menu **Search** digunakan untuk memulai proses scanning terhadap seluruh isi memory dump.

---

# 7. Memilih Jenis Filesystem

## Tujuan

Menentukan tipe filesystem yang digunakan selama proses recovery.

### Pilihan

```
Other
```

![Filesystem](../../screenshots/praktikum3/07-filesystem.png)

### Analisis

Memory dump tidak memiliki filesystem normal sehingga opsi **Other** menjadi pilihan yang sesuai.

---

# 8. Menentukan Folder Recovery

## Tujuan

Menentukan lokasi penyimpanan hasil recovery.

### Lokasi

```
Documents
```

Tekan tombol:

```
C
```

![Recovery Folder](../../screenshots/praktikum3/08-recovery-folder.png)

### Analisis

Seluruh file hasil recovery akan disimpan pada folder Documents dalam direktori `recup_dir.*`.

---

# 9. Proses Recovery

## Tujuan

Melakukan proses file carving terhadap memory dump.

![Recovery Process](../../screenshots/praktikum3/09-recovery-process.png)

### Analisis

PhotoRec akan membaca signature setiap blok data kemudian mencoba memulihkan file yang masih memiliki struktur header yang valid.

---

# 10. Hasil Recovery

## Tujuan

Melihat file yang berhasil dipulihkan.

![Recovery Result](../../screenshots/praktikum3/10-recovery-result.png)

### Analisis

PhotoRec secara otomatis membuat folder seperti:

- recup_dir.1
- recup_dir.2
- recup_dir.3

Setiap folder berisi file hasil recovery yang selanjutnya dapat dianalisis menggunakan teknik digital forensics lainnya.

---

# Artefak Digital yang Berhasil Dipulihkan

Selama praktikum berlangsung berhasil diperoleh berbagai artefak digital, antara lain:

- Dokumen.
- File gambar.
- Temporary file.
- Browser cache.
- Session file.
- Text file.
- Metadata file.
- File hasil carving.

Artefak tersebut dapat digunakan untuk proses keyword searching, timeline analysis, metadata analysis, browser artifact analysis, maupun chat reconstruction.

---

# Kesimpulan

Pada praktikum ini dilakukan proses recovery data dari sebuah Linux memory dump menggunakan PhotoRec. Investigator berhasil melakukan instalasi tool recovery, menjalankan proses file carving, menentukan lokasi penyimpanan hasil recovery, serta memperoleh berbagai artefak digital dari RAM dump.

Teknik file carving memungkinkan proses recovery dilakukan tanpa bergantung pada filesystem sehingga sangat efektif digunakan pada memory dump, raw image, maupun media penyimpanan yang mengalami kerusakan. Hasil recovery selanjutnya dapat dimanfaatkan sebagai barang bukti digital maupun bahan analisis lanjutan dalam proses investigasi digital forensics.
