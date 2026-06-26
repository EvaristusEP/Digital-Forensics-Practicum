# Praktikum 6 - Static Analysis pada Aplikasi Android (InsecureShop)

## Pendahuluan

Static analysis merupakan proses analisis aplikasi Android tanpa menjalankan aplikasi tersebut. Melalui metode ini, investigator dapat memahami struktur internal aplikasi, business logic, serta mengidentifikasi berbagai potensi kerentanan keamanan hanya dengan melakukan analisis terhadap file APK.

Pada praktikum ini dilakukan analisis statis menggunakan aplikasi **InsecureShop** sebagai target pembelajaran. Beberapa tools yang digunakan antara lain **JADX**, **APKTool**, dan **MobSF**.

---

## Tujuan Praktikum

Setelah menyelesaikan praktikum ini diharapkan mampu:

- Memahami struktur file APK Android.
- Melakukan decompile aplikasi Android.
- Menganalisis AndroidManifest.xml.
- Mengidentifikasi hardcoded credential.
- Menganalisis konfigurasi WebView.
- Mengidentifikasi exported component.
- Melakukan analisis SharedPreferences.
- Memahami dasar Smali code.
- Melakukan automated security analysis menggunakan MobSF.

---

## Target Aplikasi

```text
InsecureShop.apk
```

---

## Environment

| Komponen | Keterangan |
|----------|------------|
| Target APK | InsecureShop.apk |
| Tools | JADX, APKTool, MobSF |
| Sistem Operasi | Windows / Linux |

---

# 1. APK Acquisition

## Tujuan

Memperoleh file APK yang akan dianalisis.

File yang digunakan:

```text
InsecureShop.apk
```

![APK Acquisition](../../screenshots/praktikum6/01-apk-acquisition.png)

### Analisis

Tahap pertama adalah memperoleh file APK dari repository resmi agar proses analisis dilakukan terhadap aplikasi yang sesuai dengan kebutuhan praktikum.

---

# 2. Memahami Struktur APK

## Tujuan

Mengenal struktur dasar file APK Android.

Langkah:

Ubah nama file:

```text
InsecureShop.apk
```

menjadi

```text
InsecureShop.zip
```

Kemudian lakukan ekstraksi.

![APK Structure](../../screenshots/praktikum6/02-apk-structure.png)

### Analisis

APK merupakan arsip ZIP yang berisi seluruh komponen aplikasi Android, mulai dari source bytecode, resource, library, hingga informasi konfigurasi aplikasi.

---

# 3. Analisis Struktur Internal APK

## Tujuan

Mengidentifikasi komponen utama penyusun APK.

| File | Fungsi |
|------|--------|
| AndroidManifest.xml | Konfigurasi aplikasi |
| classes.dex | Dalvik Bytecode |
| resources.arsc | Resource aplikasi |
| assets | File tambahan |
| lib | Native Library |
| META-INF | Signature aplikasi |

![APK Contents](../../screenshots/praktikum6/03-apk-contents.png)

### Analisis

Setiap komponen memiliki fungsi yang berbeda dan menjadi sumber informasi penting selama proses reverse engineering.

---

# 4. Decompile APK Menggunakan JADX

## Tujuan

Mengubah file DEX menjadi source code Java agar lebih mudah dipahami.

### Command

```bash
jadx-gui InsecureShop.apk
```

![JADX](../../screenshots/praktikum6/04-jadx.png)

### Analisis

JADX mempermudah investigator dalam memahami alur program serta melakukan code review terhadap aplikasi Android.

---

# 5. Review Source Code

## Tujuan

Melakukan penelusuran terhadap package utama aplikasi.

Package utama:

```text
com.insecureshop
```

Komponen yang dianalisis:

- Activity
- Service
- Utility
- Network Class

![Source Review](../../screenshots/praktikum6/05-source-review.png)

### Analisis

Source code review membantu investigator memahami business logic aplikasi sekaligus menemukan implementasi yang berpotensi menimbulkan vulnerability.

---

# 6. Security Finding Menggunakan Fitur Search

## Tujuan

Mencari indikasi kerentanan melalui keyword tertentu.

Keyword yang digunakan:

```text
password
secret
token
apikey
firebase
sharedpreferences
webview
loadUrl
```

![Keyword Search](../../screenshots/praktikum6/06-keyword-search.png)

### Analisis

Pencarian keyword mempercepat proses identifikasi informasi sensitif maupun konfigurasi yang berpotensi menimbulkan risiko keamanan.

---

# 7. Hardcoded Credential Analysis

## Tujuan

Mengidentifikasi credential yang ditulis langsung pada source code.

Contoh:

```java
if(username.equals("admin") &&
password.equals("SuperSecure123"))
```

