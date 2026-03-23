# Endpoint Verification

## A. REST API Entry Points

---

### /api

* Method: GET
* Status:

  * curl: 301 Moved Permanently
* Evidence:

  * Redirect ke `/api/`
* Observasi:

  * Endpoint membutuhkan trailing slash
  * Saat diakses via browser, muncul directory listing pada `/api/`
* Parameter: Tidak ada
* Risiko:
  API Enumeration akibat directory listing terbuka

---

### /api/v1/users (D1)

* Method: GET
* Status:

  * `/v1/users` → 404 Not Found
  * `/api/v1/users/` → 500 Internal Server Error
* Evidence:
  curl menunjukkan error server tanpa response body
* Parameter: Tidak terlihat
* Pengujian Autentikasi:

  * Response sama dengan dan tanpa header Authorization
* Observasi:

  * Endpoint ada tetapi tidak berfungsi dengan baik
  * Tidak ada penanganan error yang baik
* Risiko:

  * Improper Error Handling
  * Potensi Information Disclosure

---

### /api/v1/submissions

* Method: GET
* Status:

  * `/v1/submissions` → 404 Not Found
  * `/api/v1/submissions/` → directory listing (browser)
* Method: POST
* Status:

  * Tidak berhasil diuji / tidak ada response valid
* Parameter: Tidak terlihat
* Observasi:

  * Struktur endpoint terekspos melalui directory listing
* Risiko:

  * Potensi IDOR (berdasarkan SAST)
  * API Enumeration

---

### /api/v1/contexts

* Method: GET
* Status:

  * `/v1/contexts` → 404 Not Found
  * `/api/v1/contexts/` → directory listing (browser)
* Parameter: Tidak ada
* Observasi:

  * Struktur endpoint dapat diakses publik
* Risiko:
  Information Disclosure

---

## B. Admin Entry Points

---

### /index.php/index/admin (E1)

* Method: GET
* Status: 302 Found
* Evidence:

  * Redirect ke halaman login
* Observasi:

  * Autentikasi diterapkan dengan benar
* Parameter: Tidak ada
* Risiko:
  Rendah (sesuai ekspektasi)

---

### /index.php/index/admin/settings

* Method: POST
* Status: 302 Found
* Evidence:

  * Redirect ke login
* Observasi:

  * Membutuhkan autentikasi
* Risiko:
  Rendah

---

### /index.php/index/admin/plugins

* Method:

  * GET → 404 Not Found
  * POST → 404 Not Found
* Observasi:

  * Endpoint tidak tersedia
* Parameter: Tidak ada
* Risiko:
  Kemungkinan misconfiguration atau fitur dinonaktifkan

---

### /index.php/index/admin/users

* Method:

  * GET → 404 Not Found
  * POST → 404 Not Found
* Observasi:

  * Endpoint tidak tersedia
* Parameter: Tidak ada
* Risiko:
  Sama seperti di atas

---

## C. Endpoint Autentikasi & Sesi

---

### /index.php/index/login (A1)

* Method: GET/POST
* Status: 200 OK
* Parameter:

  * username
  * password
* Observasi:
  Form login tersedia dan dapat diakses

---

### /index.php/index/login/signIn (A2)

* Method: POST
* Parameter:

  * username
  * password
* Observasi:
  Endpoint untuk proses autentikasi

---

### /index.php/index/login/lostPassword (A3)

* Method: POST
* Parameter:

  * email
* Observasi:
  Fitur reset password tersedia

---

## D. Endpoint Upload File

---

### /index.php/$journal/submission/wizard (B1)

* Method: POST
* Observasi:
  Terdapat fitur upload file
* Risiko:
  Unrestricted File Upload

---

### /index.php/$journal/api/v1/submissions (B2)

* Method: POST
* Observasi:
  Endpoint API untuk submission
* Risiko:
  Potensi IDOR atau validasi tidak memadai

---

## E. Endpoint Input Pengguna

---

### /index.php/$journal/search (C1)

* Method: GET
* Parameter:

  * query pencarian
* Risiko:
  Reflected XSS

---

### Form Profil Pengguna (C2)

* Method: POST
* Parameter:

  * data profil
* Risiko:
  Stored XSS

---

## Verifikasi Autentikasi (REST API)

* Endpoint diuji: `/api/v1/users/`

  * Tanpa autentikasi → 500 Internal Server Error
  * Dengan autentikasi → response sama
* Hasil:
  Tidak ada perbedaan response
* Risiko:
  Tidak ada enforcement autentikasi

---

## Perbedaan antara SAST dan DAST

---

### Discrepancy 1: Perilaku Endpoint Users

* SAST:
  `/api/v1/users` mengembalikan data user
* DAST:

  * `/v1/users` → 404
  * `/api/v1/users/` → 500
* Dampak:
  Endpoint ada tetapi tidak berfungsi (misconfiguration)

---

### Discrepancy 2: Eksposur Struktur API

* SAST:
  Hanya endpoint tertentu yang didokumentasikan
* DAST:
  Directory listing menampilkan seluruh struktur API
* Dampak:
  Attack surface meningkat

---

### Discrepancy 3: Endpoint Admin

* SAST:
  Endpoint plugins dan users tersedia
* DAST:
  Menghasilkan 404 Not Found
* Dampak:
  Fitur tidak aktif atau deployment tidak lengkap

---

### Discrepancy 4: Perilaku API

* SAST:
  API mengembalikan response terstruktur
* DAST:
  Redirect dan directory listing ditemukan
* Dampak:
  Misconfiguration dan perilaku tidak konsisten

---