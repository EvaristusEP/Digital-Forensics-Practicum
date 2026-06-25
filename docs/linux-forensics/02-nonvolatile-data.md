# Praktikum 2 - Acquire Non-Volatile Data in Linux System

## Pendahuluan

Non-volatile data merupakan data yang tetap tersimpan pada media penyimpanan meskipun sistem dimatikan atau mengalami proses restart. Dalam digital forensics, data jenis ini memiliki peran penting karena dapat digunakan untuk merekonstruksi aktivitas pengguna, mengidentifikasi jejak serangan, mendeteksi malware, serta menganalisis pelanggaran keamanan yang pernah terjadi pada sistem.

Berbeda dengan volatile data yang hilang ketika sistem dimatikan, non-volatile data tetap tersedia pada media penyimpanan sehingga menjadi salah satu sumber bukti utama dalam proses investigasi digital forensics.

---

## Tujuan Praktikum

Pada praktikum ini dilakukan proses akuisisi data non-volatile pada sistem operasi Linux Ubuntu. Setelah menyelesaikan praktikum ini, diharapkan mampu:

- Memperoleh hak akses administrator (root).
- Mengidentifikasi informasi kernel Linux.
- Mengumpulkan informasi akun pengguna.
- Menganalisis riwayat login sistem.
- Mengumpulkan authentication log.
- Melakukan deteksi rootkit menggunakan RKHunter.

---

## Environment

| Komponen | Keterangan |
|----------|------------|
| Sistem Operasi | Ubuntu Linux |
| User | james |
| Mode Akses | Root |
| Tools | Terminal, RKHunter |

---

# 1. Mendapatkan Hak Akses Root

## Tujuan

Mengaktifkan hak akses administrator agar investigator dapat mengakses seluruh artefak sistem yang memerlukan izin root.

### Command

```bash
sudo su
```

Password:

```text
toor
```


![Root Access](../../screenshots/praktikum2/01-root-access.png)

### Analisis

Hak akses root diperlukan karena sebagian besar file sistem Linux, seperti `/etc/shadow` dan berbagai file log keamanan, hanya dapat diakses oleh administrator. Tahap ini menjadi langkah awal sebelum proses akuisisi artefak dilakukan.

---

# 2. Melihat Versi Kernel Linux

## Tujuan

Mengidentifikasi versi kernel Linux yang digunakan pada sistem.

### Command

```bash
uname -r
```


![Kernel Version](../../screenshots/praktikum2/02-kernel-version.png)

### Analisis

Kernel merupakan inti dari sistem operasi Linux. Informasi versi kernel berguna untuk mengetahui patch keamanan yang telah diterapkan serta membantu investigator mengidentifikasi kemungkinan kerentanan (vulnerability) yang dapat dimanfaatkan oleh attacker.

---

# 3. Mengumpulkan Informasi User Account

## Tujuan

Mengidentifikasi seluruh akun pengguna yang terdaftar pada sistem.

### Command

```bash
cat /etc/passwd
```

```bash
cat /etc/shadow
```


![User Account](../../screenshots/praktikum2/03-user-account.png)

### Analisis

File `/etc/passwd` berisi informasi akun pengguna seperti username, UID, GID, home directory, dan shell yang digunakan. Sedangkan `/etc/shadow` menyimpan password dalam bentuk terenkripsi. Kedua file ini menjadi artefak penting untuk mendeteksi akun yang mencurigakan maupun akun backdoor.

---

# 4. Menampilkan Daftar Username

## Tujuan

Menampilkan daftar username tanpa informasi tambahan.

### Command

```bash
cut -d: -f1 /etc/passwd
```


![Username List](../../screenshots/praktikum2/04-username-list.png)

### Analisis

Perintah ini hanya mengambil field pertama dari file `/etc/passwd`, sehingga investigator dapat melihat seluruh akun pengguna secara lebih ringkas. Informasi ini memudahkan proses identifikasi akun yang tidak dikenal.

---

# 5. Mengumpulkan Riwayat Login Sistem

## Tujuan

Melihat histori login pengguna pada sistem Linux.

### Command

```bash
last -f /var/log/wtmp
```


![Login History](../../screenshots/praktikum2/05-login-history.png)

### Analisis

Riwayat login memberikan informasi mengenai waktu login, logout, reboot sistem, serta sesi terminal yang pernah digunakan. Data ini membantu investigator dalam menyusun timeline aktivitas pengguna maupun mendeteksi akses yang tidak sah.

---

# 6. Mengumpulkan Authentication Log

## Tujuan

Mengidentifikasi aktivitas autentikasi dan otorisasi pada sistem.

### Command

```bash
cat /var/log/auth.log
```


![Authentication Log](../../screenshots/praktikum2/06-authentication-log.png)

### Analisis

Authentication log mencatat berbagai aktivitas penting seperti login berhasil, login gagal, penggunaan perintah sudo, autentikasi SSH, hingga percobaan brute force. Log ini merupakan salah satu sumber evidence utama dalam investigasi keamanan sistem Linux.

---

# 7. Menginstal RKHunter

## Tujuan

Menginstal aplikasi Rootkit Hunter (RKHunter) untuk melakukan deteksi rootkit pada sistem.

### Command

```bash
apt install rkhunter
```

Jika muncul konfirmasi:

```text
Do you want to continue?
```

Masukkan:

```text
Y
```


![Install RKHunter](../../screenshots/praktikum2/07-install-rkhunter.png)

### Analisis

RKHunter merupakan utilitas keamanan yang digunakan untuk mendeteksi rootkit, malware, hidden file, startup modification, serta berbagai indikator kompromi lainnya.

---

# 8. Melakukan Scanning Rootkit

## Tujuan

Melakukan pemeriksaan terhadap kemungkinan adanya malware maupun rootkit.

### Command

```bash
rkhunter --check --rwo
```


![RKHunter Scan](../../screenshots/praktikum2/08-rkhunter-scan.png)

### Analisis

Parameter `--check` digunakan untuk menjalankan proses pemeriksaan, sedangkan `--rwo` hanya menampilkan hasil berupa warning. Investigator perlu memperhatikan indikator seperti hidden file, unknown service, unauthorized sudo, maupun startup modification yang dapat menunjukkan adanya kompromi pada sistem.

---

# Artefak Digital yang Berhasil Dikumpulkan

Selama praktikum berlangsung berhasil diperoleh berbagai artefak digital, antara lain:

- Informasi versi kernel Linux.
- Informasi akun pengguna.
- Daftar username.
- Riwayat login sistem.
- Authentication log.
- Aktivitas sudo.
- Hasil scanning rootkit menggunakan RKHunter.

Artefak tersebut dapat digunakan sebagai dasar dalam proses incident response, threat hunting, malware investigation, audit keamanan, serta rekonstruksi aktivitas yang terjadi pada sistem.

---

# Kesimpulan

Pada praktikum ini dilakukan proses akuisisi data non-volatile pada sistem Linux Ubuntu. Investigator berhasil memperoleh berbagai artefak permanen seperti informasi kernel, data akun pengguna, histori login, authentication log, hingga hasil deteksi rootkit menggunakan RKHunter.

Karena data non-volatile tetap tersimpan meskipun sistem dimatikan, artefak tersebut menjadi sumber bukti utama dalam proses digital forensics. Informasi yang diperoleh dapat digunakan untuk mendukung proses investigasi, mengidentifikasi indikasi kompromi, serta membantu merekonstruksi aktivitas yang pernah terjadi pada sistem.
