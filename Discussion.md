# Discussions

# 1. Perbedaan Attack Surface vs Attack Vector (Studi Kasus OJS)

### 🔵 Attack Surface
Attack surface adalah **seluruh area, komponen, atau entry point pada sistem** yang dapat diakses oleh attacker dan berpotensi menjadi titik serangan.

➡️ Fokus: *"DI MANA bisa diserang?"*

Contoh:
- Endpoint API
- Form login
- File upload
- Admin panel
- Port & service terbuka

---

### 🔴 Attack Vector
Attack vector adalah **cara atau teknik spesifik yang digunakan attacker untuk mengeksploitasi attack surface tersebut**.

➡️ Fokus: *"BAGAIMANA cara menyerangnya?"*

Contoh:
- Brute force
- SQL Injection
- XSS
- File upload bypass
- Remote Code Execution (RCE)

---

### 2. Perbedaan Utama

| Aspek | Attack Surface | Attack Vector |
|------|--------------|--------------|
| Definisi | Titik masuk / area yang bisa diserang | Metode atau teknik serangan |
| Fokus | Lokasi | Cara |
| Sifat | Pasif (eksis di sistem) | Aktif (aksi attacker) |
| Contoh | `/index/login` | Brute force login |
| Analogi | Pintu rumah | Cara maling membobol pintu |

---

### 3. Contoh pada OJS

### ✅ Contoh 1 — Login Endpoint

- **Attack Surface:**  
  `/index/login`

- **Attack Vector:**  
  - Brute force  
  - Credential stuffing  

📌 Evidence:
- Tidak ada rate limiting  
- Tidak ada CAPTCHA  
- CSRF token statis  

➡️ Dampak:
- Account takeover (admin)

---

### ✅ Contoh 2 — File Upload (Submission Wizard)

- **Attack Surface:**  
  `/submission/wizard`

- **Attack Vector:**  
  - Upload file berbahaya (`shell.php.jpg`, `shell.phtml`)  
  - Bypass validasi ekstensi  
  - Remote Code Execution (RCE)

📌 Evidence:
- Double extension lolos  
- Tidak ada validasi MIME type  
- File dapat dieksekusi di server  

➡️ Dampak:
- Server takeover

---

### ✅ Contoh 3 — REST API Users

- **Attack Surface:**  
  `/api/v1/users`

- **Attack Vector:**  
  - Information disclosure  
  - User enumeration  

📌 Evidence:
- Endpoint tanpa autentikasi  
- Data sensitif (email, role) terekspos  

➡️ Dampak:
- Kebocoran data pengguna

---

### ✅ Contoh 4 — Session & Cookie

- **Attack Surface:**  
  Session cookie (`OJSSID`)

- **Attack Vector:**  
  - Session hijacking  
  - Cookie theft  

📌 Evidence:
- Tidak ada flag `HttpOnly` dan `Secure`  

➡️ Dampak:
- Pengambilalihan sesi user

---

## 4. Insight dari OJS

Attack surface pada OJS meliputi:
- Authentication (login, register)
- File upload
- REST API
- Admin panel
- Database & file system

Attack vector paling berbahaya:
1. Brute force login → ambil admin
2. Malicious file upload → RCE
3. API exposure → data leakage

➡️ Semakin luas attack surface, semakin banyak kemungkinan attack vector.

---

### 5. Kesimpulan

- **Attack surface = tempat yang bisa diserang**
- **Attack vector = cara menyerangnya**

Contoh:
- `/submission/wizard` → attack surface  
- Upload `shell.php.jpg` → attack vector  

👉 Tanpa attack surface → attack vector tidak punya target  
👉 Tanpa attack vector → attack surface tidak tereksploitasi  

# 2. Mengapa Endpoint Upload File (B1–B4) Lebih Berisiko dibanding Endpoint GET?

## 1. Perbedaan Fundamental

### 🔴 Endpoint Upload (POST)
Endpoint upload file (B1–B4) digunakan untuk:
- Mengirim data ke server
- Menyimpan file ke file system
- Memproses input kompleks (file binary)

➡️ Contoh:
- `/submission/wizard` (B1)
- `/api/v1/submissions` (B2)
- `/admin/settings` (upload logo)

