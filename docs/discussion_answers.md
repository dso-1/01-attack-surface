# Jawaban Pertanyaan Diskusi

## Pertemuan 1
1. **Pentingnya Scope:** Untuk membatasi pengujian agar tidak merusak sistem di luar target dan memastikan aspek legalitas terjaga.
2. **Risiko Tanpa RoE:** Dapat menyebabkan downtime yang tidak direncanakan, kerusakan data permanen, atau masalah hukum jika sistem produksi terganggu.

## Pertemuan 2
1. **Attack Surface vs Vector:** *Surface* adalah titik masuk (misal: Form Login), sedangkan *Vector* adalah metode serangannya (misal: Brute Force).
2. **Risiko Endpoint Upload:** Memiliki risiko lebih tinggi karena berinteraksi langsung dengan sistem penyimpanan (file system) dan dapat memicu RCE.
3. **Titik SQL Injection:** Paling besar terjadi pada fungsi `UserDAO::getByUsername` saat memproses input username di form login jika tidak menggunakan prepared statements.
4. **Informasi Sensitif di Header:** Versi Web Server (Apache), versi PHP, dan versi spesifik OJS (misal: 3.3.0-8).
