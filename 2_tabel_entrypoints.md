
## A. REST API Entry Points (OJS)

| Endpoint | Method | Deskripsi | Risiko | Status | Evidence |
|----------|--------|----------|--------|--------|----------|
| /api | GET | Root API endpoint | API Enumeration | 301 | Redirect ke /api/ |
| /api/v1/users | GET | Data user | Info Disclosure, Improper Error Handling | 500 | Internal Server Error |
| /api/v1/submissions | GET/POST | Data submission | IDOR | 301 | Redirect ke /api/v1/submissions/ |
| /api/v1/contexts | GET | Data jurnal | Info Disclosure | 301 | Redirect ke /api/v1/contexts/ |

## B. Admin Entry Points

| Endpoint | Method | Deskripsi | Risiko | Status | Evidence |
|----------|--------|----------|--------|--------|----------|
| /index.php/index/admin | GET | Dashboard admin | Unauthorized access | 302 | Redirect ke login |
| /index.php/index/admin/settings | POST | Pengaturan sistem | Misconfiguration | 302 | Redirect ke login |
| /index.php/index/admin/plugins | GET/POST | Manajemen plugin | RCE | 404 | Tidak ditemukan |
| /index.php/index/admin/users | GET/POST | Manajemen user | Privilege escalation | 404 | Tidak ditemukan |