---

### 🟢 Endpoint GET (Read Only)
Endpoint GET hanya digunakan untuk:
- Mengambil data
- Menampilkan informasi
- Tidak mengubah state server

➡️ Contoh:
- `/api/v1/users`
- `/article/view`
- `/search`

---

## 2. Alasan Endpoint Upload Lebih Berisiko

### 🔥 1. Menulis ke Server (Write Operation)
Upload file = **menambahkan file ke server**

➡️ Risiko:
- File berbahaya tersimpan permanen
- Bisa diakses ulang oleh attacker

Contoh:
- Upload `shell.php.jpg` → disimpan di `/uploads/`

---

### 🔥 2. Potensi Remote Code Execution (RCE)

Jika file yang diupload bisa dieksekusi:

➡️ Attack vector:
- Upload web shell
- Akses via browser:


➡️ Dampak:
- Full server compromise

---

### 🔥 3. Bypass Validasi File

Endpoint upload sering memiliki kelemahan seperti:
- Tidak validasi MIME type
- Hanya cek ekstensi
- Tidak deteksi double extension

Contoh bypass:
- `shell.php.jpg`
- `shell.php.pdf`
- `shell.phtml`

➡️ Ini terbukti pada B1 (critical)

---

### 🔥 4. Interaksi dengan File System

Upload langsung berhubungan dengan:
- Directory (`/uploads/`)
- Permission file
- Execution context server

➡️ Risiko:
- Path traversal
- File overwrite
- Malware persistence

---

### 🔥 5. Attack Surface Lebih Kompleks

Upload melibatkan banyak layer:
1. Client (form upload)
2. Web server (Apache)
3. Application (PHP handler)
4. File system
5. Validasi (sering lemah)

➡️ Semakin kompleks → semakin banyak celah

---

### 🔥 6. Bisa Digunakan untuk Chain Attack

Upload file sering jadi pintu awal:

Flow serangan:
1. Upload shell  
2. Execute shell (RCE)  
3. Ambil database credentials  
4. Privilege escalation  

➡️ Ini terlihat pada alur DFD (RCE path)

---

### 🔥 7. Tidak Idempotent & Sulit Dikontrol

- Upload mengubah state sistem
- Tidak bisa "di-undo" dengan mudah
- Bisa disalahgunakan berkali-kali

---

## 3. Kenapa GET Lebih Aman?

### 🟢 1. Tidak Mengubah Data
GET hanya membaca → tidak menulis ke server

---

### 🟢 2. Tidak Eksekusi File
Tidak ada upload → tidak ada RCE langsung

---

### 🟢 3. Risiko Terbatas
Biasanya hanya:
- Information disclosure
- IDOR
- XSS (jika ada)

➡️ Dampaknya lebih kecil dibanding RCE

---

### 🟢 4. Lebih Mudah Dikontrol
- Bisa pakai caching
- Bisa dibatasi dengan auth
- Tidak menyentuh file system langsung

---

## 4. Studi Kasus OJS

### 🔴 B1 — `/submission/wizard` (CRITICAL)

Temuan:
- Upload `shell.php.jpg` berhasil
- Double extension tidak terdeteksi
- File bisa diakses publik

➡️ Dampak:
- Remote Code Execution (RCE)
- Server takeover

---

### 🟢 Endpoint GET — `/api/v1/users`

Temuan:
- Data user terekspos tanpa auth

➡️ Dampak:
- Information disclosure (MEDIUM)

---

## 5. Perbandingan Risiko

| Aspek | Upload Endpoint (POST) | GET Endpoint |
|------|----------------------|-------------|
| Operasi | Write (ubah sistem) | Read only |
| Risiko utama | RCE, malware, takeover | Data leak |
| Dampak | Sangat tinggi (critical) | Sedang |
| Kompleksitas | Tinggi | Rendah |
| Interaksi FS | Ya | Tidak |

---

## 6. Kesimpulan

Endpoint upload file memiliki risiko lebih tinggi karena:

- ✅ Menulis data ke server  
- ✅ Bisa digunakan untuk RCE  
- ✅ Validasi sering lemah  
- ✅ Berinteraksi langsung dengan file system  
- ✅ Bisa menjadi pintu masuk serangan lanjutan  

