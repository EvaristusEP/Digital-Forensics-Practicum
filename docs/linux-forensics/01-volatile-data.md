# Praktikum 1 - Acquire Volatile Data in Linux System

## Pendahuluan

Volatile data merupakan data yang tersimpan di memori (RAM) dan akan hilang ketika sistem dimatikan atau mengalami restart. Oleh karena itu, proses akuisisi data volatile menjadi tahapan penting dalam digital forensics karena dapat memberikan gambaran kondisi sistem saat insiden sedang berlangsung.

Beberapa artefak volatile yang umum dikumpulkan meliputi informasi uptime sistem, aktivitas login pengguna, koneksi jaringan yang sedang aktif, proses yang berjalan, file yang sedang digunakan, hingga informasi yang tersimpan pada clipboard. Seluruh artefak tersebut dapat membantu investigator dalam melakukan analisis serta merekonstruksi kronologi suatu insiden keamanan.

---

## Tujuan Praktikum

Pada praktikum ini dilakukan proses akuisisi berbagai artefak volatile pada sistem operasi Linux Ubuntu. Setelah menyelesaikan praktikum ini, diharapkan mampu:

- Memperoleh hak akses administrator (root).
- Mengumpulkan informasi kondisi sistem yang sedang berjalan.
- Mengidentifikasi aktivitas pengguna.
- Menganalisis koneksi jaringan aktif.
- Mengidentifikasi proses dan file yang sedang digunakan.
- Mengumpulkan artefak digital yang bersifat sementara sebagai bahan investigasi.

---

## Environment

| Komponen | Keterangan |
|----------|------------|
| Sistem Operasi | Ubuntu Linux |
| User | james |
| Mode Akses | Root |
| Tools | Terminal, Netstat, LSOF, Nmap, Auditd |

---

# 1. Login ke Sistem

## Tujuan

Masuk ke sistem Linux menggunakan akun yang telah disediakan sebelum proses investigasi dilakukan.

### Username

```text
james
```

### Password

```text
toor
```


![Login](../../screenshots/praktikum1/01-login.png)

### Analisis

Tahap login merupakan langkah awal sebelum investigator dapat mengakses sistem operasi. Akun yang digunakan pada praktikum ini memiliki hak akses standar yang kemudian akan ditingkatkan menjadi administrator.

---

# 2. Mendapatkan Hak Akses Root

## Tujuan

Mengaktifkan hak akses administrator agar investigator dapat memperoleh seluruh artefak sistem.

### Command

```bash
sudo su
```


![Root Access](../../screenshots/praktikum1/02-root-access.png)

### Analisis

Hak akses root diperlukan karena sebagian besar artefak sistem Linux hanya dapat diakses oleh administrator. Dengan mode root, investigator dapat melakukan proses akuisisi tanpa dibatasi oleh permission pengguna biasa.

---

# 3. Melihat System Uptime

## Tujuan

Mengetahui lama sistem berjalan sejak proses boot terakhir.

### Command

```bash
w
```


![System Uptime](../../screenshots/praktikum1/03-system-uptime.png)

### Analisis

Informasi uptime membantu investigator mengetahui apakah sistem baru saja dinyalakan atau telah berjalan dalam waktu yang lama. Data ini juga berguna ketika membangun timeline insiden.

---

# 4. Mengumpulkan Riwayat Login

## Tujuan

Mengidentifikasi pengguna yang pernah masuk ke sistem.

### Command

```bash
last -a
```


![Login History](../../screenshots/praktikum1/04-login-history.png)

### Analisis

Riwayat login dapat menunjukkan aktivitas pengguna, waktu login, alamat IP asal, serta membantu mendeteksi login yang tidak normal.

---

# 5. Melihat Koneksi Jaringan Aktif

## Tujuan

Mengidentifikasi koneksi jaringan yang sedang aktif pada sistem.

### Command

```bash
netstat
```


![Netstat](../../screenshots/praktikum1/05-netstat.png)

### Analisis

Koneksi jaringan aktif dapat menunjukkan komunikasi yang sedang berlangsung antara sistem dengan perangkat lain. Informasi ini penting untuk mendeteksi kemungkinan koneksi yang tidak sah.

---

# 6. Mengidentifikasi File yang Sedang Digunakan

## Tujuan

Menampilkan daftar file yang sedang diakses oleh proses aktif.

### Command

```bash
lsof > openfiles.txt
```


![Open Files](../../screenshots/praktikum1/06-open-files.png)

### Analisis

Perintah `lsof` digunakan untuk melihat file yang sedang dibuka oleh suatu proses. Artefak ini dapat membantu mengidentifikasi file penting yang sedang digunakan oleh aplikasi maupun malware.

---

# 7. Melihat Kernel Module

## Tujuan

Mengidentifikasi module kernel yang sedang dimuat.

### Command

```bash
lsmod
```


![Kernel Module](../../screenshots/praktikum1/07-lsmod.png)

### Analisis

Kernel module yang tidak dikenal dapat menjadi indikator adanya rootkit atau modifikasi kernel yang dilakukan oleh attacker.

---

# 8. Audit Framework Analysis

## Tujuan

Melihat aktivitas audit pada sistem.

### Install Auditd

```bash
apt install auditd
```

### Menampilkan Report

```bash
aureport
```


![Audit Report](../../screenshots/praktikum1/08-aureport.png)

### Analisis

Auditd mencatat berbagai aktivitas penting pada sistem sehingga dapat digunakan untuk melakukan investigasi terhadap aktivitas pengguna maupun perubahan konfigurasi.

---

# 9. Analisis Aktivitas User Berdasarkan UID

