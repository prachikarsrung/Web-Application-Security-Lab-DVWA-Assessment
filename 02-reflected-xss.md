# Finding 02: Reflected Cross-Site Scripting (XSS)

| Field | Details |
|---|---|
| Category | Cross-Site Scripting (CWE-79), OWASP A03:2021 Injection |
| Location | `/vulnerabilities/xss_r/` (name parameter) |
| Environment | DVWA, security level **Low**, `127.0.0.1:4280` |
| Tools | Chrome, Burp Suite Community |
| Risk | **Medium** |

## Summary
The "What's your name?" input is reflected into the page without encoding. A script payload placed in the URL parameter executes in the victim's browser.

## Steps to reproduce
1. Open **XSS (Reflected)** at Low security.
2. Enter: `<script>alert(1)</script>`
3. Submit. The browser shows an alert box, and the URL contains the payload (`?name=<script>alert(1)</script>`).
4. Review the request and response in Burp Suite to confirm the payload appears unencoded in the HTML response.

![Payload entered](../screenshots/xss-reflected-input.png)
![Alert executed](../screenshots/xss-reflected-alert.png)

## Why it works
The server echoes the `name` value straight into the response HTML. The browser treats the injected `<script>` tag as part of the page and runs it.

## Impact
An attacker can send a victim a crafted link. When opened, the script runs in the context of the site and can steal session cookies, perform actions as the user, or redirect to a malicious page.

## Remediation
- **Encode output** for the context (HTML-encode, e.g. `htmlspecialchars`).
- Validate input against an allow-list where possible.
- Add a **Content Security Policy** that blocks inline scripts.
- Set cookies with `HttpOnly` and `SameSite`.

## Low vs. Impossible
At **Impossible**, DVWA encodes the output with `htmlspecialchars`, so the payload displays as text and does not execute.

> TODO: re-run at Impossible, add a screenshot, and confirm against the source.
