# Praktikum 1 - Acquire Volatile Data in Linux System

## Pendahuluan

Volatile data merupakan data yang tersimpan sementara di memori sistem dan akan hilang ketika komputer dimatikan atau direstart. Dalam proses digital forensics, pengumpulan volatile data menjadi tahap penting karena dapat memberikan gambaran kondisi sistem secara real-time pada saat investigasi dilakukan.

Artefak volatile yang dapat dikumpulkan meliputi:

- Running Process
- Active Network Connection
- Open Files
- User Activity
- ARP Cache
- Clipboard
- Scheduled Tasks
- Memory Related Information

---

## Tujuan Praktikum

Pada praktikum ini dilakukan proses akuisisi data volatile pada sistem Linux Ubuntu untuk memperoleh berbagai artefak digital yang dapat digunakan dalam proses investigasi forensik.

---

## Environment

| Komponen | Keterangan |
|-----------|------------|
| OS | Ubuntu Linux |
| User | james |
| Tools | Netstat, LSOF, Auditd, Nmap |
| Investigator Mode | Root |

---

# 1. Login ke Sistem

Login menggunakan akun yang telah disediakan.

**Username**

```text
james
```

**Password**

```text
toor
```

### Screenshot

![Login](../screenshots/praktikum1/01-login-ubuntu.png)

---

# 2. Mendapatkan Hak Akses Root

Command:

```bash
sudo su
```

### Screenshot

![Root](../screenshots/praktikum1/02-root-access.png)

### Analisis

Hak akses root diperlukan karena sebagian besar artefak sistem hanya dapat diakses oleh administrator.

---

# 3. Melihat System Uptime

Command:

```bash
w
```

### Screenshot

![Uptime](../screenshots/praktikum1/03-uptime.png)

### Analisis

Informasi uptime membantu investigator menentukan berapa lama sistem berjalan sejak proses boot terakhir.

---

# 4. Analisis Riwayat Login

Command:

```bash
last -a
```

### Screenshot

![Login History](../screenshots/praktikum1/04-login-history.png)

### Analisis

Data login dapat digunakan untuk mengidentifikasi akses pengguna, login tidak sah, maupun aktivitas insider.

---

# 5. Analisis Koneksi Jaringan Aktif

Command:

```bash
netstat
```

### Screenshot

![Netstat](../screenshots/praktikum1/05-netstat.png)

### Analisis

Netstat menampilkan koneksi TCP/UDP aktif serta service yang sedang berjalan pada sistem.

---

# 6. Investigasi Open Files

Command:

```bash
lsof > openfiles.txt
```

### Screenshot

![Open Files](../screenshots/praktikum1/06-openfiles.png)

### Analisis

Artefak ini membantu investigator mengetahui file yang sedang digunakan oleh proses aktif.

---

# 7. Analisis Loaded Kernel Modules

Command:

```bash
lsmod
```

### Screenshot

![Kernel Modules](../screenshots/praktikum1/07-lsmod.png)

### Analisis

Kernel module yang tidak dikenal dapat menjadi indikator adanya rootkit atau modifikasi kernel.

---

# 8. Audit Framework Analysis

Install:

```bash
apt install -y auditd
```

Report:

```bash
aureport
```

### Screenshot

![Audit Report](../screenshots/praktikum1/08-aureport.png)

### Analisis

Auditd menyediakan catatan aktivitas sistem yang penting dalam investigasi keamanan.

---

# 9. Tracking Aktivitas User Berdasarkan UID

Command:

```bash
id james
```

```bash
ausearch -ui 1000 --interpret
```

### Screenshot

![UID Investigation](../screenshots/praktikum1/09-ausearch.png)

### Analisis

Memungkinkan investigator melakukan pelacakan aktivitas berdasarkan identitas pengguna tertentu.

---

# 10. Investigasi Scheduled Task

Lokasi:

```bash
/var/spool/cron
```

```bash
/etc/cron.daily
```

### Screenshot

![Cron](../screenshots/praktikum1/10-cron.png)

### Analisis

Scheduled task sering digunakan malware sebagai mekanisme persistence.

---

# 11. Analisis Bash History

Command:

```bash
cat ~/.bash_history
```

### Screenshot

![Bash History](../screenshots/praktikum1/11-bash-history.png)

### Analisis

Riwayat command dapat mengungkap aktivitas pengguna maupun attacker.

---

# 12. Analisis ARP Cache

Command:

```bash
arp
```

### Screenshot

![ARP Cache](../screenshots/praktikum1/12-arp-cache.png)

### Analisis

ARP cache membantu mengidentifikasi perangkat lain yang pernah berkomunikasi dalam jaringan lokal.

---

# 13. Investigasi Running Process

Command:

```bash
ps auxww
```

### Screenshot

![Process](../screenshots/praktikum1/13-process.png)

### Analisis

Running process memberikan gambaran aktivitas sistem yang sedang berlangsung.

---

# 14. Investigasi Direktori /proc

Command:

```bash
cd /proc
ls
```

### Screenshot

![Proc](../screenshots/praktikum1/14-proc.png)

### Analisis

Direktori /proc menyediakan informasi real-time mengenai proses dan kernel Linux.

---

# 15. Analisis Clipboard

Command:

```bash
xclip -o
```

### Screenshot

![Clipboard](../screenshots/praktikum1/15-clipboard.png)

### Analisis

Clipboard dapat menyimpan artefak penting yang secara tidak sengaja ditinggalkan pengguna.

---

# 16. Identifikasi Hostname

Command:

```bash
hostname
```

### Screenshot

![Hostname](../screenshots/praktikum1/16-hostname.png)

### Analisis

Hostname digunakan untuk mengidentifikasi sistem yang sedang dianalisis.

---

# 17. Scanning Open TCP Port

Command:

```bash
nmap -sT localhost
```

### Screenshot

![TCP Scan](../screenshots/praktikum1/17-nmap-tcp.png)

### Analisis

Scanning TCP membantu mengidentifikasi service yang aktif pada sistem.

---

# 18. Scanning Open UDP Port

Command:

```bash
nmap -sU localhost
```

### Screenshot

![UDP Scan](../screenshots/praktikum1/18-nmap-udp.png)

### Analisis

Port UDP sering digunakan oleh berbagai layanan jaringan dan perlu diperiksa selama investigasi.

---

# Kesimpulan

Praktikum ini berhasil melakukan akuisisi berbagai artefak volatile pada sistem Linux, meliputi informasi uptime, login history, koneksi jaringan, open files, running process, audit log, scheduled task, hingga hasil scanning port.

Data volatile memiliki nilai forensik yang tinggi karena menggambarkan kondisi sistem secara langsung saat investigasi berlangsung. Oleh karena itu proses akuisisi harus dilakukan secepat mungkin sebelum data hilang akibat shutdown atau restart sistem.
