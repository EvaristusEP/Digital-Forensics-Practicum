# Praktikum 5 - Android Reverse Engineering & Mobile Security Assessment

## Pendahuluan

Pada praktikum ini dilakukan pengenalan terhadap proses reverse engineering aplikasi Android menggunakan aplikasi laboratorium **InsecureShop**. Aplikasi ini dirancang khusus sebagai media pembelajaran untuk memahami berbagai kerentanan keamanan pada aplikasi Android tanpa melanggar etika maupun hukum.

Selama praktikum dilakukan proses **static analysis** dan **dynamic analysis** untuk memahami struktur aplikasi Android, melakukan identifikasi potensi vulnerability, serta mengenal berbagai tools yang umum digunakan dalam mobile application security assessment.

---

## Scope dan Legal Notice

Praktikum ini hanya dilakukan pada aplikasi yang memang dibuat untuk kebutuhan pembelajaran keamanan aplikasi mobile.

Seluruh teknik yang digunakan hanya diperbolehkan pada:

- Aplikasi milik sendiri.
- Environment laboratorium.
- Aplikasi vulnerable untuk pelatihan.
- Kegiatan security research yang memiliki izin.

Praktikum ini **tidak ditujukan** untuk melakukan pengujian terhadap aplikasi produksi maupun sistem yang tidak memiliki izin resmi.

---

## Tujuan Praktikum

Setelah menyelesaikan praktikum ini diharapkan mampu:

- Memahami struktur dasar file APK Android.
- Mengenal AndroidManifest.xml.
- Memahami konsep DEX dan Smali.
- Melakukan static analysis pada aplikasi Android.
- Mengenal proses dynamic analysis.
- Mengidentifikasi beberapa kerentanan umum pada aplikasi Android.

---

## Target Aplikasi

Target yang digunakan selama praktikum adalah:

```text
InsecureShop
```

Aplikasi ini sengaja dibuat mengandung berbagai vulnerability sehingga aman digunakan sebagai media pembelajaran reverse engineering.

---

## Metodologi Assessment

Tahapan assessment pada praktikum ini meliputi:

```text
APK Acquisition
        ↓
Static Analysis
        ↓
Manifest Review
        ↓
Source Code Review
        ↓
Dynamic Analysis
        ↓
Traffic Inspection
        ↓
Runtime Instrumentation
        ↓
Security Finding & Reporting
```

---

## Environment

| Komponen | Keterangan |
|----------|------------|
| Sistem Operasi | Windows / Ubuntu / Kali Linux |
| Target | InsecureShop APK |
| Emulator | Android Emulator |
| Platform | Android Studio |

---

# 1. Persiapan Environment

## Tujuan

Mempersiapkan seluruh lingkungan praktikum sebelum proses reverse engineering dilakukan.

### Environment Minimum

| Komponen | Minimum |
|----------|---------|
| RAM | 8 GB |
| Storage | 20 GB |
| Processor | Intel i5 / Ryzen 5 |
| Virtualization | Enabled |

![Environment](../../screenshots/praktikum5/01-environment.png)

### Analisis

Environment yang memadai diperlukan agar Android Emulator dan berbagai tools analisis dapat berjalan dengan stabil selama proses praktikum.

---

# 2. Android Studio

## Tujuan

Menginstal Android Studio beserta Android SDK dan Android Emulator.

### Komponen

- Android SDK
- Android SDK Platform Tools
- Android Emulator

### Verifikasi

```bash
adb version
```

![Android Studio](../../screenshots/praktikum5/02-android-studio.png)

### Analisis

Android Studio digunakan sebagai platform utama untuk menjalankan Android Emulator serta menyediakan Android Debug Bridge (ADB) yang dibutuhkan selama proses analisis.

---

# 3. Instalasi JADX

## Tujuan

Menginstal tool decompiler untuk membaca source code aplikasi Android.

### Verifikasi

```bash
jadx --version
```

![JADX](../../screenshots/praktikum5/03-jadx.png)

### Analisis

JADX digunakan untuk mengubah file DEX menjadi source code Java sehingga investigator dapat melakukan code review secara lebih mudah.

---

# 4. Instalasi APKTool

## Tujuan

Menginstal APKTool sebagai resource decoder.

### Verifikasi

```bash
apktool
```

![APKTool](../../screenshots/praktikum5/04-apktool.png)

### Analisis

APKTool digunakan untuk melakukan proses decompile resource aplikasi Android, termasuk AndroidManifest.xml, resource XML, maupun Smali code.

---

# 5. Instalasi Burp Suite

## Tujuan

Menyiapkan Burp Suite Community Edition sebagai tools untuk traffic inspection.

![Burp Suite](../../screenshots/praktikum5/05-burp-suite.png)

### Analisis

Burp Suite digunakan untuk melakukan monitoring maupun analisis komunikasi HTTP/HTTPS antara aplikasi Android dengan server.

---

# 6. Instalasi Frida

## Tujuan

Menginstal Frida sebagai runtime instrumentation framework.

### Command

```bash
pip install frida-tools
```

### Verifikasi

```bash
frida --version
```

![Frida](../../screenshots/praktikum5/06-frida.png)

### Analisis

Frida memungkinkan investigator melakukan proses hooking terhadap aplikasi Android saat sedang berjalan sehingga memudahkan analisis perilaku aplikasi secara dinamis.

---

# 7. Tools Mobile Security Assessment

Selama praktikum digunakan beberapa tools utama berikut.

| Tools | Fungsi |
|--------|--------|
| Android Studio | Android Emulator |
| ADB | Android Debug Bridge |
| JADX | Java/Kotlin Decompiler |
| APKTool | Resource Decoder |
| Burp Suite | Traffic Inspection |
| Frida | Runtime Instrumentation |
| MobSF | Automated Mobile Security Assessment |

![Tools](../../screenshots/praktikum5/07-tools-overview.png)

### Analisis

Setiap tools memiliki fungsi yang berbeda sehingga saling melengkapi dalam proses mobile application security assessment, mulai dari static analysis hingga dynamic analysis.

---

# Konsep yang Dipelajari

Selama praktikum diperkenalkan beberapa konsep dasar mobile security, antara lain:

- APK Structure
- AndroidManifest.xml
- DEX File
- Smali Code
- Static Analysis
- Dynamic Analysis
- Runtime Inspection
- Exported Component
- Insecure Storage
- WebView Security
- Deep Link Exposure

Konsep-konsep tersebut menjadi dasar sebelum melakukan analisis keamanan aplikasi Android secara lebih mendalam.

---

# Kesimpulan

Pada praktikum ini dilakukan persiapan environment untuk mobile application security assessment menggunakan aplikasi laboratorium **InsecureShop**. Berbagai tools seperti Android Studio, JADX, APKTool, Burp Suite, Frida, dan MobSF berhasil dipersiapkan sebagai lingkungan kerja untuk proses reverse engineering.

Pemahaman mengenai struktur APK, AndroidManifest.xml, static analysis, serta dynamic analysis menjadi fondasi penting sebelum melakukan identifikasi vulnerability pada aplikasi Android pada praktikum selanjutnya.
