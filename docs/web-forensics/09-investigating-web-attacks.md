# Praktikum 9 - Investigating Web Attacks

## Pendahuluan

Serangan terhadap aplikasi web merupakan salah satu insiden keamanan yang paling sering terjadi pada organisasi modern. Dalam proses **Digital Forensics & Investigation (DFI)**, investigator bertugas mengumpulkan, memverifikasi, dan menganalisis berbagai artefak digital untuk mengetahui bagaimana serangan terjadi, aktivitas yang dilakukan penyerang, serta dampak yang ditimbulkan terhadap sistem.

Pada praktikum ini dilakukan investigasi terhadap sebuah insiden keamanan pada portal akademik menggunakan berbagai sumber evidence seperti web server log, database log, application log, network capture, serta image database sebelum dan sesudah insiden.

---

## Tujuan Praktikum

Setelah menyelesaikan praktikum ini diharapkan mampu:

- Memverifikasi integritas evidence digital.
- Menganalisis web server log.
- Mengidentifikasi indikator kompromi (IoC).
- Menganalisis aktivitas database.
- Mengidentifikasi SQL Injection.
- Mengidentifikasi akses tanpa otorisasi.
- Melakukan analisis persistence.
- Membangun timeline insiden.
- Menyusun laporan investigasi digital.

---

## Skenario Kasus

Pada **10 Juni 2026**, Tim IT Universitas Nusantara menemukan aktivitas mencurigakan pada portal akademik.

Beberapa indikasi yang ditemukan antara lain:

- Perubahan nilai mahasiswa tanpa otorisasi.
- Muncul akun administrator baru.
- Ditemukan file mencurigakan pada server.
- Dugaan akses tidak sah terhadap data mahasiswa.

Investigator bertugas menentukan:

- Bagaimana attacker memperoleh akses awal.
- Aktivitas setelah berhasil masuk ke sistem.
- Data yang berhasil diakses maupun dimodifikasi.
- Mekanisme persistence.
- Dampak insiden terhadap sistem.

---

## Evidence

Direktori evidence:

```text
~/case
```

Struktur evidence:

```text
case/
├── access.log
├── error.log
├── general.log
├── app.log
├── before.sql
├── after.sql
├── attack.pcap
├── hashes.sha256
└── uploads/
```

---

# Task 1 - Evidence Validation

## Tujuan

Memastikan integritas seluruh evidence sebelum dilakukan analisis.

### Command

```bash
cd ~/case
```

```bash
sha256sum *
```

Bandingkan dengan:

```bash
cat hashes.sha256
```

![Evidence Validation](../../screenshots/praktikum9/01-evidence-validation.png)

### Analisis

Verifikasi hash memastikan evidence tidak mengalami perubahan selama proses penyimpanan maupun investigasi sehingga keaslian bukti tetap terjaga.

---

# Task 2 - Source Identification

## Tujuan

Mengidentifikasi alamat IP yang paling aktif selama insiden.

### Command

```bash
awk '{print $1}' access.log | sort | uniq -c | sort -nr
```

Melihat aktivitas IP tertentu:

```bash
grep "192.168" access.log
```

![Source Identification](../../screenshots/praktikum9/02-source-identification.png)

### Analisis

Analisis alamat IP membantu investigator menentukan host yang paling aktif serta mengidentifikasi aktivitas yang berpotensi mencurigakan.

---

# Task 3 - Initial Access Analysis

## Tujuan

Menentukan bagaimana attacker memperoleh akses awal.

### Command

Cari login:

```bash
grep "login.php" access.log
```

Hitung percobaan login:

```bash
grep "POST /portal/login.php" access.log | wc -l
```

![Initial Access](../../screenshots/praktikum9/03-initial-access.png)

### Analisis

Aktivitas login dapat menunjukkan indikasi brute-force attack maupun keberhasilan autentikasi menggunakan akun tertentu.

---

# Task 4 - SQL Injection Investigation

## Tujuan

Mengidentifikasi indikasi SQL Injection.

### Command

Cari payload mencurigakan:

```bash
grep "'" access.log
```

Analisis query database:

```bash
grep "SELECT" general.log
```

![SQL Injection](../../screenshots/praktikum9/04-sql-injection.png)

### Analisis

Payload SQL Injection biasanya terlihat pada parameter HTTP maupun query database yang tidak normal sehingga dapat digunakan sebagai indikator kompromi.

---

# Task 5 - Unauthorized Data Access Analysis

## Tujuan

Mengidentifikasi akses data tanpa otorisasi.

### Command

```bash
grep "nilai.php" access.log
```

Perhatikan parameter:

```text
id=1
id=2
id=3
id=4
id=5
```

![Unauthorized Access](../../screenshots/praktikum9/05-unauthorized-access.png)

### Analisis

Pola akses terhadap beberapa ID secara berurutan dapat menjadi indikasi **Insecure Direct Object Reference (IDOR)**.

---

# Task 6 - Persistence Analysis

## Tujuan

