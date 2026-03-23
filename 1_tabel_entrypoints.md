## A. Authentication & Session Entry Points (OJS)

| Endpoint | Method | Deskripsi | Risiko | Status | Evidence |
|----------|--------|-----------|--------|--------|----------|
| `/index.php/index/login` | GET/POST | Form login utama | Brute force, credential stuffing | 200 | Percobaan login gagal berturut-turut selalu mengembalikan status 200 dengan waktu respons konsisten; Tidak ada lockout, CAPTCHA, atau rate limiting; Credential dikirim via HTTP plaintext |
| `/index.php/index/login/signIn` | POST | Proses autentikasi | SQL Injection, auth bypass | 200 | Payload `admin'--` dan `admin' OR '1'='1` tidak memunculkan error SQL, response tetap "Invalid username or password"; Input tampak ter-sanitize oleh server |
| `/index.php/index/login/lostPassword` | POST | Reset password | Account takeover | 200 | Tidak ada rate limiting atau CAPTCHA sehingga endpoint bisa diabuse untuk email flooding |
| `/index.php/index/register` | GET/POST | Registrasi user baru | Mass registration, spam | 404 | Percobaan registrasi berturut-turut selalu mengembalikan 404 Not Found |