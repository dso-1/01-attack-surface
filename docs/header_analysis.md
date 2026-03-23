# Header Analysis

## Endpoint: /

### Finding 1: Server Version Disclosure

* Header: `Server: Apache/2.4.58 (Ubuntu)`
* Risk: Information Disclosure
* Description: The server reveals exact version and OS, which can help attackers identify known vulnerabilities.
* Severity: Medium

---

### Finding 2: Session Cookie Missing Security Flags

* Header: `Set-Cookie: OJSSID=cf1b2jkjo4jq0qdnsgnrqmmifd`
* Issue:

  * No `HttpOnly` flag
  * No `Secure` flag
* Risk: Session Hijacking
* Description: Cookies can be accessed via JavaScript or transmitted over HTTP.
* Severity: High

---

### Finding 3: Missing Security Headers

* Missing Headers:

  * `X-Frame-Options`
  * `X-Content-Type-Options`
  * `Content-Security-Policy`
* Risk: Clickjacking, MIME sniffing, XSS
* Severity: Medium

---

### Finding 4: Cache-Control Misconfiguration

* Header: `Cache-Control: public`
* Risk: Sensitive data may be cached by intermediaries
* Severity: Low

---

### Finding 5: Directory Listing Enabled (API Exposure)

* Endpoint: `/api/` and `/api/v1/`

* Evidence: Browser shows "Index of /api" and lists internal directories

* Exposed Paths:

  * /api/v1/users/
  * /api/v1/submissions/
  * /api/v1/contexts/
  * /api/v1/stats/
  * /api/v1/site/
  * /api/v1/temporaryFiles/
  * etc.

* Risk: Information Disclosure / Attack Surface Expansion

* Description:
  Directory listing is enabled on the web server, allowing attackers to enumerate all available API endpoints and internal structure.

* Impact:

  * Easier API discovery
  * Targeted attacks on sensitive endpoints (users, submissions, uploads)
  * Potential access to unintended resources

* Severity: High

* Recommendation:
  Disable directory listing in Apache:

  ```
  Options -Indexes
  ```
