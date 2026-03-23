# Mitigation & Hardening Plan

## 1. Mitigasi Segera (Patching)
* **Upgrade OJS:** Segera update ke versi minimal **3.3.0-12** untuk menutup celah XSS dan SSRF kritis.
* **Update PHP:** Pastikan menggunakan versi PHP yang mendapatkan security patch terbaru.

## 2. Hardening Server & Aplikasi
* **File Permissions:** Pindahkan direktori `/files/` ke luar Document Root Apache agar file yang diunggah tidak dapat dieksekusi langsung via URL.
* **Input Validation:** Terapkan pembersihan (sanitasi) ketat pada seluruh input user menggunakan HTML Purifier untuk mencegah XSS.
* **Database Security:** Gunakan user database dengan hak akses terbatas (bukan root) dan hanya berikan izin pada database `ojs_db`.

## 3. Monitoring
* Aktifkan logging detail pada Apache dan audit log pada OJS untuk mendeteksi percobaan serangan secara dini.
