# User Guide – Raw HTTP Security Header Analyzer

## Goal
Verify that HTTP responses include modern security headers.

## Steps
1. Open `index.html`.
2. Paste raw response headers.
3. Click **Analyze**.
4. Review missing headers and add them to your server config.

## Notes
- HSTS must be served only over HTTPS.
- Tailor your Content-Security-Policy to your asset domains.