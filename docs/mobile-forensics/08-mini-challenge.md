# Praktikum 8 - Mini Challenge: Practical Mobile Security Assessment

## Pendahuluan

Setelah mempelajari konsep **Static Analysis** dan **Dynamic Analysis**, pada praktikum ini dilakukan simulasi sederhana berupa **Mobile Security Assessment** terhadap aplikasi laboratorium **InsecureShop**.

Berbeda dengan praktikum sebelumnya yang berfokus pada penggunaan tools, praktikum ini menekankan proses berpikir sebagai seorang **Mobile Security Assessor**, mulai dari proses identifikasi vulnerability hingga penyusunan laporan temuan keamanan.

Seluruh aktivitas dilakukan pada aplikasi laboratorium yang memang dirancang untuk kebutuhan pembelajaran keamanan aplikasi Android.

---

## Tujuan Praktikum

Setelah menyelesaikan praktikum ini diharapkan mampu:

- Menghubungkan hasil static analysis dan dynamic analysis.
- Mengidentifikasi vulnerability pada aplikasi Android.
- Melakukan assessment sederhana menggunakan berbagai tools.
- Menyusun dokumentasi security finding.
- Memahami workflow mobile application security assessment.

---

## Target Aplikasi

```text
InsecureShop.apk
```

---

## Tools

| Tools | Fungsi |
|---------|--------|
| JADX | Source Code Review |
| APKTool | Manifest Analysis |
| Android Emulator | Dynamic Analysis |
| ADB | Runtime Inspection |
| Burp Suite | Traffic Analysis |
| Frida | Runtime Instrumentation |

---

# Challenge 1 - Menemukan Hardcoded Credential

## Tujuan

Mengidentifikasi credential yang disimpan langsung pada source code aplikasi Android.

### Tools

- JADX

### Langkah Praktikum

Membuka aplikasi menggunakan JADX.

```bash
jadx-gui InsecureShop.apk
```

Cari keyword berikut:

```text
password
secret
token
admin
```

Periksa package:

```text
com.insecureshop
```

Contoh kode:

```java
if(username.equals("admin") &&
password.equals("SuperSecure123"))
```

![Challenge 1](../../screenshots/praktikum8/01-hardcoded-credential.png)

### Analisis

Hardcoded credential memungkinkan attacker memperoleh username maupun password hanya melalui proses reverse engineering APK sehingga berpotensi menyebabkan credential disclosure maupun authentication bypass.

---

# Challenge 2 - Exported Activity Analysis

## Tujuan

Mengidentifikasi Activity yang dapat diakses oleh aplikasi lain.

### Tools

- JADX
- APKTool
- ADB

### Langkah Praktikum

Periksa file:

```text
AndroidManifest.xml
```

Cari konfigurasi:

```xml
android:exported="true"
```

Enumerasi menggunakan:

```bash
adb shell dumpsys package com.insecureshop
```

Cari:

```text
Activity Resolver Table
```

![Challenge 2](../../screenshots/praktikum8/02-exported-activity.png)

### Analisis

Komponen yang diekspor tanpa validasi dapat memperluas attack surface aplikasi dan memungkinkan aplikasi lain menjalankan Activity secara langsung.

---

# Challenge 3 - Traffic Inspection Menggunakan Burp Suite

## Tujuan

Menganalisis komunikasi jaringan aplikasi Android.

### Tools

- Burp Suite
- Android Emulator

### Langkah Praktikum

Aktifkan listener:

```text
127.0.0.1:8080
```

Konfigurasikan proxy emulator menuju komputer.

Install certificate melalui:

```text
http://burp
```

Lakukan proses login atau request API.

![Challenge 3](../../screenshots/praktikum8/03-burp-traffic.png)

### Analisis

Traffic inspection memungkinkan investigator mengamati request maupun response yang dikirim aplikasi, termasuk Authorization Header, Session Token, API Endpoint, maupun informasi sensitif lainnya.

---

# Challenge 4 - Runtime Hooking Menggunakan Frida

## Tujuan

Memahami konsep runtime instrumentation.

### Tools

- Frida
- Android Emulator

### Script

```javascript
Java.perform(function () {

var Login = Java.use(
"com.insecureshop.LoginActivity"
);

Login.validate.implementation = function(a,b){

console.log("USER:",a);
console.log("PASS:",b);

return true;

};

});
```

Menjalankan script:

```bash
frida -U -f com.insecureshop -l hook.js
```

![Challenge 4](../../screenshots/praktikum8/04-frida-hook.png)

### Analisis

Hooking memungkinkan investigator memonitor parameter maupun nilai yang diproses aplikasi secara langsung ketika aplikasi sedang berjalan tanpa perlu mengubah source code.

---

# Challenge 5 - Membuat Vulnerability Report

## Tujuan

Menyusun dokumentasi hasil security assessment.

### Contoh Reporting

| Field | Keterangan |
|--------|------------|
| Title | Hardcoded Credential |
| Severity | Medium |
| Description | Credential ditemukan pada source code APK |
| Impact | Credential Disclosure |
| Evidence | Source Code Review |
| Recommendation | Gunakan Server-side Authentication |

![Challenge 5](../../screenshots/praktikum8/05-vulnerability-report.png)

### Analisis

Penyusunan vulnerability report merupakan tahap akhir dalam security assessment agar setiap temuan dapat dikomunikasikan dengan jelas kepada developer maupun stakeholder.

---

# Ringkasan Hasil Challenge

| Challenge | Hasil |
|------------|-------|
| Hardcoded Credential | Credential berhasil diidentifikasi |
| Exported Activity | Activity yang dapat diakses berhasil ditemukan |
| Traffic Inspection | HTTP Request berhasil diamati |
| Runtime Hooking | Method berhasil dimonitor menggunakan Frida |
| Vulnerability Report | Laporan security finding berhasil disusun |

---

# Security Finding

Selama mini challenge diperoleh beberapa contoh vulnerability yang umum ditemukan pada aplikasi Android, antara lain:

- Hardcoded Credential
- Exported Activity
- Sensitive Data Exposure
- Insecure HTTP Communication
- Runtime Information Disclosure
- Weak Authentication
- Client-side Secret

Temuan tersebut digunakan sebagai contoh untuk memahami proses Mobile Application Security Assessment.

---

# Kesimpulan

Pada praktikum ini dilakukan simulasi **Mobile Security Assessment** terhadap aplikasi laboratorium **InsecureShop** dengan menggabungkan teknik static analysis, dynamic analysis, traffic inspection, serta runtime instrumentation.

Melalui mini challenge ini, peserta tidak hanya memahami cara menggunakan berbagai tools keamanan Android, tetapi juga belajar menghubungkan hasil analisis menjadi sebuah **security finding** yang terdokumentasi dengan baik. Pendekatan ini memberikan gambaran mengenai alur kerja seorang Mobile Security Assessor dalam melakukan investigasi dan menyusun vulnerability report secara profesional.
