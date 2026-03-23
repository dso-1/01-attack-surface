## A. Authentication & Session Entry Points (OJS)

| Endpoint | Method | Deskripsi | Risiko | Status | Evidence |
|----------|--------|-----------|--------|--------|----------|
| `/index.php/index/login` | GET | Form login utama | Info Disclosure (OJS 3.3.0.8 & Apache 2.4.58 exposed), Cookie tanpa HttpOnly/Secure/SameSite, Missing security headers | 200 OK | Cookie `OJSSID` tanpa flag proteksi; CSRF token tersedia; Version terekspos via meta generator & Server header |
| `/index.php/index/login/signIn` | POST | Proses autentikasi | Brute Force (no rate limiting/lockout), Credential Stuffing, Plaintext HTTP | 200 OK | Error msg generik: "Invalid username or password" (anti enum); Username reflected di form; Tidak ada throttling |
| `/index.php/index/login/lostPassword` | POST | Reset password | Email Flooding (no rate limit), No CAPTCHA | 200 OK | Email invalid: form ulang tanpa feedback; Tidak ada throttle/CAPTCHA |
| `/index.php/index/register` | GET/POST | Registrasi user baru | Endpoint disabled | 404 | GET: tidak ada form; POST: `404 Not Found`; Registrasi dinonaktifkan |