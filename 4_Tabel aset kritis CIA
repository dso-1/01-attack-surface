# Tabel Aset Kritis — Klasifikasi CIA Triad

**Target Sistem :** Open Journal Systems (OJS) 3.3.0.8  
**IP Target :** 10.34.100.178  
**Web Server :** Apache 2.4.58 (Ubuntu)  
**Tanggal Pengujian :** 23 Maret 2026  
**Tim :** kelompok1@devsecops

---

## Skala Penilaian

| Tingkat | Keterangan |
|---------|------------|
| **Tinggi** | Dampak signifikan jika terjadi pelanggaran — data sensitif / layanan kritis |
| **Sedang** | Dampak moderat — dapat dimitigasi dengan prosedur tertentu |
| **Rendah** | Dampak minimal — mudah dipulihkan, tidak bersifat sensitif |

> **Nilai Kritis** ditentukan berdasarkan kombinasi tertinggi dari ketiga aspek CIA.  
> **Kritis** = minimal dua aspek bernilai Tinggi | **Tinggi** = satu aspek Tinggi | **Sedang** = semua aspek Sedang/Rendah

---

## Klasifikasi Aset OJS Berdasarkan CIA Triad

| Aset | Confidentiality | Integrity | Availability | Nilai Kritis |
|------|:--------------:|:---------:|:------------:|:------------:|
| Data login admin | Tinggi | Tinggi | Sedang | **Kritis** |
| Naskah unpublished | Tinggi | Tinggi | Sedang | **Kritis** |
| Data reviewer | Sedang | Tinggi | Sedang | **Tinggi** |
| Artikel published | Rendah | Tinggi | Tinggi | **Tinggi** |
| File konfigurasi (`config.inc.php`) | Tinggi | Tinggi | Sedang | **Kritis** |
| Database credentials | Tinggi | Tinggi | Rendah | **Kritis** |
| Log file server | Sedang | Sedang | Sedang | **Sedang** |
| Session cookie (OJSSID) | Tinggi | Tinggi | Sedang | **Kritis** |
| CSRF Token | Tinggi | Tinggi | Sedang | **Kritis** |
| Direktori upload (`/uploads/`) | Tinggi | Tinggi | Tinggi | **Kritis** |
| Data user API (`/api/v1/users`) | Tinggi | Sedang | Sedang | **Tinggi** |
| Plugin manager | Tinggi | Tinggi | Tinggi | **Kritis** |
| Metadata artikel (Title, Abstract) | Sedang | Tinggi | Tinggi | **Tinggi** |
| Profil pengguna (Bio Statement) | Sedang | Sedang | Rendah | **Sedang** |
| HTTP response header | Sedang | Rendah | Rendah | **Sedang** |
| Endpoint reset password | Tinggi | Sedang | Sedang | **Tinggi** |
| Admin dashboard (`/index/admin`) | Tinggi | Tinggi | Tinggi | **Kritis** |
| Port & service terbuka (22, 80) | Sedang | Sedang | Tinggi | **Sedang** |

---

## Rekap Berdasarkan Nilai Kritis

| Nilai Kritis | Jumlah Aset | Aset |
|:------------:|:-----------:|------|
| **Kritis** | 9 | Data login admin, Naskah unpublished, File konfigurasi, Database credentials, Session cookie, CSRF Token, Direktori upload, Plugin manager, Admin dashboard |
| **Tinggi** | 5 | Data reviewer, Artikel published, Data user API, Metadata artikel, Endpoint reset password |
| **Sedang** | 4 | Log file server, Profil pengguna, HTTP response header, Port & service terbuka |
| **Rendah** | 0 | — |

---

## Temuan Pengujian Berdasarkan Aset Kritis

| Aset | Nilai Kritis | Status | Temuan |
|------|:------------:|:------:|--------|
| Data login admin | **Kritis** | RENTAN | Kredensial valid ditemukan via brute force: `admin / Admin@123` |
| Session cookie (OJSSID) | **Kritis** | RENTAN | Terekspos di HTTP header tanpa flag `HttpOnly` / `Secure` |
| CSRF Token | **Kritis** | RENTAN | Token bersifat statis — dapat digunakan ulang tanpa regenerasi |
| Direktori upload (`/uploads/`) | **Kritis** | RENTAN | Menerima `shell.php.jpg`, `shell.php.pdf`, `shell.phtml` — potensi RCE |
| File konfigurasi (`config.inc.php`) | **Kritis** | BELUM DIUJI | Tidak diakses langsung — perlu pengujian path traversal lanjutan |
| Database credentials | **Kritis** | BELUM DIUJI | Berpotensi terekspos jika RCE dari upload berhasil dieksploitasi |
| Naskah unpublished | **Kritis** | AMAN | Terlindungi oleh kontrol akses RBAC |
| Plugin manager | **Kritis** | AMAN | Fitur upload plugin dinonaktifkan — tidak ditemukan kerentanan |
| Admin dashboard | **Kritis** | AMAN | Redirect ke login jika tidak terautentikasi |
| Data user API | **Tinggi** | RENTAN | Endpoint `/api/v1/users` dapat diakses tanpa autentikasi |
| Endpoint reset password | **Tinggi** | RENTAN | Tidak ada rate limiting — rentan spam & user enumeration |
| Data reviewer | **Tinggi** | AMAN | Terlindungi oleh RBAC — tidak ditemukan IDOR |
| Artikel published | **Tinggi** | AMAN | XSS payload tidak tereksekusi — output encoding berjalan |
| Metadata artikel | **Tinggi** | AMAN | Input disanitasi — tidak ada Stored/Reflected XSS |

---

## Prioritas Mitigasi

| Prioritas | Aset | Nilai Kritis | Rekomendasi |
|:---------:|------|:------------:|-------------|
| 1 | Data login admin | Kritis | Ganti password segera, terapkan kebijakan password kuat + account lockout |
| 2 | Direktori upload | Kritis | Validasi MIME type, whitelist extension, simpan di path non-executable |
| 3 | CSRF Token | Kritis | Aktifkan regenerasi token per-request |
| 4 | Session cookie | Kritis | Tambahkan flag `HttpOnly`, `Secure`, `SameSite=Strict` |
| 5 | Data user API | Tinggi | Wajibkan autentikasi pada endpoint `/api/v1/users` |
| 6 | Endpoint reset password | Tinggi | Implementasi rate limiting dan CAPTCHA |
| 7 | HTTP response header | Sedang | Sembunyikan versi server dengan `ServerTokens Prod` di konfigurasi Apache |

---

*Dokumen ini merupakan bagian dari laporan Penetration Testing Pertemuan 2.*  
*Seluruh pengujian dilakukan pada lingkungan yang telah diizinkan (authorized testing environment).*
