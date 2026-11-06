# Finding 01: SQL Injection

| Field | Details |
|---|---|
| Category | SQL Injection (CWE-89), OWASP A03:2021 Injection |
| Location | `/vulnerabilities/sqli/` (User ID parameter) |
| Environment | DVWA, security level **Low**, `127.0.0.1:4280` |
| Tools | Chrome, Burp Suite Community (HTTP history) |
| Risk | **High** |

## Summary
The User ID field passes input directly into a SQL query. A crafted value changes the query logic and returns every row in the users table instead of one record.

## Steps to reproduce
1. Set DVWA Security to **Low** and open **SQL Injection**.
2. Enter a normal value (`1`) and note that only one user is returned.
3. Enter the payload: `1' OR '1'='1' #`
4. Submit. The page returns all users: admin, Gordon Brown, Hack Me, Pablo Picasso, Bob Smith.
5. In Burp Suite > Proxy > HTTP history, review the `GET /vulnerabilities/sqli/?id=...` request and response.

![SQLi payload and result](../screenshots/sqli-result.png)

## Why it works
The application builds the query by concatenating user input, roughly:

```sql
SELECT first_name, last_name FROM users WHERE user_id = '$id';
```

The payload closes the quote, adds `OR '1'='1'` (always true), and `#` comments out the rest. The WHERE clause matches every row.

## Impact
An attacker could read data from the database, including other users' records and potentially password hashes, and in some configurations modify or delete data.

## Remediation
- Use **prepared statements / parameterized queries** so input is never treated as SQL.
- Validate input (e.g. require a numeric ID).
- Apply least privilege to the database account.
- Show generic error messages, never raw SQL errors.

## Low vs. Impossible
At the **Impossible** level DVWA uses a prepared statement with bound parameters and checks that the ID is numeric. The same payload returns nothing.

> TODO: re-run the payload at Impossible, add a screenshot, and confirm against the source (View Source button in DVWA).
