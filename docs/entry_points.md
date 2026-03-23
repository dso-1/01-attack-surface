# Daftar Entry Points OJS

Berikut adalah rincian titik masuk yang diidentifikasi melalui pengintaian (reconnaissance).

### A. Autentikasi & Sesi
- **A1:** `/index.php/index/login` (GET/POST) - Form login utama.
- **A2:** `/index.php/index/login/signIn` (POST) - Proses autentikasi.
- **A3:** `/index.php/index/login/lostPassword` (POST) - Reset kata sandi.

### B. Unggah File (Risiko Tinggi)
- **B1:** `/index.php/$journal/submission/wizard` (POST) - Submit naskah.
- **B2:** `/index.php/$journal/api/v1/submissions` (POST) - API submit naskah.
- **B3:** Unggah plugin `.tar.gz` melalui Plugin Manager (Risiko RCE).

### C. Input Pengguna (XSS Vector)
- **C1:** `/index.php/$journal/search` (GET) - Kolom pencarian.
- **C2:** Form profil pengguna (POST) - Edit data profil.

### D. REST API & Admin Panel
- **D1:** `/api/v1/users` - Manajemen pengguna (Risiko IDOR).
- **E1:** `/index.php/index/admin` - Dashboard administrator situs.
