# Threat Model Matrix — STRIDE

**Target Sistem :** Open Journal Systems (OJS) 3.3.0.8  
**IP Target :** 10.34.100.178  
**Web Server :** Apache 2.4.58 (Ubuntu)  
**Tanggal Pengujian :** 23 Maret 2026  
**Tim :** kelompok1@devsecops

---

## Penjelasan Kategori STRIDE

| Kategori | Kepanjangan | Deskripsi |
|----------|-------------|-----------|
| **S** | Spoofing | Penyerang berpura-pura menjadi entitas lain yang sah |
| **T** | Tampering | Modifikasi data atau proses secara tidak sah |
| **R** | Repudiation | Penyangkalan atas tindakan yang telah dilakukan |
| **I** | Information Disclosure | Kebocoran informasi kepada pihak yang tidak berwenang |
| **D** | Denial of Service | Gangguan ketersediaan layanan |
| **E** | Elevation of Privilege | Peningkatan hak akses secara tidak sah |

---

## Skala Risiko

| Tingkat | Keterangan |
|---------|------------|
| **Kritis** | Eksploitasi langsung memungkinkan kompromi penuh sistem |
| **Tinggi** | Eksploitasi berdampak besar, memerlukan mitigasi segera |
| **Sedang** | Eksploitasi terbatas, berdampak moderat |
| **Rendah** | Dampak minimal, mitigasi bersifat preventif |

---

## STRIDE Threat Model Matrix

### S — Spoofing (Pemalsuan Identitas)

| ID | Komponen / Aset | Ancaman | Vektor Serangan | Kondisi Saat Ini | Tingkat Risiko |
|----|----------------|---------|-----------------|-----------------|:--------------:|
| S-01 | Login Endpoint `/index/login` | Credential Stuffing — penyerang menggunakan kredensial bocor dari sumber lain untuk login | POST request dengan wordlist via Burp Intruder mode Pitchfork | Tidak ada rate limiting, tidak ada CAPTCHA, tidak ada account lockout | **Kritis** |
| S-02 | Login Endpoint `/index/login` | Brute Force Attack — penyerang mencoba kombinasi password secara sistematis | POST request otomatis dengan wordlist password umum | CSRF token statis dapat digunakan ulang; tidak ada perlindungan percobaan login berulang | **Kritis** |
| S-03 | Session Cookie (OJSSID) | Session Hijacking — penyerang mencuri cookie sesi pengguna aktif | Sniffing jaringan (HTTP tanpa HTTPS) atau XSS | Cookie tidak memiliki flag `HttpOnly` dan `Secure` | **Tinggi** |
| S-04 | Endpoint Reset Password `/lostPassword` | Account Takeover via Password Reset — penyerang memicu reset password tanpa pembatasan | POST request berulang ke endpoint reset | Tidak ada rate limiting pada endpoint reset password | **Sedang** |
| S-05 | Registrasi User `/index/register` | Mass Account Registration — penyerang membuat banyak akun palsu | Script otomatis POST ke endpoint registrasi | Tidak ada CAPTCHA atau mekanisme anti-automation | **Sedang** |

---

### T — Tampering (Manipulasi Data)

| ID | Komponen / Aset | Ancaman | Vektor Serangan | Kondisi Saat Ini | Tingkat Risiko |
|----|----------------|---------|-----------------|-----------------|:--------------:|
| T-01 | File Upload `/submission/wizard` | Malicious File Upload — mengunggah file PHP berbahaya berkedok ekstensi lain | Upload `shell.php.jpg`, `shell.php.pdf`, `shell.phtml` via form submission | Tidak ada validasi MIME type; double extension tidak terdeteksi; `.phtml` lolos filter | **Kritis** |
| T-02 | Database OJS | SQL Injection pada Login — manipulasi query autentikasi | Payload `' OR 1=1 --` dan `' OR '1'='1` pada parameter `username` | Sistem menggunakan prepared statement — tidak rentan (diuji, tidak berhasil) | **Rendah** |
| T-03 | Metadata Artikel (Title, Abstract) | Stored XSS — menyuntikkan skrip berbahaya ke metadata artikel | Payload `<script>alert('XSS')</script>` pada field Title dan Abstract | Sistem melakukan HTML encoding pada output — tidak rentan | **Rendah** |
| T-04 | Profil Pengguna (Bio Statement) | Stored XSS pada Bio — skrip tersimpan di profil dan dieksekusi pengunjung | Payload `<img src=x onerror=alert('XSS')>` pada field Bio | Input difilter dan output di-encode — tidak rentan | **Rendah** |
| T-05 | Plugin Manager | Malicious Plugin Upload — instalasi plugin berbahaya berisi kode PHP | Upload file `.tar.gz` berisi web shell via Plugin Gallery | Fitur upload plugin dinonaktifkan pada environment saat ini | **Kritis** *(jika aktif)* |
| T-06 | Admin Site Settings | Manipulasi URL Konfigurasi (SSRF) — memaksa server melakukan request ke host internal | Input `http://127.0.0.1` atau `http://evil.com` pada field URL konfigurasi | URL hanya disimpan sebagai konfigurasi, tidak diproses server-side | **Rendah** |