Sedangkan GET endpoint:
- ❌ Tidak mengubah sistem  
- ❌ Tidak bisa langsung eksekusi kode  
- ✅ Risiko lebih terbatas  

👉 Oleh karena itu, upload endpoint (B1–B4) dikategorikan **high–critical risk**, sedangkan GET umumnya **low–medium risk**.

# 3. Titik Potensi SQL Injection pada Alur Autentikasi OJS

## 1. Gambaran Alur Autentikasi OJS

Berdasarkan diagram dan DFD, alur login OJS adalah:

1. User mengirim request login  
   → `/index/login` (POST)

2. Data dikirim ke:
   → Router / Controller (MVC Layer)

3. Diproses oleh:
   → Authentication Logic

4. Diteruskan ke:
   → DAL (Data Access Layer)

5. Query ke:
   → Database (MySQL)

---

## 2. Titik Paling Berpotensi SQL Injection

### 🎯 Titik Kritis: Input Login → Query Database

**Lokasi:**
- Endpoint: `/index/login`
- Parameter:
  - `username`
  - `password`

➡️ Ini adalah titik paling rawan karena:
- Input langsung dari user
- Digunakan dalam query SQL
- Terhubung langsung ke database

---

## 3. Mekanisme SQL Injection (Secara Konseptual)

Jika tidak aman, query bisa seperti:

