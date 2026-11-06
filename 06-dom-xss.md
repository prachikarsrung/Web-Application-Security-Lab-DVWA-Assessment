# Finding 06: DOM-Based Cross-Site Scripting (XSS)

| Field | Details |
|---|---|
| Category | DOM-based XSS (CWE-79), OWASP A03:2021 Injection |
| Location | `/vulnerabilities/xss_d/` (`default` URL parameter) |
| Environment | DVWA, security level **Low**, `127.0.0.1:4280` |
| Tools | Chrome, Burp Suite Community, browser DevTools |
| Risk | **Medium** |

## Summary
The language selector reads the `default` value from the URL and writes it into the page using client-side JavaScript. Because the value is inserted into the DOM without sanitization, a script placed in the URL executes in the victim's browser. Unlike reflected XSS, the payload is handled by the browser, so the server's response itself may look harmless.

## Steps to reproduce
1. Set DVWA Security to **Low** and open **XSS (DOM)**.
2. Pick a language and click **Select**. Note that the URL changes to `...xss_d/?default=English`.
3. Edit the URL so the parameter holds a payload:
   `...xss_d/?default=<script>alert(1)</script>`
4. Load the URL. The browser runs the script and shows an alert.
5. Open DevTools (F12) and check the page source and the script that writes the `default` value into the page. Note that the data flows from the URL straight into the DOM.
6. In Burp Suite > Proxy > HTTP history, compare the request and response to confirm the server did not need to reflect the payload for it to run.

![DOM XSS payload in URL](../screenshots/xss-dom-url.png)
![DOM XSS alert executed](../screenshots/xss-dom-alert.png)

> TODO: add both screenshots after you test it yourself.

## Why it works
- **Source:** the `default` value comes from the URL (`document.location`).
- **Sink:** client-side code writes it into the page (for example with `document.write`) without encoding it.

The vulnerability lives in the browser-side code, not in a server response, which is why server-side filters alone often miss it.

## Impact
An attacker can send a victim a crafted link. When opened, the script runs in the site's context and can steal session data, perform actions as the user, or redirect to a malicious page.


## Low vs. Impossible
At the **Impossible** level the payload no longer executes. Review DVWA's source (View Source button) and note how the value is handled differently.

> TODO: re-run the payload at Impossible, add a screenshot, and record what you observe in the source.

## Optional extension: Medium level
At Medium, DVWA filters `<script>` tags. Try an alternative that does not use a script tag (for example, breaking out of the `<select>` element and using an image error event), then explain why blacklist filtering is weaker than allow-listing.
