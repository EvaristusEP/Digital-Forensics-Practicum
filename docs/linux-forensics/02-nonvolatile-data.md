# Praktikum 2 - Acquire Non-Volatile Data in Linux System

## Pendahuluan

Non-volatile data merupakan data yang tetap tersimpan pada media penyimpanan meskipun sistem dimatikan atau mengalami restart. Dalam proses digital forensics, data non-volatile menjadi salah satu sumber bukti utama karena dapat digunakan untuk merekonstruksi aktivitas pengguna, mengidentifikasi jejak serangan, mendeteksi malware, serta menganalisis pelanggaran keamanan yang pernah terjadi pada suatu sistem.

Berbeda dengan volatile data yang hanya tersedia selama sistem berjalan, non-volatile data tetap tersimpan sehingga investigator masih dapat melakukan analisis setelah insiden selesai terjadi.

---

## Tujuan Praktikum

Pada praktikum ini dilakukan proses akuisisi data non-volatile pada sistem Linux Ubuntu dengan tujuan:

- Mengumpulkan informasi kernel Linux.
- Mengidentifikasi akun pengguna yang terdaftar.
- Menganalisis riwayat login pengguna.
- Mengumpulkan authentication log.
- Mengidentifikasi aktivitas sudo.
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

Mengaktifkan hak akses administrator agar investigator dapat memperoleh seluruh artefak sistem yang membutuhkan izin root.

### Command

```bash
sudo su
```

Password

```text
toor
```

### Screenshot

```
screenshots/praktikum2/01-terminal-root.png
```

### Analisis

Hak akses root diperlukan karena sebagian besar file sistem seperti `/etc/shadow` maupun log autentikasi hanya dapat diakses oleh administrator. Dengan menggunakan mode root, investigator dapat memperoleh seluruh artefak tanpa mengalami keterbatasan hak akses.

---

# 2. Melihat Versi Kernel Linux

## Tujuan

Mengidentifikasi versi kernel Linux yang digunakan oleh sistem.

### Command

```bash
uname -r
```

### Screenshot

```
screenshots/praktikum2/02-uname-r.png
```

### Analisis

Kernel merupakan inti dari sistem operasi Linux. Informasi versi kernel dapat digunakan untuk mengetahui patch keamanan yang telah diterapkan, mengevaluasi kemungkinan kerentanan (vulnerability), serta membantu proses identifikasi teknik eksploitasi yang mungkin digunakan oleh attacker.

---

# 3. Mengumpulkan Informasi User Account

## Tujuan

Mengidentifikasi seluruh akun pengguna yang tersedia pada sistem.

### Command

```bash
cat /etc/passwd
```

```bash
cat /etc/shadow
```

### Screenshot

```
screenshots/praktikum2/03-passwd.png
```

### Analisis

File `/etc/passwd` berisi informasi dasar mengenai seluruh akun pengguna seperti username, UID, GID, home directory, serta default shell. Sedangkan `/etc/shadow` menyimpan informasi password yang telah dienkripsi. Kedua file tersebut sangat penting dalam investigasi karena dapat digunakan untuk menemukan akun mencurigakan maupun akun backdoor.

---

# 4. Menampilkan Daftar Username

## Tujuan

Menampilkan hanya nama akun pengguna tanpa informasi tambahan.

### Command

```bash
cut -d: -f1 /etc/passwd
```

### Screenshot

```
screenshots/praktikum2/04-user-list.png
```

### Analisis

Perintah ini hanya mengambil field pertama dari file `/etc/passwd`, sehingga investigator dapat memperoleh daftar seluruh username dengan lebih mudah. Langkah ini berguna untuk mengidentifikasi akun yang tidak dikenal atau akun yang dibuat tanpa otorisasi.

---

# 5. Mengumpulkan Riwayat Login

## Tujuan

Melihat histori login pengguna pada sistem.

### Command

```bash
last -f /var/log/wtmp
```

### Screenshot

```
screenshots/praktikum2/05-login-history.png
```

### Analisis

Riwayat login memberikan informasi mengenai waktu login, logout, reboot sistem, serta sesi terminal yang pernah dilakukan. Informasi ini membantu investigator dalam menyusun timeline aktivitas pengguna maupun mendeteksi login yang tidak normal.

---

# 6. Mengumpulkan Authentication Log

## Tujuan

Mengidentifikasi aktivitas autentikasi dan otorisasi pada sistem.

### Command

```bash
cat /var/log/auth.log
```

### Screenshot

```
screenshots/praktikum2/06-auth-log.png
```

### Analisis

Authentication log mencatat berbagai aktivitas keamanan seperti login berhasil, login gagal, penggunaan perintah sudo, autentikasi SSH, hingga percobaan brute force. Log ini merupakan salah satu sumber evidence utama dalam investigasi keamanan sistem Linux.

---

# 7. Menginstal RKHunter

## Tujuan

Menginstal Rootkit Hunter sebagai tools pendeteksi rootkit.

### Command

```bash
apt install rkhunter
```

Ketika muncul konfirmasi:

```text
Do you want to continue?
```

Masukkan:

```text
Y
```

### Screenshot

```
screenshots/praktikum2/07-install-rkhunter.png
```

### Analisis

RKHunter merupakan utilitas keamanan yang digunakan untuk mendeteksi rootkit, malware, file mencurigakan, perubahan startup system, serta berbagai indikator kompromi lainnya.

---

# 8. Melakukan Scanning Rootkit

## Tujuan

Melakukan pemeriksaan terhadap kemungkinan adanya malware atau rootkit pada sistem.

### Command

```bash
rkhunter --check --rwo
```

### Screenshot

```
screenshots/praktikum2/08-rkhunter-scan.png
```

### Analisis

Parameter `--check` digunakan untuk menjalankan proses pemeriksaan, sedangkan `--rwo` hanya menampilkan hasil berupa warning. Investigator perlu memperhatikan indikator seperti hidden file, unknown service, perubahan startup file, maupun aktivitas lain yang dapat menunjukkan adanya kompromi pada sistem.

---

# Artefak Digital yang Berhasil Dikumpulkan

Selama praktikum berlangsung berhasil diperoleh beberapa artefak digital, antara lain:

- Informasi versi kernel Linux.
- Data akun pengguna.
- Daftar username.
- Riwayat login sistem.
- Authentication log.
- Aktivitas sudo.
- Hasil pemindaian rootkit menggunakan RKHunter.

Artefak tersebut dapat digunakan sebagai dasar dalam proses incident response, malware investigation, threat hunting, maupun audit keamanan sistem.

---

# Kesimpulan

Pada praktikum ini dilakukan proses akuisisi data non-volatile pada sistem Linux Ubuntu. Investigator berhasil memperoleh berbagai artefak permanen seperti informasi kernel, data akun pengguna, histori login, authentication log, serta hasil deteksi rootkit menggunakan RKHunter.

Berbeda dengan data volatile, artefak non-volatile tetap tersedia meskipun sistem telah dimatikan sehingga menjadi salah satu sumber bukti utama dalam proses digital forensics. Informasi yang diperoleh dari praktikum ini dapat dimanfaatkan untuk merekonstruksi aktivitas pengguna, mengidentifikasi indikasi kompromi, serta mendukung proses investigasi keamanan secara menyeluruh.