---

### R — Repudiation (Penyangkalan)

| ID | Komponen / Aset | Ancaman | Vektor Serangan | Kondisi Saat Ini | Tingkat Risiko |
|----|----------------|---------|-----------------|-----------------|:--------------:|
| R-01 | Log File Server | Penghapusan / Manipulasi Log — penyerang menghapus jejak aktivitas setelah kompromi | Akses langsung ke sistem file jika RCE berhasil via malicious upload | Jika `shell.php.jpg` dieksekusi, penyerang dapat memodifikasi log | **Tinggi** |
| R-02 | Login Endpoint | Tidak Ada Audit Trail Login Gagal — percobaan login berulang tidak tercatat secara memadai | Brute force dilakukan tanpa aktivasi alarm atau notifikasi admin | Tidak ada mekanisme peringatan otomatis pada percobaan login massal | **Sedang** |
| R-03 | Submission Artikel | Penyangkalan Kepemilikan Naskah — penyerang memanipulasi metadata authorship | Akses admin yang didapat via brute force dapat mengubah metadata artikel | Setelah login sebagai admin, semua metadata dapat dimodifikasi | **Tinggi** |
| R-04 | Registrasi & Aktivitas User | Akun Anonim — penyerang mendaftar dengan identitas palsu | Mass registration dengan data fiktif via script otomatis | Tidak ada verifikasi email wajib yang ketat sebelum akun aktif | **Sedang** |

---

### I — Information Disclosure (Kebocoran Informasi)

| ID | Komponen / Aset | Ancaman | Vektor Serangan | Kondisi Saat Ini | Tingkat Risiko |
|----|----------------|---------|-----------------|-----------------|:--------------:|
| I-01 | REST API `/api/v1/users` | User Enumeration & Data Leakage — daftar user beserta email, nama, dan role terekspos tanpa autentikasi | GET request langsung ke endpoint tanpa header autentikasi | Endpoint dapat diakses publik; mengembalikan email, fullname, username, role | **Tinggi** |
| I-02 | HTTP Response Header | Server Fingerprinting — informasi versi server terekspos di header | `curl -I http://10.34.100.178` | Header mengekspos: `Apache/2.4.58 (Ubuntu)`, `OJS/PKP 3.3.0.8` | **Sedang** |
| I-03 | WhatWeb Fingerprinting | Technology Stack Disclosure — framework, CMS, dan versi teridentifikasi | `whatweb http://10.34.100.178` | Mengekspos: Bootstrap, jQuery, OJS 3.3.0.8, Apache 2.4.58, Ubuntu Linux | **Sedang** |
| I-04 | Directory Enumeration | Direktori Sensitif Terekspos — path aplikasi dapat dienumerasi | Gobuster dengan wordlist `common.txt` | Ditemukan: `/api/`, `/cache/`, `/classes/`, `/plugins/`, `/tools/` (status 301) | **Sedang** |
| I-05 | File konfigurasi `config.inc.php` | Konfigurasi & Kredensial Database Bocor — file config dapat diakses langsung | Path traversal atau direct file access jika RCE berhasil | Belum diuji secara langsung — berpotensi kritis jika dapat diakses | **Kritis** *(potensi)* |
| I-06 | Pencarian Artikel `/JPK/search` | Reflected XSS (Information Leakage via Script) | Payload `<script>alert(1)</script>` pada parameter `query` | Sistem melakukan output encoding — payload ditampilkan sebagai teks | **Rendah** |

---

### D — Denial of Service (Gangguan Ketersediaan)

| ID | Komponen / Aset | Ancaman | Vektor Serangan | Kondisi Saat Ini | Tingkat Risiko |
|----|----------------|---------|-----------------|-----------------|:--------------:|
| D-01 | Login Endpoint `/index/login` | Login Flood — pengiriman request login massal menyebabkan beban server meningkat | Brute force massal tanpa pembatasan via Burp Intruder | Tidak ada rate limiting — server menerima semua request tanpa pembatasan | **Tinggi** |
| D-02 | Endpoint Reset Password `/lostPassword` | Email Flood — spam pengiriman email reset password ke target | POST request berulang ke endpoint dengan email valid | Tidak ada rate limiting — memungkinkan pengiriman email tanpa batas | **Sedang** |
| D-03 | Endpoint Registrasi `/index/register` | Registration Flood — pendaftaran akun massal menyebabkan beban database | Script otomatis kirim POST registrasi berulang | Tidak ada CAPTCHA atau throttling — database dapat dibanjiri entri baru | **Sedang** |
| D-04 | File Upload `/submission/wizard` | Storage Exhaustion — upload file berukuran besar secara massal menghabiskan kapasitas disk | Upload file besar berulang via form submission | Tidak ada pembatasan ukuran file yang ketat atau kuota penyimpanan | **Sedang** |
| D-05 | Web Server (Apache 2.4.58) | HTTP Request Flooding — server kewalahan oleh request bersamaan | Tool seperti `ab` atau `hping3` ke port 80 | Tidak ada WAF atau DDoS protection yang teridentifikasi | **Tinggi** |

