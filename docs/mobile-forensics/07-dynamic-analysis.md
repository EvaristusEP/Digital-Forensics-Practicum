# Praktikum 7 - Dynamic Analysis pada Aplikasi Android (InsecureShop)

## Pendahuluan

Dynamic analysis merupakan proses analisis aplikasi Android dengan menjalankan aplikasi secara langsung pada emulator atau perangkat Android. Berbeda dengan static analysis, metode ini memungkinkan investigator mengamati perilaku aplikasi saat runtime, menganalisis komunikasi jaringan, memonitor log sistem, serta melakukan inspeksi terhadap data yang diproses oleh aplikasi.

Pada praktikum ini dilakukan dynamic analysis terhadap aplikasi **InsecureShop** menggunakan Android Emulator, Android Debug Bridge (ADB), Burp Suite, dan Frida.

---

## Tujuan Praktikum

Setelah menyelesaikan praktikum ini diharapkan mampu:

- Menjalankan aplikasi Android pada emulator.
- Melakukan runtime inspection menggunakan ADB.
- Mengidentifikasi exported activity.
- Menguji keamanan Deep Link.
- Menganalisis Logcat.
- Mengidentifikasi insecure local storage.
- Melakukan traffic inspection menggunakan Burp Suite.
- Memahami dasar runtime instrumentation menggunakan Frida.

---

## Target Aplikasi

```text
InsecureShop.apk
```

---

## Environment

| Komponen | Keterangan |
|----------|------------|
| Emulator | Android Studio Emulator |
| Android Version | Android 11 |
| Target APK | InsecureShop.apk |
| Tools | ADB, Burp Suite, Frida |

---

# 1. Membuat Android Emulator

## Tujuan

Mempersiapkan perangkat virtual untuk menjalankan aplikasi Android.

Konfigurasi yang digunakan:

- Pixel Device
- Android 11
- Google APIs Image

![Android Emulator](../../screenshots/praktikum7/01-create-emulator.png)

### Analisis

Android Emulator digunakan sebagai lingkungan pengujian sehingga seluruh aktivitas aplikasi dapat diamati tanpa menggunakan perangkat fisik.

---

# 2. Menjalankan Emulator

## Tujuan

Memastikan emulator berhasil dikenali oleh Android Debug Bridge.

### Command

```bash
adb devices
```

Expected Output

```text
List of devices attached
emulator-5554 device
```

![ADB Devices](../../screenshots/praktikum7/02-adb-devices.png)

### Analisis

Output menunjukkan bahwa emulator telah aktif dan siap digunakan untuk proses dynamic analysis.

---

# 3. Install APK ke Emulator

## Tujuan

Menginstal aplikasi InsecureShop ke dalam emulator.

### Command

```bash
adb install InsecureShop.apk
```

Expected Output

```text
Success
```

![Install APK](../../screenshots/praktikum7/03-install-apk.png)

### Analisis

Keberhasilan instalasi menunjukkan bahwa aplikasi siap dijalankan untuk proses analisis lebih lanjut.

---

# 4. Menjalankan Aplikasi

## Tujuan

Menjalankan aplikasi menggunakan Android Debug Bridge.

### Command

Menampilkan package:

```bash
adb shell pm list packages | grep insecure
```

Menjalankan aplikasi:

```bash
adb shell monkey -p com.insecureshop 1
```

![Run Application](../../screenshots/praktikum7/04-run-application.png)

### Analisis

ADB memungkinkan investigator menjalankan aplikasi langsung melalui terminal sehingga mempermudah proses otomasi maupun pengujian.

---

# 5. Enumerasi Activity

## Tujuan

Mengidentifikasi activity yang tersedia pada aplikasi.

### Command

```bash
adb shell dumpsys package com.insecureshop
```

Cari bagian:

```text
Activity Resolver Table
```

![Activity Enumeration](../../screenshots/praktikum7/05-activity-enumeration.png)

### Analisis

Informasi ini membantu investigator menemukan exported activity maupun deep link handler yang dapat diakses oleh aplikasi lain.

---

# 6. Deep Link Testing

## Tujuan

Menguji implementasi Deep Link pada aplikasi.

### Command

```bash
adb shell am start \
-a android.intent.action.VIEW \
-d "insecureshop://com.insecureshop/web?url=https://example.com"
```

![Deep Link Testing](../../screenshots/praktikum7/06-deeplink-testing.png)

### Analisis

Pengujian dilakukan untuk mengetahui apakah aplikasi melakukan validasi terhadap URL yang diterima melalui Deep Link. Konfigurasi yang tidak aman dapat menyebabkan unauthorized navigation maupun intent abuse.

---

# 7. Analisis Logcat

## Tujuan

Melihat log aplikasi saat runtime.

### Command

```bash
adb logcat
```

Filter:

```bash
adb logcat | grep insecure
```

![Logcat Analysis](../../screenshots/praktikum7/07-logcat.png)

### Analisis

Logcat dapat menampilkan berbagai informasi penting seperti stack trace, debug information, token, maupun pesan error yang dapat dimanfaatkan selama proses investigasi.

---

# 8. SharedPreferences Extraction

## Tujuan