```sql
SELECT * FROM users 
WHERE username = '$username' 
AND password = '$password';

ika attacker memasukkan:

username: ' OR 1=1 --
password: bebas

Maka query menjadi:

SELECT * FROM users 
WHERE username = '' OR 1=1 -- 
AND password = '';

➡️ Hasil:

Kondisi selalu TRUE
Login bypass (authentication bypass)

4. Kenapa Titik Ini Paling Rawan?
🔥 1. Input Langsung dari User

Field login adalah input bebas tanpa kontrol penuh.

🔥 2. Digunakan dalam Query Autentikasi

Query login menentukan:

Valid / tidaknya user
Hak akses

➡️ Jika berhasil di-inject → langsung dapat akses

🔥 3. Tidak Terlihat oleh User (Blind Trust)

User tidak melihat proses query → mudah disalahgunakan.

🔥 4. Bagian dari Critical Asset

Berdasarkan CIA:

Data login admin = KRITIS


➡️ Jika bocor / bypass:

Full system compromise
5. Hasil Pengujian pada OJS

Berdasarkan testing:

Payload diuji:
' OR 1=1 --
' OR '1'='1
Hasil:
❌ Tidak berhasil (login gagal)

➡️ Artinya:

OJS menggunakan prepared statement
Query sudah diproteksi dari SQL Injection

➡️ Sesuai temuan:

SQL Injection → LOW risk (tidak rentan)

6. Titik Lain yang Berpotensi (Sekunder)

Selain login, titik lain yang mungkin rawan:

🔸 A. Registrasi (/index/register)
Input user baru
Bisa inject ke field username/email
🔸 B. Reset Password (/lostPassword)
Query berdasarkan email
Potensi enumeration / injection
🔸 C. REST API (/api/v1/users)
Parameter query/filter
Jika tidak disanitasi → raw SQL

➡️ Namun:

Tidak ditemukan eksploitasi SQLi pada titik-titik ini
7. Kesimpulan

Titik paling berpotensi SQL Injection pada OJS adalah:

🎯 Endpoint login /index/login pada saat input username & password diproses ke query database

Alasan:

Input langsung dari user
Digunakan dalam query autentikasi
Berhubungan dengan aset kritis (akun admin)

Namun pada implementasi OJS:

✅ Sudah menggunakan prepared statement
✅ Tidak rentan terhadap SQL Injection

👉 Jadi:

Secara teori: sangat rawan
Secara implementasi: aman (pada versi yang diuji)
8. Insight Penting

Walaupun SQL Injection tidak berhasil:

Sistem tetap memiliki celah lain:
Brute force login (KRITIS)
CSRF token statis
Weak password admin

➡️ Artinya:

Sistem bisa tetap dikompromi tanpa SQL Injection


---

Kalau mau sekalian beres:
👉 bilang aja **“lanjut nomor 4”** — nanti aku tutup sekalian jadi full laporan siap submit 🔥

# 4. Informasi Sensitif pada HTTP Response Headers OJS

## 1. Pengertian Singkat

HTTP response headers adalah metadata yang dikirim oleh server ke client (browser) saat merespon request.

➡️ Jika tidak dikonfigurasi dengan baik, header ini dapat membocorkan informasi sensitif yang membantu attacker melakukan reconnaissance.

---

## 2. Informasi Sensitif yang Berpotensi Bocor

Berikut minimal **3 informasi sensitif** yang ditemukan pada OJS:

---

### 🔴 1. Versi Web Server

Contoh header:
Server: Apache/2.4.58 (Ubuntu)


➡️ Informasi yang bocor:
- Jenis web server (Apache)
- Versi spesifik (2.4.58)
- OS yang digunakan (Ubuntu)

➡️ Risiko:
- Attacker bisa mencari CVE spesifik untuk versi tersebut
- Mempermudah exploit berbasis version-specific vulnerability

---

### 🔴 2. Teknologi / Framework Aplikasi

Contoh header:
X-Powered-By: PHP

atau dari fingerprint:
HTTP-Generator: OJS 3.3.0.8


➡️ Informasi yang bocor:
- Bahasa backend (PHP)
- Framework/CMS (OJS)
- Versi aplikasi (3.3.0.8)

➡️ Risiko:
- Attacker bisa mencari exploit khusus OJS
- Mempermudah targeted attack (misalnya plugin vulnerability)

---

### 🔴 3. Informasi Versi Aplikasi (OJS)

Contoh:
HTTP-Generator: OJS 3.3.0.8


➡️ Informasi yang bocor:
- Versi exact aplikasi

➡️ Risiko:
- Bisa dicocokkan dengan vulnerability database (CVE)
- Menentukan apakah target rentan terhadap exploit tertentu

---

### 🔴 4. Informasi Stack Teknologi Tambahan (Opsional)

Dari hasil fingerprinting (WhatWeb):

➡️ Informasi:
- Bootstrap
- jQuery
- HTML5

➡️ Risiko:
- Membantu attacker memahami struktur frontend
- Bisa digunakan untuk XSS atau client-side attack

---

## 3. Dampak Kebocoran Header

Kebocoran ini termasuk kategori:

➡️ **Information Disclosure (I-02, I-03)**  
:contentReference[oaicite:0]{index=0}

Dampaknya:
- Mempermudah reconnaissance
- Mengurangi effort attacker
- Mempercepat exploit discovery

---

## 4. Kenapa Ini Berbahaya?

Walaupun tidak langsung menyerang sistem:

- Memberikan “blueprint” sistem ke attacker
- Mengarah ke serangan yang lebih spesifik dan efektif
- Digunakan sebagai tahap awal (recon phase)

---

## 5. Contoh Skenario Serangan

1. Attacker melihat:
Apache/2.4.58 (Ubuntu)


2. Mencari:
- "Apache 2.4.58 vulnerability"

3. Menemukan exploit → langsung digunakan

➡️ Tanpa header ini, attacker harus menebak-nebak (lebih sulit)

---

## 6. Mitigasi

Untuk mengurangi risiko:

### ✅ 1. Sembunyikan Server Header
```apache
ServerTokens Prod
ServerSignature Off

✅ 2. Hilangkan X-Powered-By
Header unset X-Powered-By

3. Nonaktifkan HTTP-Generator
Konfigurasi OJS untuk tidak menampilkan versi
✅ 4. Gunakan Security Headers Tambahan
X-Content-Type-Options
X-Frame-Options
Content-Security-Policy
7. Kesimpulan

Minimal 3 informasi sensitif yang bocor melalui HTTP headers OJS:

Versi web server (Apache 2.4.58 + Ubuntu)
Teknologi backend (PHP, OJS)
Versi aplikasi (OJS 3.3.0.8)

➡️ Walaupun terlihat sepele, informasi ini sangat membantu attacker dalam:

Reconnaissance
Vulnerability mapping
Targeted exploitation