![Hardcoded Credential](../../screenshots/praktikum6/07-hardcoded-credential.png)

### Analisis

Hardcoded credential memungkinkan attacker memperoleh username maupun password melalui proses reverse engineering sehingga berpotensi menyebabkan authentication bypass.

---

# 8. AndroidManifest.xml Review

## Tujuan

Menganalisis konfigurasi komponen aplikasi.

Fokus pencarian:

```xml
android:exported="true"
```

Komponen yang diperiksa:

- Activity
- Service
- Broadcast Receiver

![Manifest Review](../../screenshots/praktikum6/08-manifest-review.png)

### Analisis

Komponen yang diekspor tanpa pembatasan dapat diakses oleh aplikasi lain sehingga meningkatkan risiko penyalahgunaan.

---

# 9. Deep Link Analysis

## Tujuan

Mengidentifikasi deep link yang digunakan aplikasi.

Contoh:

```xml
<intent-filter>
<data android:scheme="insecureshop"/>
```

![Deep Link](../../screenshots/praktikum6/09-deeplink.png)

### Analisis

Deep link yang tidak divalidasi dapat menyebabkan unauthorized navigation maupun intent abuse oleh aplikasi lain.

---

# 10. WebView Analysis

## Tujuan

Menganalisis konfigurasi WebView.

Contoh:

```java
webView.loadUrl(url);
```

Periksa penggunaan:

```java
setJavaScriptEnabled(true)
```

![WebView](../../screenshots/praktikum6/10-webview.png)

### Analisis

Konfigurasi WebView yang tidak aman berpotensi menyebabkan JavaScript Injection, phishing, maupun pemuatan URL berbahaya.

---

# 11. SharedPreferences Analysis

## Tujuan

Mengidentifikasi penyimpanan data sensitif.

Keyword:

```java
getSharedPreferences()
```

Lokasi penyimpanan:

```text
/data/data/package/shared_prefs/
```

![SharedPreferences](../../screenshots/praktikum6/11-sharedpreferences.png)

### Analisis

Investigator perlu memastikan bahwa credential maupun token tidak disimpan dalam bentuk plaintext pada SharedPreferences.

---

# 12. Decode APK Menggunakan APKTool

## Tujuan

Melakukan decoding resource aplikasi Android.

### Command

```bash
apktool d InsecureShop.apk
```

![APKTool Decode](../../screenshots/praktikum6/12-apktool-decode.png)

### Analisis

APKTool menghasilkan struktur project yang lebih lengkap sehingga memudahkan analisis resource maupun file konfigurasi aplikasi.

---

# 13. Analisis Smali Code

## Tujuan

Memahami representasi low-level dari Dalvik Bytecode.

Contoh:

```smali
const-string v0, "admin"
```

Interpretasi:

```java
String value = "admin";
```

![Smali Analysis](../../screenshots/praktikum6/13-smali.png)

### Analisis

Smali digunakan ketika source Java tidak tersedia sehingga investigator tetap dapat memahami logika program pada level bytecode.

---

# 14. Automated Analysis Menggunakan MobSF

## Tujuan

Melakukan analisis keamanan aplikasi secara otomatis.

### Docker

```bash
docker run -it -p 8000:8000 opensecurity/mobile-security-framework-mobsf
```

MobSF dapat mendeteksi:

- Hardcoded Secret
- Weak Cryptography
- Insecure Permission
- Insecure Storage

![MobSF](../../screenshots/praktikum6/14-mobsf.png)

### Analisis

MobSF mempercepat proses security assessment dengan menghasilkan laporan otomatis mengenai berbagai potensi vulnerability pada aplikasi Android.

---

# Security Finding yang Dipelajari

Selama praktikum diperkenalkan beberapa jenis kerentanan keamanan pada aplikasi Android, antara lain:

- Hardcoded Credential
- Exported Component
- Insecure WebView
- SharedPreferences Exposure
- Deep Link Exposure
- Weak Permission
- Insecure Storage
- Hardcoded Secret

Temuan-temuan tersebut menjadi dasar dalam melakukan mobile application security assessment secara lebih komprehensif.

---

# Kesimpulan

Pada praktikum ini dilakukan proses **static analysis** terhadap aplikasi Android menggunakan berbagai tools seperti JADX, APKTool, dan MobSF. Investigator berhasil memahami struktur APK, melakukan decompile aplikasi, menganalisis AndroidManifest.xml, source code, WebView, SharedPreferences, hingga Smali code.

Melalui proses static analysis, berbagai potensi kerentanan keamanan dapat diidentifikasi tanpa harus menjalankan aplikasi. Pendekatan ini menjadi langkah awal yang sangat penting sebelum melanjutkan ke proses **dynamic analysis** pada tahap berikutnya.
