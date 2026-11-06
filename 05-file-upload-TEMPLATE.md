# Finding 05: Unrestricted File Upload 

| Field | Details |
|---|---|
| Category | Unrestricted File Upload (CWE-434), OWASP A04:2021 Insecure Design |
| Location | `/vulnerabilities/upload/` |
| Environment | DVWA, security level **Low**, `127.0.0.1:4280` |
| Risk | TODO |

## Status
Your current screenshot only shows a normal PNG uploading successfully. That is expected behavior, not a vulnerability. To make this a real finding you must show the application accepting a file it should reject.

## What to test (safe, local lab only)
1. Upload a harmless **text file renamed to .php or .txt** and see whether the app accepts it.
2. Check whether the app validates by file extension, MIME type (Content-Type), or content.
3. In Burp Suite, intercept the upload request and note the `Content-Type` and filename fields.
4. Observe where the file is stored (the success message shows the path).

## Notes
- Your screenshot also showed "The PHP module GD is not installed". That affects the higher security levels (image re-processing), so enable GD in your PHP config if you want to test Medium/High/Impossible properly.
- Keep any test file harmless. Do not upload working web shells.

## Write-up (fill in after testing)
- **Steps to reproduce:**
- **Why it works:**
- **Impact:** an attacker who can upload and execute server-side code may take control of the server.
- **Remediation:** allow-list extensions, validate content (not just MIME type), re-encode images, rename files, store uploads outside the web root, disable script execution in the upload directory.
- **Low vs. Impossible:**
