# Finding 04: OS Command Injection

| Field | Details |
|---|---|
| Category | OS Command Injection (CWE-78), OWASP A03:2021 Injection |
| Location | `/vulnerabilities/exec/` (IP address field) |
| Environment | DVWA, security level **Low**, local Windows host |
| Tools | Chrome |
| Risk | **Critical** |

## Summary
The "Ping a device" feature passes user input to a system command. Adding a second command after the IP address makes the server run it and return the output.

## Steps to reproduce
1. Open **Command Injection** at Low security.
2. Enter a normal value: `127.0.0.1` and observe the ping output.
3. Enter: `127.0.0.1;whoami`
4. Submit. The ping output is followed by the account name the web server is running as.

![Command injection result](../screenshots/command-injection.png)

> **Redact the username in this screenshot before publishing.**

## Why it works
The application builds a shell command by concatenating the input (conceptually `ping <input>`). Characters like `;` and `&&` let an attacker append further commands, which the shell executes.

## Impact
Remote code execution as the web server account. An attacker could read files, create users, install malware, or pivot to other systems on the network.

## Remediation
- Avoid calling the shell; use language-native functions or libraries.
- **Allow-list validation:** accept only a valid IPv4 address (four numeric octets).
- If a command is unavoidable, pass arguments safely, never through string concatenation.
- Run the web server with least privilege.

## Low vs. Impossible
At **Impossible**, DVWA splits the input into four octets, checks each is numeric, and rebuilds the IP, so injected characters are rejected. It also adds an anti-CSRF token.

> TODO: re-run at Impossible and add a screenshot of the rejected input.
