\# JS Beautifier \& Secret Scanner for Burp Suite



A self-contained Burp Suite extension that:



\- \*\*beautifies JavaScript\*\* with the real \[js-beautify 1.15.1](https://github.com/beautifier/js-beautify) engine (the same library used by beautifier.io),

\- \*\*highlights hardcoded secrets\*\* — API keys, AES keys/IVs, Basic-auth credentials, JWTs, private keys, bearer tokens, and risky JavaScript sinks, and

\- \*\*passively audits\*\* in-scope JavaScript plus observed HTTP history for correlated logic, CORS, cookie, header, caching, response-exposure, and \*\*cloud-storage/SAS findings\*\* (Azure Blob SAS, AWS S3 presigned URLs, Google Cloud Storage signed URLs).



> \*\*Passive only.\*\* The extension never sends requests during a scan. A single user-initiated \*\*HEAD validation\*\* button checks Burp scope, asks for confirmation, and sends exactly one metadata-only HEAD request — no body download, no GET fallback, no list/upload/delete.



!\[status](https://img.shields.io/badge/build-passing-brightgreen) !\[burp](https://img.shields.io/badge/Burp-2026.4-blue) !\[version](https://img.shields.io/badge/version-1.5.0-blue) !\[license](https://img.shields.io/badge/license-MIT-green)



\---



\## Features



\### JS Beautifier

\- Real js-beautify engine bundled (no external dependencies)

\- Line numbers, syntax highlighting, options panel

\- Right-click any request/response → \*\*Send to JS Beautifier \& Scanner\*\*



\### Secret Scanner (passive)

Detects and classifies:

\- Generic hardcoded `password`, `secret`, `token`, `apiKey`, `authorization`, `client\_secret`

\- Cloud/API keys: AWS, Google/Firebase, Stripe, GitHub, GitLab, Slack, Twilio, SendGrid, npm, Docker, OpenAI, Anthropic, and more

\- AES keys and IVs, JWT secrets, bearer tokens, Basic-auth (decoded)

\- Private keys (RSA, EC, DSA, OpenSSH, PGP)

\- Client-side APIKey/checksum derivation secrets

\- Firebase database URLs and source-map references



\### Cloud Storage / SAS Detection

\- \*\*Azure Blob SAS\*\* — account/container extraction, permissions (`r`, `w`, `a`, `c`, `d`, `l`, `x`, `t`, `o`, `f`), expiry, IP/protocol restrictions, user-delegation, stored access policy, encryption scope

\- \*\*AWS S3 Presigned URLs\*\* — V2 and V4 signatures, bucket/key/region extraction, virtual-hosted and path-style endpoints, session tokens, dualstack, custom endpoints (MinIO etc.)

\- \*\*Google Cloud Storage Signed URLs\*\* — virtual-hosted and path-style, bucket/object extraction

\- \*\*Bare SAS tokens\*\* — detected without a full URL (reported as "resource unknown")

\- All signatures \*\*redacted\*\* in UI, evidence, POC, curl output, and exports

\- Severity: expired → Low, live write/delete → Critical, live read/list → High, unknown → Medium



\### Scope Audit

\- Correlates JavaScript findings with observed HTTP history

\- CORS, cookie, header, caching, response-exposure analysis

\- Endpoint extraction and classification

\- Export to CSV/JSON with redacted signatures



\### HEAD Validation (opt-in)

\- Explicit button click only

\- Burp scope check before sending

\- Confirmation dialog

\- One HEAD request, metadata-only response (status, Content-Type, Content-Length, Server, Last-Modified)

\- No body download, no GET fallback



Install in Burp

1\. Open Extensions → Installed

2\. Click Add

3\. Extension type: Java

4\. Select JSBeautifierScanner-1.5.0.jar

New suite tabs JS Beautifier and Scope Audit will appear.

Requirements

\- Burp Suite 2026.4 (Montoya API 2026.2) or compatible newer

\- Java 17+

\- No Maven/Gradle, no external runtime dependencies — Rhino, js-beautify, and all scanner code are bundled



Security Notes

\- No hardcoded secrets — all sensitive values come from environment variables or operator input

\- All signatures redacted — SAS tokens, presigned URL signatures, credentials, and session tokens are masked in UI, evidence, POC, curl output, CSV, and JSON exports

\- Passive scan only — no sendRequest() during scanning; HEAD validation requires explicit user click + scope check + confirmation

\- Self-test — 74 offline checks verify checksum logic, cloud parsing, redaction, and POC generation

