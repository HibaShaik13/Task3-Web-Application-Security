# Task3-Web-Application-Security
# Task 3 — Web Application Security

**Intern:** Shaik Hiba Tharunnum | **Offer Letter ID:** APSPL2645050
**Organization:** ApexPlanet Software Pvt. Ltd. | **Domain:** Cybersecurity & Ethical Hacking

## Overview

Hands-on identification and exploitation of common OWASP Top 10 web application
vulnerabilities, performed against Damn Vulnerable Web Application (DVWA) hosted
on Metasploitable2 (`192.168.56.102`) from a Kali Linux attacker machine
(`192.168.56.101`).

## What Was Done

- **SQL Injection** — authentication-bypass payload (`' OR '1'='1`) returning all
  users; UNION-based extraction of usernames + MD5 password hashes; compared
  Low-security (raw concatenation) vs. High-security (escaping + `is_numeric()`,
  not true prepared statements) source code.
- **Stored XSS** — persistent `<script>` payload in the guestbook, confirmed
  firing on reload.
- **Reflected XSS** — `<script>` payload in the Name field, reflected via the
  URL query parameter.
- **CSRF** — admin password changed via a crafted GET URL with no anti-CSRF
  token; High-security source reviewed (uses current-password re-verification,
  not a token).
- **File Inclusion** — LFI via path traversal (`../../../etc/passwd`); RFI
  attempt blocked by `allow_url_include=Off` at the server config level.
- **Burp Suite** — intercepted and edited a live DVWA login request, forwarded
  it, then configured Intruder payload positions on `username`/`password`.
- **Security Headers** — inspected via `curl -I` (since the target is a private
  IP); found `X-Frame-Options`, `Content-Security-Policy`,
  `X-Content-Type-Options`, and `Strict-Transport-Security` all missing.

## Key Findings (Severity)

| Vulnerability | Severity |
|---|---|
| SQL Injection | Critical |
| Local File Inclusion | Critical |
| Stored XSS | High |
| Reflected XSS | High |
| CSRF | High |
| Remote File Inclusion | Medium |
| Missing Security Headers | Medium |

Full details, screenshots, and recommended fixes: see
[`Task3_Web_Application_Security_Report.pdf`](./Task3_Web_Application_Security_Report.pdf)
and [`task3-findings.md`](./task3-findings.md).

## Files in This Folder

- `Task3_Web_Application_Security_Report.docx` / `.pdf` — full report
- `task3-findings.md` — raw technical notes (payloads, hashes, header output)
- `README.md` — this file

## Tools Used

DVWA, Burp Suite Community Edition, curl, Firefox DevTools, Kali Linux.