## Tujuan

Menelusuri aktivitas pengguna tertentu berdasarkan User ID.

### Command

```bash
id james
```

```bash
ausearch -ui 1000 --interpret
```


![User Activity](../../screenshots/praktikum1/09-user-activity.png)

### Analisis

Pencarian berdasarkan UID memudahkan investigator dalam mengumpulkan seluruh aktivitas yang dilakukan oleh seorang pengguna tertentu.

---

# 10. Pemeriksaan Scheduled Task

## Tujuan

Mengidentifikasi task yang dijalankan secara otomatis.

### Command

```bash
cd /var/spool/cron
```

```bash
cd /etc/cron.daily
```


![Cron](../../screenshots/praktikum1/10-cron.png)

### Analisis

Scheduled task sering dimanfaatkan sebagai mekanisme persistence oleh malware agar tetap aktif setelah sistem dinyalakan kembali.

---

# 11. Analisis Bash History

## Tujuan

Melihat riwayat perintah yang pernah dijalankan pengguna.

### Command

```bash
cat ~/.bash_history
```


![Bash History](../../screenshots/praktikum1/11-bash-history.png)

### Analisis

Riwayat command dapat digunakan untuk mengetahui aktivitas administrator maupun attacker selama menggunakan terminal.

---

# 12. Analisis ARP Cache

## Tujuan

Mengidentifikasi perangkat yang pernah berkomunikasi dalam jaringan lokal.

### Command

```bash
arp
```


![ARP Cache](../../screenshots/praktikum1/12-arp-cache.png)

### Analisis

ARP Cache menyimpan pemetaan alamat IP dan MAC Address yang pernah digunakan sehingga berguna dalam analisis jaringan lokal.

---

# 13. Mengidentifikasi Running Process

## Tujuan

Menampilkan seluruh proses yang sedang berjalan.

### Command

```bash
ps auxww
```


![Running Process](../../screenshots/praktikum1/13-running-process.png)

### Analisis

Running process memberikan informasi mengenai aplikasi maupun service yang aktif sehingga investigator dapat mendeteksi proses yang mencurigakan.

---

# 14. Analisis Direktori `/proc`

## Tujuan

Melihat informasi proses yang disediakan oleh ProcFS.

### Command

```bash
cd /proc
```

```bash
ls
```


![Proc Filesystem](../../screenshots/praktikum1/14-proc-filesystem.png)

### Analisis

Direktori `/proc` menyediakan informasi real-time mengenai proses yang sedang berjalan dan kondisi kernel Linux.

---

# 15. Analisis Clipboard

## Tujuan

Mengidentifikasi informasi yang tersimpan pada clipboard.

### Command

```bash
xclip -o
```


![Clipboard](../../screenshots/praktikum1/15-clipboard.png)

### Analisis

Clipboard terkadang menyimpan data sensitif seperti password, token, maupun potongan teks yang dapat menjadi artefak digital penting.

---

# 16. Mengetahui Hostname Sistem

## Tujuan

Mengidentifikasi nama host dari sistem yang sedang dianalisis.

### Command

```bash
hostname
```


![Hostname](../../screenshots/praktikum1/16-hostname.png)

### Analisis

Hostname digunakan sebagai identitas perangkat dan berguna ketika investigator menganalisis beberapa sistem dalam satu jaringan.

---

# 17. Melakukan TCP Port Scanning

## Tujuan

Mengidentifikasi port TCP yang terbuka.

### Command

```bash
nmap -sT localhost
```


![TCP Scan](../../screenshots/praktikum1/17-tcp-scan.png)

### Analisis

Scanning TCP membantu investigator mengetahui service yang aktif pada sistem sehingga dapat digunakan untuk mengidentifikasi permukaan serangan (attack surface).

---

# 18. Melakukan UDP Port Scanning

## Tujuan

Mengidentifikasi port UDP yang terbuka.

### Command

```bash
nmap -sU localhost
```


![UDP Scan](../../screenshots/praktikum1/18-udp-scan.png)

### Analisis

Selain TCP, layanan berbasis UDP juga perlu diperiksa karena beberapa service jaringan menggunakan protokol UDP dan dapat menjadi target eksploitasi.

---

# Artefak Digital yang Berhasil Dikumpulkan

Selama praktikum berlangsung berhasil diperoleh berbagai artefak digital, antara lain:

- Informasi uptime sistem.
- Riwayat login pengguna.
- Koneksi jaringan aktif.
- Open files.
- Kernel module.
- Audit report.
- Aktivitas pengguna.
- Scheduled task.
- Bash history.
- ARP Cache.
- Running process.
- Informasi ProcFS.
- Clipboard.
- Hostname.
- Hasil scanning TCP dan UDP.

Artefak tersebut dapat digunakan sebagai dasar dalam proses incident response, malware investigation, threat hunting, serta rekonstruksi kronologi suatu insiden.

---

# Kesimpulan

Praktikum ini membahas proses akuisisi data volatile pada sistem Linux Ubuntu sebagai langkah awal dalam investigasi digital forensics. Berbagai artefak seperti proses yang sedang berjalan, koneksi jaringan, aktivitas pengguna, hingga informasi sistem berhasil dikumpulkan menggunakan beberapa utilitas bawaan Linux.

Karena data volatile akan hilang ketika sistem dimatikan atau mengalami restart, proses akuisisi harus dilakukan sesegera mungkin sebelum investigator melanjutkan ke tahap analisis berikutnya. Data yang diperoleh dari praktikum ini menjadi dasar penting dalam memahami kondisi sistem saat insiden berlangsung.
