# Risk Register & Asset Classification

## 1. Klasifikasi Aset (CIA Triad)
| Aset | Confidentiality | Integrity | Availability | Nilai Kritis |
|---|---|---|---|---|
| Kredensial Database | Tinggi | Tinggi | Rendah | **Kritis** |
| Naskah Belum Terbit | Tinggi | Tinggi | Sedang | **Kritis** |
| Konfigurasi (`config.inc.php`) | Tinggi | Tinggi | Sedang | **Kritis** |
| Akun Admin/Editor | Tinggi | Tinggi | Tinggi | **Kritis** |

## 2. Matriks Ancaman STRIDE
| ID | Kategori | Ancaman Spesifik | Dampak |
|---|---|---|---|
| R-01 | **S**poofing | Login dengan akun editor orang lain | Akses ilegal ke naskah |
| R-02 | **T**ampering | Modifikasi metadata artikel via API | Integritas data rusak |
| R-03 | **I**nformation Disclosure | Eksponasi path file/error verbose | Membantu serangan lanjutan |
| R-04 | **E**levation of Privilege | Author mengakses fitur Admin | Kontrol penuh sistem |
