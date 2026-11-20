# 🛡️ SecuLog Analyzer — Cybersecurity Capstone Project

## 📌 Project Overview
**SecuLog Analyzer** is a lightweight, browser-based Security Information and Event Management (SIEM) log analysis tool designed to detect common web and authentication attack vectors in server logs without transmitting sensitive log data to external servers.

---

## 🏗️ Security Architecture
1. **Zero-Trust Client-Side Processing:** All log parsing and Regular Expression (RegEx) signature matching occur locally inside the user's browser sandbox. No log files are uploaded to a backend database, eliminating data-in-transit interception and third-party data leakage risks.
2. **Signature-Based Detection Engine:** Evaluates raw Apache, Nginx, and Linux SSH (`auth.log`) entries against OWASP Top 10 threat signatures:
   - **SQL Injection (SQLi):** Detects tautologies (`' OR '1'='1`), comment injections (`--`, `#`), and `UNION SELECT` payloads.
   - **Cross-Site Scripting (XSS):** Detects `<script>` tags, URL-encoded tags (`%3Cscript%3E`), and inline event handlers.
   - **Path Traversal / LFI:** Flags `../` sequences and sensitive system file probes (`/etc/passwd`).
   - **Brute-Force & Recon:** Identifies repeated SSH authentication failures and automated directory probing (`/wp-admin`, `/.env`).
3. **XSS Output Sanitization:** Parsed log lines are HTML-entity encoded (`<` and `>` escaped) before rendering in the DOM to prevent Stored/Reflected XSS execution if an attacker injects malicious HTML into a User-Agent string.

---

## 🧪 Testing & Validation Report
*Scope & Authorization Note: All testing was conducted exclusively on synthetic, locally generated server log datasets in an isolated environment.*

| Test Case ID | Attack Vector Simulated | Sample Payload Tested | Expected Outcome | Status |
| :--- | :--- | :--- | :--- | :--- |
| **TC-01** | Normal Traffic | `GET /index.html HTTP/1.1 200` | Ignored (No false positive) | ✅ Pass |
| **TC-02** | SQL Injection | `GET /login.php?user=admin' OR '1'='1` | Flagged as `HIGH` Severity | ✅ Pass |
| **TC-03** | Reflected XSS | `GET /search?q=<script>alert('XSS')</script>` | Flagged as `HIGH` & safely escaped | ✅ Pass |
| **TC-04** | Directory Traversal | `GET /../../../../etc/passwd` | Flagged as `HIGH` Severity | ✅ Pass |
| **TC-05** | SSH Brute-Force | `Failed password for root from 91.132.44.12` | Flagged as `MEDIUM` Severity | ✅ Pass |

---

## 🚀 How to Run Locally
1. Clone the repository: `git clone https://github.com/<your-username>/seculog-analyzer.git`
2. Open `index.html` in any modern web browser.
3. Click **Load Sample Attack Logs** and **Analyze Logs**.