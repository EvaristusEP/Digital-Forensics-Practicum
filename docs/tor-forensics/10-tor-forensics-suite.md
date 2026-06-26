# Praktikum 10 - Tor Forensics Suite

## Pendahuluan

Pada praktikum ini dilakukan pengenalan terhadap **Tor Forensics Suite**, sebuah tools yang dikembangkan untuk membantu investigator melakukan analisis artefak digital dari Tor Browser. Aplikasi ini mendukung proses identifikasi lokasi instalasi Tor, analisis aktivitas pengguna, pemeriksaan integritas file, hingga pembuatan laporan hasil investigasi.

Repository yang digunakan pada praktikum ini:

https://github.com/wbth/tor_forensics_suite

---

## Tujuan Praktikum

Setelah menyelesaikan praktikum ini, mahasiswa diharapkan mampu:

- Memahami struktur project Tor Forensics Suite.
- Mengenali fungsi setiap komponen aplikasi.
- Menjalankan aplikasi melalui Command Line Interface (CLI).
- Memahami proses analisis artefak Tor Browser.
- Memahami proses pembuatan laporan forensik secara otomatis.

---

## Struktur Project

Repository memiliki struktur sebagai berikut.

```text
tor_forensics_suite/
├── core/
│   ├── __init__.py
│   ├── detector.py
│   └── analyzer.py
├── app.py
├── cli.py
└── requirements.txt
```

Komponen utama:

| File | Fungsi |
|------|--------|
| detector.py | Mendeteksi lokasi instalasi Tor Browser |
| analyzer.py | Melakukan analisis artefak digital |
| app.py | Interface berbasis Streamlit |
| cli.py | Interface Command Line |
| requirements.txt | Daftar library Python |

![](screenshots/01-repository-structure.png)

---

## Persiapan Environment

Pastikan Python telah terpasang pada sistem.

Cek versi Python:

```bash
python --version
```

atau

```bash
python3 --version
```

![](screenshots/02-python-version.png)

---

## Install Dependency

Install seluruh dependency menggunakan pip.

```bash
pip install -r requirements.txt
```

Tunggu hingga seluruh package selesai diinstall.

![](screenshots/03-install-requirements.png)

---

## Menjalankan CLI

Aplikasi dapat dijalankan melalui terminal menggunakan:

```bash
python cli.py
```

atau

```bash
python3 cli.py
```

![](screenshots/04-run-cli.png)

---

## Analisis Otomatis Lokasi Tor Browser

Program akan mencoba mendeteksi lokasi instalasi Tor Browser secara otomatis menggunakan modul:

```
core/detector.py
```

Modul ini melakukan pencarian berdasarkan sistem operasi yang digunakan, seperti:

- Windows
- Linux
- macOS

Apabila folder profil berhasil ditemukan, proses analisis akan dilanjutkan secara otomatis.

![](screenshots/05-detector.png)

---

## Analisis Sistem

Selanjutnya aplikasi melakukan pemeriksaan terhadap status Tor pada sistem.

Beberapa informasi yang dianalisis antara lain:

- Status Tor Daemon
- Proxychains
- Informasi jaringan
- Konfigurasi Tor

![](screenshots/06-system-analysis.png)

---

## Verifikasi Integritas File

Program menghitung nilai hash terhadap file penting seperti:

- places.sqlite
- prefs.js

Algoritma yang digunakan:

- SHA-256

Contoh proses:

```text
places.sqlite
prefs.js
```

Hash digunakan untuk menjaga integritas evidence selama investigasi digital.

![](screenshots/07-hash-analysis.png)

---

## Audit Konfigurasi Keamanan

Aplikasi melakukan pemeriksaan terhadap konfigurasi keamanan Tor Browser, seperti:

- Security Level
- Status JavaScript
- Konfigurasi privasi
- Penggunaan Bridge

Informasi ini berguna untuk mengetahui tingkat keamanan konfigurasi pengguna.

![](screenshots/08-opsec-analysis.png)

---

## Analisis Riwayat Browser

Program membaca database SQLite milik Tor Browser dan menampilkan riwayat aktivitas pengguna.

Artefak yang diperoleh meliputi:

- URL
- Jumlah kunjungan
- Waktu akses

Data tersebut dapat digunakan dalam proses rekonstruksi aktivitas pengguna.

![](screenshots/09-history-analysis.png)

---

## Analisis Temporal

Selain membaca history, aplikasi juga melakukan analisis pola aktivitas pengguna.

Beberapa informasi yang diperoleh antara lain:

- Jam paling aktif
- Pola penggunaan
- Aktivitas harian

Informasi ini dapat membantu investigator membangun timeline kejadian.

![](screenshots/10-temporal-analysis.png)

---

## Recovery Data

Tor Forensics Suite melakukan proses carving terhadap file WAL (Write Ahead Log) untuk menemukan data yang sebelumnya telah dihapus.

Artefak yang berhasil dipulihkan dapat berupa:

- URL
- Aktivitas browsing
- Data sementara

![](screenshots/11-wal-carving.png)

---

## Pembuatan Laporan

Hasil investigasi dapat diekspor secara otomatis menjadi:

- JSON
- HTML

Laporan berisi:

- Metadata kasus
- Nilai hash
- Hasil analisis
- Riwayat aktivitas
- Data recovery

![](screenshots/12-report-export.png)

---

## Hasil Praktikum

Melalui praktikum ini berhasil dipahami bahwa Tor Forensics Suite mampu:

- Mendeteksi lokasi instalasi Tor Browser.
- Melakukan analisis artefak digital.
- Menghitung hash evidence.
- Menganalisis konfigurasi keamanan.
- Mengekstraksi riwayat aktivitas pengguna.
- Melakukan recovery data dari WAL.
- Menghasilkan laporan investigasi secara otomatis.

---

## Kesimpulan

Tor Forensics Suite merupakan tools yang membantu investigator dalam proses analisis artefak Tor Browser secara lebih cepat dan terstruktur. Integrasi berbagai fitur seperti deteksi lokasi, analisis history, pemeriksaan hash, recovery data, hingga pembuatan laporan otomatis menjadikan aplikasi ini sebagai salah satu contoh implementasi digital forensics berbasis Python.