Memeriksa data yang disimpan secara lokal oleh aplikasi.

### Command

```bash
adb shell
```

```bash
su
```

```bash
cd /data/data/com.insecureshop/shared_prefs
```

![SharedPreferences](../../screenshots/praktikum7/08-sharedpreferences.png)

### Analisis

Investigator perlu memastikan bahwa credential, session token, maupun informasi sensitif lainnya tidak disimpan dalam bentuk plaintext.

---

# 9. Traffic Inspection Menggunakan Burp Suite

## Tujuan

Menganalisis komunikasi HTTP/HTTPS aplikasi.

Konfigurasi Proxy:

```text
127.0.0.1:8080
```

Install certificate melalui:

```text
http://burp
```

![Burp Suite](../../screenshots/praktikum7/09-burp-suite.png)

### Analisis

Burp Suite digunakan untuk menangkap, memonitor, dan menganalisis seluruh komunikasi jaringan yang dilakukan aplikasi.

---

# 10. Analisis HTTP Request

## Tujuan

Mengidentifikasi informasi penting pada request aplikasi.

Fokus analisis:

- Authorization Header
- Session Token
- API Endpoint
- Plaintext Credential

![HTTP Request](../../screenshots/praktikum7/10-http-request.png)

### Analisis

Header HTTP dapat mengungkap mekanisme autentikasi maupun token yang digunakan aplikasi sehingga menjadi bagian penting dalam mobile security assessment.

---

# 11. Runtime Instrumentation Menggunakan Frida

## Tujuan

Mempersiapkan Frida untuk proses hooking aplikasi.

### Install

```bash
pip install frida-tools
```

List Process

```bash
frida-ps -U
```

![Frida Process](../../screenshots/praktikum7/11-frida-process.png)

### Analisis

Frida memungkinkan investigator melakukan inspeksi terhadap aplikasi ketika sedang berjalan tanpa harus memodifikasi APK.

---

# 12. Konsep Dasar Frida

## Tujuan

Memahami komponen utama Frida.

Komponen penting:

- Java.perform()
- Java.use()
- implementation override

![Frida Concept](../../screenshots/praktikum7/12-frida-concept.png)

### Analisis

Frida digunakan untuk memonitor runtime value, melakukan method inspection, maupun mengubah perilaku aplikasi selama proses eksekusi.

---

# 13. Hooking Method

## Tujuan

Melakukan hooking terhadap method LoginActivity.

### Contoh Script

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

![Hooking Method](../../screenshots/praktikum7/13-hooking-method.png)

### Analisis

Hooking memungkinkan investigator melihat parameter maupun nilai yang diproses aplikasi saat runtime tanpa perlu memodifikasi source code.

---

# 14. Security Finding Reporting

## Tujuan

Mendokumentasikan hasil temuan selama proses assessment.

| Field | Keterangan |
|-------|------------|
| Title | Hardcoded Credential |
| Severity | Medium |
| Description | Credential tersimpan pada APK |
| Impact | Unauthorized Access |
| Evidence | Source Code Review |
| Recommendation | Remove Client-side Credential |

![Security Report](../../screenshots/praktikum7/14-security-report.png)

### Analisis

Setiap vulnerability perlu didokumentasikan secara sistematis agar memudahkan proses mitigasi maupun penyusunan laporan keamanan.

---

# 15. Troubleshooting

Selama praktikum beberapa kendala yang mungkin ditemui antara lain:

### Emulator Tidak Terdeteksi

```bash
adb kill-server
adb start-server
```

### APK Gagal Diinstal

```bash
adb install -r InsecureShop.apk
```

### Burp Tidak Menangkap Traffic

Periksa:

- Proxy Emulator
- Firewall
- Certificate Burp

### Frida Tidak Mendeteksi Device

```bash
adb devices
```

![Troubleshooting](../../screenshots/praktikum7/15-troubleshooting.png)

### Analisis

Proses troubleshooting membantu memastikan seluruh tools berjalan dengan baik sehingga dynamic analysis dapat dilakukan tanpa hambatan.

---

# Security Finding yang Dipelajari

Selama praktikum diperkenalkan beberapa potensi kerentanan pada aplikasi Android, antara lain:

- Exported Activity
- Deep Link Exposure
- Debug Information Disclosure
- Token Disclosure
- Insecure Local Storage
- Plaintext Credential
- Insecure HTTP Communication
- Runtime Hooking

Temuan tersebut menjadi dasar dalam proses mobile application security assessment.

---

# Kesimpulan

Pada praktikum ini dilakukan **dynamic analysis** terhadap aplikasi Android menggunakan Android Emulator, ADB, Burp Suite, dan Frida. Investigator berhasil menjalankan aplikasi, melakukan enumerasi activity, menguji deep link, menganalisis Logcat, memeriksa SharedPreferences, melakukan traffic inspection, hingga memahami konsep runtime instrumentation.

Melalui dynamic analysis, perilaku aplikasi dapat diamati secara langsung saat runtime sehingga berbagai kerentanan yang tidak terlihat pada static analysis dapat diidentifikasi. Pendekatan ini melengkapi proses mobile application security assessment secara menyeluruh.
