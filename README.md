# JS Beautifier & Secret Scanner for Burp Suite

**A passive JavaScript analysis, hardcoded secret detection, and cloud-storage security auditing extension for Burp Suite.**

[![Build](https://img.shields.io/badge/build-passing-brightgreen)](https://github.com/)
[![Burp Suite](https://img.shields.io/badge/Burp-2026.4-blue)](https://portswigger.net/burp)
[![Version](https://img.shields.io/badge/version-1.5.0-blue)](#)
[![Java](https://img.shields.io/badge/Java-17%2B-orange)](https://www.oracle.com/java/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

---

## Overview

**JS Beautifier & Secret Scanner** is a self-contained Burp Suite extension designed to help security researchers and penetration testers identify security weaknesses in JavaScript files and observed HTTP traffic.

The extension combines JavaScript beautification, hardcoded secret detection, passive security auditing, and cloud-storage signature analysis into a single Burp Suite extension.

It identifies exposed credentials, cryptographic material, authentication tokens, risky JavaScript sinks, client-side security logic, misconfigured security controls, and potentially exposed cloud-storage resources.

### Key Capabilities

- **JavaScript Beautifier:** Beautify and analyze minified or obfuscated JavaScript.
- **Hardcoded Secret Detection:** Identify API keys, passwords, AES keys, JWTs, bearer tokens, and private keys.
- **JavaScript Security Analysis:** Detect risky JavaScript sinks and client-side security logic.
- **Cloud Storage Detection:** Identify Azure Blob SAS tokens, AWS S3 presigned URLs, and Google Cloud Storage signed URLs.
- **Passive Scope Audit:** Analyze in-scope JavaScript and observed HTTP history.
- **Security Configuration Analysis:** Identify CORS, cookie, header, caching, and response-exposure weaknesses.
- **Evidence Export:** Export findings into CSV and JSON with sensitive values redacted.
- **Optional HEAD Validation:** Perform a single, user-authorized metadata-only HEAD request.

> **Important:** Scanning is passive. The extension does not generate HTTP requests during passive analysis. HEAD validation is a separate, explicitly user-initiated action.

---

## Table of Contents

1. [Features](#features)
2. [Architecture](#architecture)
3. [JS Beautifier](#1-js-beautifier)
4. [Secret Scanner](#2-secret-scanner)
5. [Cloud Storage & SAS Detection](#3-cloud-storage--sas-detection)
6. [Scope Audit](#4-scope-audit)
7. [HEAD Validation](#5-head-validation)
8. [Installation](#installation)
9. [Requirements](#requirements)
10. [Usage Guide](#usage-guide)
11. [Export & Reporting](#export--reporting)
12. [Security & Privacy](#security--privacy)
13. [Self-Test](#self-test)
14. [Limitations](#limitations)
15. [Version Information](#version-information)
16. [License](#license)

---

## Features

### 1. JS Beautifier

A built-in JavaScript beautification engine that helps researchers analyze minified JavaScript files and understand client-side application logic.

**Capabilities:**

- Bundled `js-beautify` engine.
- No external runtime dependencies.
- Beautification of minified JavaScript.
- Line-number support.
- Syntax highlighting.
- Configurable beautification options.
- Integrated JavaScript analysis.

**Burp Suite Integration:**

Right-click any HTTP request or response containing JavaScript and select:

`Send to JS Beautifier & Scanner`

The selected content can then be analyzed within the extension.

### 2. Secret Scanner

The Secret Scanner identifies potentially exposed credentials, authentication material, cryptographic keys, and sensitive configuration values embedded in JavaScript and observed HTTP content.

#### Detection Categories

| Category | Detection |
|---|---|
| Generic Secrets | Passwords, secrets, tokens, API keys |
| Authentication | Authorization headers, Basic Auth credentials, bearer tokens |
| Cloud Credentials | AWS, Google Cloud, Firebase |
| Payment APIs | Stripe and other payment-related API credentials |
| Developer Platforms | GitHub, GitLab, npm, Docker |
| Communication APIs | Slack, Twilio, SendGrid |
| AI API Credentials | OpenAI, Anthropic |
| Cryptographic Material | AES keys, AES IVs, JWT secrets |
| Private Keys | RSA, EC, DSA, OpenSSH, PGP |
| Application Logic | Client-side API key and checksum derivation |
| Firebase | Firebase database URLs |
| Source Maps | JavaScript source-map references |

#### Additional Analysis

- Basic Authentication credential decoding.
- JWT identification.
- Bearer token detection.
- AES key and initialization vector identification.
- Client-side checksum and API key derivation analysis.
- Source-map reference identification.

> **Note:** Detection results are potential security findings. Manual verification is recommended before reporting a vulnerability.

### 3. Cloud Storage & SAS Detection

The extension includes dedicated detection and parsing logic for commonly encountered cloud-storage access signatures.

#### Azure Blob Storage SAS

Detects and analyzes Azure Shared Access Signature (SAS) tokens.

**Extracted Information:**

- Storage account.
- Container and resource information.
- SAS permissions.
- Start and expiration timestamps.
- IP restrictions.
- Protocol restrictions.
- User-delegation information.
- Stored access policy references.
- Encryption scope.

**Supported Permissions:**

| Permission | Description |
|---|---|
| `r` | Read |
| `w` | Write |
| `a` | Add |
| `c` | Create |
| `d` | Delete |
| `l` | List |
| `x` | Delete previous versions |
| `t` | Read tags |
| `o` | Set tags |
| `f` | Filter by tags |

#### AWS S3 Presigned URLs

Supports detection of AWS S3 presigned URLs, including:

- Signature Version 2.
- Signature Version 4.
- Bucket and object extraction.
- AWS region identification.
- Virtual-hosted-style endpoints.
- Path-style endpoints.
- Temporary session tokens.
- Dual-stack endpoints.
- Custom S3-compatible endpoints, including MinIO.

#### Google Cloud Storage Signed URLs

Supports detection of Google Cloud Storage signed URLs.

**Detection includes:**

- Virtual-hosted-style URLs.
- Path-style URLs.
- Bucket extraction.
- Object extraction.
- Signed URL parameter identification.

#### Bare SAS Token Detection

The scanner can identify SAS tokens even when they appear without a complete resource URL.

Such findings are reported with the resource marked as unknown.

#### Signature Redaction

Sensitive cloud-storage signatures are redacted across:

- Extension UI.
- Finding evidence.
- Proof of concept.
- Generated cURL output.
- CSV exports.
- JSON exports.

#### Cloud Finding Severity Classification

| Condition | Severity |
|---|---|
| Expired signature | Low |
| Active write/delete permissions | Critical |
| Active read/list permissions | High |
| Unknown or incomplete permissions | Medium |

> Severity is an initial classification based on detected signature properties. Actual impact depends on resource exposure, authorization requirements, and the application's security context.

### 4. Scope Audit

The Scope Audit module performs passive analysis of in-scope JavaScript files and observed HTTP history.

It correlates JavaScript findings with available HTTP traffic to identify potential application security weaknesses.

#### Security Checks

**CORS Analysis**
- Access-Control-Allow-Origin configuration.
- Potentially permissive cross-origin policies.
- Credential and origin configuration review.

**Cookie Security**
- Missing Secure attribute.
- Missing HttpOnly attribute.
- SameSite configuration review.

**Security Headers**
- Missing or weak security headers.
- HTTP response security configuration.
- Potentially unsafe header configurations.

**Caching Analysis**
- Cache-Control configuration.
- Potentially sensitive responses being cached.
- Public/private caching policy review.

**Response Exposure**
- Sensitive information in HTTP responses.
- Exposed configuration values.
- Potentially exposed authentication material.

**Endpoint Analysis**
- Endpoint extraction.
- Endpoint classification.
- Correlation with JavaScript references.
- Observed HTTP history analysis.

**JavaScript Correlation**
- Client-side API key references.
- Checksum generation logic.
- Authentication-related implementation.
- Potentially exposed application logic.

### 5. HEAD Validation

The extension provides an optional, manually triggered HEAD validation feature for cloud-storage resources.

This feature is separate from passive scanning.

#### Validation Workflow

1. The researcher selects a detected cloud-storage resource.
2. The researcher clicks the HEAD Validation button.
3. The extension verifies that the target is within Burp Suite scope.
4. A confirmation dialog is displayed.
5. After confirmation, exactly one HEAD request is sent.
6. The metadata-only response is displayed.

#### Response Metadata

The extension captures:

- HTTP status code.
- Content-Type.
- Content-Length.
- Server.
- Last-Modified.

#### Safety Restrictions

- Explicit user interaction is mandatory.
- Burp Suite scope validation is performed before sending.
- User confirmation is required.
- Exactly one HEAD request is sent per validation action.
- No response body download.
- No GET fallback.
- No bucket listing.
- No file upload.
- No file deletion.

---

## Architecture

The extension is organized into the following logical modules:

```text
JS Beautifier & Secret Scanner
│
├── JS Beautifier
│   ├── JavaScript Parser
│   ├── Beautification Engine
│   ├── Syntax Highlighting
│   └── Line Number Support
│
├── Secret Scanner
│   ├── Generic Secret Detection
│   ├── API Key Detection
│   ├── Authentication Detection
│   ├── Cryptographic Key Detection
│   ├── JWT Analysis
│   └── Private Key Detection
│
├── Cloud Storage Scanner
│   ├── Azure Blob SAS Parser
│   ├── AWS S3 Signature Parser
│   ├── GCS Signed URL Parser
│   └── Signature Redaction
│
├── Scope Audit
│   ├── JavaScript Correlation
│   ├── HTTP History Analysis
│   ├── CORS Analysis
│   ├── Cookie Analysis
│   ├── Header Analysis
│   ├── Cache Analysis
│   └── Response Exposure Analysis
│
├── HEAD Validation
│   ├── Scope Verification
│   ├── User Confirmation
│   └── Metadata Extraction
│
└── Reporting
    ├── CSV Export
    ├── JSON Export
    └── Evidence Redaction
```

---

## Installation

### Step 1: Download the Extension

Download the latest release:

`JSBeautifierScanner-1.5.0.jar`

### Step 2: Open Burp Suite

Launch Burp Suite Professional & Community Version.

### Step 3: Navigate to Extensions

1. Open **Extensions**.
2. Select **Installed**.
3. Click **Add**.

### Step 4: Configure the Extension

Select the following options:

| Setting | Value |
|---|---|
| Extension Type | Java |
| Extension File | JSBeautifierScanner-1.5.0.jar |

### Step 5: Verify Installation

Once loaded successfully, the following suite tabs should appear:

- **JS Beautifier**
- **Scope Audit**

The extension is now ready for use.

---

## Requirements

| Component | Requirement |
|---|---|
| Burp Suite | 2026.4 or compatible newer version |
| Montoya API | 2026.2 |
| Java | 17 or newer |
| Extension Format | Java JAR |
| External Dependencies | None at runtime |

## Usage Guide

### Workflow 1: JavaScript Beautification

1. Open Proxy → HTTP History.
2. Identify a JavaScript request or response.
3. Right-click the selected item.
4. Select **Send to JS Beautifier & Scanner**.
5. Open the JS Beautifier tab.
6. Review the formatted JavaScript.
7. Analyze the application logic and detected findings.

### Workflow 2: Secret Detection

1. Send JavaScript content to the extension.
2. Open the Secret Scanner results.
3. Review the detected credentials and cryptographic material.
4. Check the associated evidence.
5. Verify whether the exposed value is valid and security-relevant.
6. Export the findings if required.

### Workflow 3: Cloud Storage Analysis

1. Open the Scope Audit module.
2. Review detected cloud-storage URLs.
3. Identify the storage provider.
4. Review the extracted permissions and expiration details.
5. Examine the assigned severity.
6. Use optional HEAD validation only when authorized and necessary.

### Workflow 4: Passive Scope Audit

1. Configure the appropriate Burp Suite scope.
2. Browse the authorized application through Burp Suite.
3. Allow HTTP history to collect application traffic.
4. Open Scope Audit.
5. Review JavaScript and HTTP history findings.
6. Analyze correlated security observations.
7. Export the results.

---

## Export & Reporting

The extension supports exporting security findings in the following formats:

| Format | Purpose |
|---|---|
| CSV | Spreadsheet analysis and reporting |
| JSON | Structured findings and integration |

### Export Security

All sensitive values are redacted before export.

This includes:

- API keys.
- Passwords.
- Authentication tokens.
- Cloud SAS signatures.
- AWS presigned URL signatures.
- Temporary session tokens.
- Sensitive credentials.

---

## Security & Privacy

Security and data protection are important design considerations.

The extension follows these principles:

**1. Passive Scanning**

No HTTP requests are generated during passive scanning.

**2. No Hardcoded Credentials**

Sensitive values are sourced from observed application traffic or operator input rather than hardcoded scanner credentials.

**3. Signature Redaction**

Sensitive credentials and cloud-storage signatures are masked in the UI, evidence, POC, cURL output, and exports.

**4. Controlled HEAD Validation**

HEAD validation requires an explicit user action, scope verification, and confirmation.

**5. No Destructive Cloud Operations**

The extension does not perform bucket listing, file upload, file modification, or file deletion.

**6. Offline Analysis**

The bundled scanning components do not require external runtime dependencies.

---

## Self-Test

The extension includes an offline self-test suite.

**Test Coverage: 74 checks**

The self-test covers:

- Checksum logic.
- Cloud-storage URL parsing.
- Azure SAS parsing.
- AWS signature parsing.
- Google Cloud Storage URL parsing.
- Sensitive signature redaction.
- Proof-of-concept generation.

The self-test is designed to validate internal parsing and processing logic without requiring external network requests.

---

## Limitations

- Secret detection relies on pattern matching and may produce false positives.
- A detected API key does not automatically indicate that the credential is active or exploitable.
- Cloud-storage signature parsing does not independently establish resource accessibility.
- HEAD responses may not accurately represent GET accessibility or permissions.
- Passive analysis is limited to JavaScript and HTTP traffic observed by Burp Suite.
- JavaScript obfuscation and runtime-generated secrets may limit static detection.
- Findings require manual validation before being included in a penetration testing report.

---

## Version Information

| Property | Details |
|---|---|
| Project | JS Beautifier & Secret Scanner |
| Version | 1.5.0 |
| Platform | Burp Suite |
| Burp Compatibility | 2026.4 or compatible newer |
| Montoya API | 2026.2 |
| Java Requirement | Java 17+ |
| Extension Format | JAR |
| License | MIT |
| Self-Test | 74 offline checks |

---

## License

This project is licensed under the **MIT License**.

See the `LICENSE` file for additional information.

---

## Disclaimer

This extension is intended for authorized security assessments, penetration testing, application security reviews, and educational purposes.

Use this extension only against applications, infrastructure, and cloud-storage resources for which you have explicit authorization.

The developer assumes no responsibility for unauthorized usage or misuse of this software.

**Perform security testing responsibly and within the defined scope of authorization.**

---

**JS Beautifier & Secret Scanner for Burp Suite**

*Beautify. Detect. Analyze. Secure.*
