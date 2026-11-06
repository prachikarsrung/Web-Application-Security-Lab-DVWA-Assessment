# Finding 03: Stored Cross-Site Scripting (XSS)

| Field | Details |
|---|---|
| Category | Cross-Site Scripting (CWE-79), OWASP A03:2021 Injection |
| Location | `/vulnerabilities/xss_s/` (guestbook Message field) |
| Environment | DVWA, security level **Low**, `127.0.0.1:4280` |
| Tools | Chrome, Burp Suite Community |
| Risk | **High** |

## Summary
Guestbook messages are stored and later displayed without encoding. A script saved in a message runs for every visitor who loads the page, with no crafted link needed.

## Steps to reproduce
1. Open **XSS (Stored)** at Low security.
2. Enter a name (e.g. `test`) and the message: `<script>alert('Stored XSS')</script>`
3. Click **Sign Guestbook**.
4. Reload the page. The script executes again on every load.
5. In Burp Suite, review the `POST /vulnerabilities/xss_s/` request carrying the payload.

![Stored XSS guestbook](../screenshots/xss-stored.png)

## Why it works
Input is saved to the database and written back into the page unencoded, so the payload persists and executes for everyone.

## Impact
Higher than reflected XSS because it affects every user who views the page and needs no social engineering. It can be used for session theft, defacement, or malware delivery.

## Remediation
- Encode output on display.
- Sanitize and validate input on save.
- Use a strict Content Security Policy.
- Use `HttpOnly` cookies.

## Low vs. Impossible
At **Impossible**, DVWA sanitizes and encodes both input and output, and adds an anti-CSRF token.

> TODO: re-run at Impossible, add a screenshot, and confirm against the source. Remember to click **Clear Guestbook** between tests.