Mengidentifikasi mekanisme persistence pada server.

### Command

```bash
cd uploads
```

```bash
ls -lah
```

```bash
file *
```

![Persistence Analysis](../../screenshots/praktikum9/06-persistence-analysis.png)

### Analisis

Direktori upload perlu diperiksa untuk menemukan file yang berpotensi menjadi web shell maupun mekanisme persistence lainnya.

---

# Task 7 - Database Impact Assessment

## Tujuan

Menganalisis perubahan database akibat insiden.

### Command

```bash
diff before.sql after.sql
```

![Database Comparison](../../screenshots/praktikum9/07-database-comparison.png)

### Analisis

Perbandingan database sebelum dan sesudah insiden membantu investigator mengetahui perubahan data, akun baru, maupun modifikasi informasi penting.

---

# Task 8 - Application Log Analysis

## Tujuan

Menganalisis aktivitas aplikasi selama insiden.

### Command

```bash
less app.log
```

Cari aktivitas login:

```bash
grep -i "login" app.log
```

Cari perubahan nilai:

```bash
grep -i "grade" app.log
```

Cari aktivitas administrator:

```bash
grep -i "admin" app.log
```

![Application Log](../../screenshots/praktikum9/08-application-log.png)

### Analisis

Application log memberikan konteks tambahan mengenai aktivitas pengguna dan perubahan yang terjadi pada aplikasi.

---

# Task 9 - Network Forensics

## Tujuan

Menganalisis aktivitas jaringan selama insiden.

### Command

Menggunakan Wireshark:

```bash
wireshark attack.pcap
```

Atau:

```bash
tshark -r attack.pcap
```

Filter:

```text
http
```

![Network Forensics](../../screenshots/praktikum9/09-network-forensics.png)

### Analisis

Network capture dapat memperlihatkan payload SQL Injection, aktivitas upload file, maupun komunikasi HTTP yang mendukung hasil investigasi.

---

# Task 10 - Timeline Reconstruction

## Tujuan

Menyusun urutan kejadian berdasarkan evidence.

| Waktu | Aktivitas | Evidence |
|--------|-----------|----------|
| HH:MM | Initial Access | access.log |
| HH:MM | Login Berhasil | app.log |
| HH:MM | SQL Injection | general.log |
| HH:MM | Upload File | access.log |
| HH:MM | Perubahan Database | after.sql |

![Timeline](../../screenshots/praktikum9/10-timeline.png)

### Analisis

Timeline membantu investigator merekonstruksi seluruh rangkaian insiden secara kronologis.

---

# Task 11 - Findings and Root Cause Analysis

## Tujuan

Mengidentifikasi akar penyebab insiden berdasarkan seluruh evidence.

Komponen yang dianalisis:

- Initial Access
- Execution
- Persistence
- Impact
- Root Cause

![Root Cause Analysis](../../screenshots/praktikum9/11-root-cause-analysis.png)

### Analisis

Seluruh evidence dikorelasikan untuk menentukan bagaimana serangan dimulai, aktivitas attacker, dampak terhadap sistem, serta kontrol keamanan yang gagal mencegah insiden.

---

# Ringkasan Hasil Investigasi

| Tahapan | Hasil |
|---------|-------|
| Evidence Validation | Integritas evidence berhasil diverifikasi |
| Source Identification | Host paling aktif berhasil diidentifikasi |
| Initial Access | Aktivitas login berhasil dianalisis |
| SQL Injection | Payload mencurigakan berhasil ditemukan |
| Unauthorized Access | Indikasi IDOR berhasil dianalisis |
| Persistence | File mencurigakan berhasil diperiksa |
| Database Assessment | Perubahan database berhasil diidentifikasi |
| Application Log | Aktivitas aplikasi berhasil direkonstruksi |
| Network Analysis | Traffic HTTP berhasil dianalisis |
| Timeline | Urutan kejadian berhasil disusun |

---

# Indicators of Compromise (IoC)

Selama proses investigasi diperkenalkan beberapa contoh indikator kompromi, antara lain:

- Suspicious IP Address
- Brute Force Login
- SQL Injection Payload
- Unauthorized Database Modification
- Suspicious Uploaded File
- Web Shell
- Administrator Account Creation
- HTTP Request Anomaly

IoC tersebut menjadi dasar dalam proses investigasi digital terhadap insiden keamanan aplikasi web.

---

# Kesimpulan

Pada praktikum ini dilakukan investigasi terhadap insiden keamanan pada aplikasi web menggunakan berbagai sumber evidence seperti web server log, database log, application log, network capture, serta image database.

Melalui proses validasi evidence, analisis log, identifikasi indikator kompromi, rekonstruksi timeline, hingga root cause analysis, investigator memperoleh gambaran menyeluruh mengenai proses serangan dan dampaknya terhadap sistem. Pendekatan ini menjadi dasar penting dalam pelaksanaan **Digital Forensics & Incident Response (DFIR)** pada lingkungan aplikasi web.
