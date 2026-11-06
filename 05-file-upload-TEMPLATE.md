# Finding 05: Unrestricted File Upload 

| Field | Details |
|---|---|
| Category | Unrestricted File Upload (CWE-434), OWASP A04:2021 Insecure Design |
| Location | `/vulnerabilities/upload/` |
| Environment | DVWA, security level **Low**, `127.0.0.1:4280` |

## Status
 current screenshot only shows a normal PNG uploading successfully. That is expected behavior, not a vulnerability.

## What to test (safe, local lab only)
1. Upload a harmless **text file renamed to .php or .txt** and see whether the app accepts it.
2. Check whether the app validates by file extension, MIME type (Content-Type), or content.
3. In Burp Suite, intercept the upload request and note the `Content-Type` and filename fields.
4. Observe where the file is stored (the success message shows the path).


## Write-up 
- **Steps to reproduce:**
- **Why it works:**
- **Impact:** an attacker who can upload and execute server-side code may take control of the server.
- **Remediation:** allow-list extensions, validate content (not just MIME type), re-encode images, rename files, store uploads outside the web root, disable script execution in the upload directory.
- **Low vs. Impossible:**
