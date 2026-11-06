# Web Application Security Lab: DVWA Assessment

Hands-on vulnerability assessment of **DVWA (Damn Vulnerable Web Application)** running in an isolated local environment. For each vulnerability I reproduced the attack, captured the traffic, identified the root cause, and documented the fix by comparing DVWA's **Low** and **Impossible** security levels.

> **Disclaimer:** All testing was performed on an intentionally vulnerable application running on my own machine (`127.0.0.1`). No real systems, websites, or third-party data were tested. Do not use these techniques against systems you do not own or have written permission to test.

---

## Demo video

[Watch the walkthrough](https://drive.google.com/file/d/1Y_eZa2PNa8IEcJ1WzLYIeMwhiv-APHMS/view?usp=drive_link)  (about 4.5 minutes: SQL injection, XSS, command injection, file upload, Burp Suite and ZAP)

---

## Lab environment

| Item | Details |
|---|---|
| Target | DVWA, running locally at `http://127.0.0.1:4280` |
| Security levels tested | Low, then Impossible (for fix comparison) |
| Interception proxy | Burp Suite Community Edition |
| Automated scanner | OWASP ZAP 2.17.0 |
| Browser | Google Chrome |
| Network | Local only, no external exposure |

---

## Findings summary

| # | Vulnerability | OWASP Top 10 (2021) | Security level | Risk | Write-up |
|---|---|---|---|---|---|
| 1 | SQL Injection | A03: Injection | Low | High | [findings/01-sql-injection.md](https://github.com/prachikarsrung/Web-Application-Security-Lab-DVWA-Assessment/blob/main/01-sql-injection.md) |
| 2 | Reflected XSS | A03: Injection | Low | Medium | [findings/02-reflected-xss.md](findings/02-reflected-xss.md) |
| 3 | Stored XSS | A03: Injection | Low | High | [findings/03-stored-xss.md](findings/03-stored-xss.md) |
| 4 | DOM-based XSS | A03: Injection | Low | Medium | [findings/04-dom-xss.md](findings/04-dom-xss.md) |
| 5 | Command Injection | A03: Injection | Low | Critical | [findings/05-command-injection.md](findings/05-command-injection.md) |
| 6 | Unrestricted File Upload | A04: Insecure Design | Low | High | [findings/06-file-upload.md](findings/06-file-upload.md) |

> Risk ratings are my own assessment for a deliberately vulnerable lab app and are included to demonstrate the rating process.

---

## What I did

### 1. Manual exploitation (Low security)
- **SQL injection:** a tautology payload (`1' OR '1'='1' #`) in the User ID field returned every user record instead of one.
- **Reflected XSS:** a script payload in the name field executed in the browser and triggered an alert.
- **Stored XSS:** a script payload saved through the guestbook persisted and ran for every visitor to the page.
- **DOM XSS:** manipulated the client-side language parameter handling.
- **Command injection:** appending a second command to the ping field (`127.0.0.1; whoami`) made the server execute it and return the output.
- **File upload:** tested how the upload handler validates file type and content.

### 2. Traffic analysis with Burp Suite
Routed browser traffic through Burp Suite's proxy and reviewed requests and responses in HTTP history to see exactly which parameters were injectable and how the server responded.

### 3. Automated scanning with OWASP ZAP
Ran an automated scan against my own local DVWA instance and reviewed the alerts. Report: [zap/zap-dvwa-report.html](https://github.com/prachikarsrung/Web-Application-Security-Lab-DVWA-Assessment/blob/main/2026-11-06-ZAP-Report-.html.pdf)

### 4. Fix comparison (Low vs. Impossible)
Repeated the same payloads at the **Impossible** level to confirm they fail, then reviewed what the secure code does differently.

---

## Root causes and fixes

| Vulnerability | Root cause (Low level) | Fix (what Impossible level does) |
|---|---|---|
| SQL Injection | User input concatenated directly into the SQL query | Prepared statements with parameterized queries; input validation |
| Reflected / Stored / DOM XSS | User input written back to the page without encoding | Output encoding (e.g. `htmlspecialchars`), input validation, Content Security Policy |
| Command Injection | User input passed straight to a shell command | Strict allow-list validation (numeric IP octets only); avoid shell calls |
| File Upload | Weak or missing validation of file type and content | Allow-list of extensions, MIME and content checks, re-encoding images, storing files outside the web root |

> Update this table with exactly what you observed in the Impossible-level source code.

---

## Repository structure

```
.
├── README.md
├── findings/          # One write-up per vulnerability
├── screenshots/       # Redacted evidence images
├── burp/              # Burp Suite request/response notes
└── zap/               # ZAP report for the local DVWA scan
```

---

## Skills demonstrated

- Web application vulnerability assessment (OWASP Top 10)
- Manual exploitation and verification of SQLi, XSS, command injection, file upload
- Intercepting and analysing HTTP traffic with Burp Suite
- Automated scanning with OWASP ZAP
- Root-cause analysis and remediation guidance
- Security findings documentation and risk rating

---

## Tools

Burp Suite Community Edition, OWASP ZAP, DVWA, Google Chrome

---

## About

**Prachi Kasrung**: BSc IT student, aspiring Security / SOC Analyst
LinkedIn: ADD_YOUR_LINKEDIN_URL | GitHub: ADD_YOUR_GITHUB_URL