---

### E — Elevation of Privilege (Eskalasi Hak Akses)

| ID | Komponen / Aset | Ancaman | Vektor Serangan | Kondisi Saat Ini | Tingkat Risiko |
|----|----------------|---------|-----------------|-----------------|:--------------:|
| E-01 | Akun Admin OJS | Privilege via Credential Compromise — akun admin diambil alih lalu digunakan untuk aksi administratif | Brute force berhasil mendapatkan `admin / Admin@123` | Akun admin aktif dengan password lemah; tidak ada MFA | **Kritis** |
| E-02 | File Upload + RCE | Privilege via Remote Code Execution — eksekusi kode PHP di server memberi akses setara web server | Akses `http://target/uploads/shell.php.jpg?cmd=whoami` | File berbahaya berhasil diupload; jika diakses, RCE memungkinkan eksekusi perintah OS | **Kritis** |
| E-03 | Admin Panel `/index/admin` | Unauthorized Admin Access — pengguna biasa mencoba akses fitur admin | Akses langsung ke URL admin tanpa login | Sistem melakukan redirect ke halaman login — tidak rentan | **Rendah** |
| E-04 | REST API `/api/v1/submissions` | IDOR — akses data submission milik pengguna lain dengan manipulasi ID | GET `/api/v1/submissions/{id}` dengan ID berbeda | Semua request tanpa autentikasi mengembalikan 403 Forbidden — tidak rentan | **Rendah** |
| E-05 | Manajemen User Admin `/admin/users` | Privilege Escalation via Role Manipulation — mengubah role pengguna biasa menjadi admin | Akses fitur Users & Roles sebagai non-admin | RBAC diterapkan dengan benar — user biasa tidak dapat mengakses fitur admin | **Rendah** |
| E-06 | Plugin Manager | RCE via Plugin — instalasi plugin berbahaya memberi akses setara web server | Upload plugin `.tar.gz` berisi web shell | Fitur upload plugin dinonaktifkan — tidak dapat dieksploitasi saat ini | **Kritis** *(jika aktif)* |

---

## Rekap STRIDE — Distribusi Ancaman

| Kategori STRIDE | Total Ancaman | Kritis | Tinggi | Sedang | Rendah |
|----------------|:-------------:|:------:|:------:|:------:|:------:|
| S — Spoofing | 5 | 2 | 1 | 2 | 0 |
| T — Tampering | 6 | 2 | 0 | 0 | 4 |
| R — Repudiation | 4 | 0 | 2 | 2 | 0 |
| I — Information Disclosure | 6 | 1 | 1 | 3 | 1 |
| D — Denial of Service | 5 | 0 | 2 | 3 | 0 |
| E — Elevation of Privilege | 6 | 3 | 0 | 0 | 3 |
| **Total** | **32** | **8** | **6** | **10** | **8** |

---

## Ancaman Prioritas Tinggi (Kritis & Tinggi)

| ID | Kategori | Ancaman | Tingkat Risiko |
|----|----------|---------|:--------------:|
| S-01 | Spoofing | Credential Stuffing via Burp Intruder | **Kritis** |
| S-02 | Spoofing | Brute Force Attack — CSRF token statis | **Kritis** |
| T-01 | Tampering | Malicious File Upload — bypass extension | **Kritis** |
| E-01 | Elevation of Privilege | Admin account takeover via brute force | **Kritis** |
| E-02 | Elevation of Privilege | RCE via uploaded web shell | **Kritis** |
| I-05 | Information Disclosure | File konfigurasi & database credentials (potensi) | **Kritis** |
| S-03 | Spoofing | Session Hijacking via cookie tanpa flag aman | **Tinggi** |
| R-01 | Repudiation | Penghapusan log setelah RCE | **Tinggi** |
| R-03 | Repudiation | Manipulasi metadata authorship artikel | **Tinggi** |
| I-01 | Information Disclosure | User enumeration via `/api/v1/users` | **Tinggi** |
| D-01 | Denial of Service | Login flood tanpa rate limiting | **Tinggi** |
| D-05 | Denial of Service | HTTP request flooding ke web server | **Tinggi** |

---

*Dokumen ini merupakan bagian dari laporan Penetration Testing Pertemuan 2.*  
*Seluruh pengujian dilakukan pada lingkungan yang telah diizinkan (authorized testing environment).*
