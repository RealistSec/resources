# 🛡️ AI Data Readiness — M365 Data Discovery & Risk Posture Toolkit

> **What can your users see? What shouldn't they?**
> Zero-install M365 data hygiene assessment — deployed as a Teams App or PWA.

---

## 1. Problem Statement

Microsoft 365 Copilot reads everything a user can access. For most SMBs, that's a terrifying sentence — because nobody actually knows the answer to:

> *"What can each of our users see across SharePoint, OneDrive, Teams, and Outlook?"*

The problem isn't theoretical. When an SMB enables Copilot, it inherits every permission misconfiguration, every over-shared SharePoint site, every "Everyone except external users" link, every departed employee's OneDrive that was never cleaned up. **Copilot doesn't create data risk — it surfaces it.**

Traditional approaches to solving this require:

- Enterprise DLP/DSPM tools (£30k+ per year)
- Intune/MDM for endpoint agents
- Active Directory with Group Policy
- Professional services engagements

Most UK SMBs have **none of these**. They have M365 Business Premium, a handful of users, no domain controller, and someone in the office who "does the IT."

**AI Data Readiness** is built for exactly this environment. It answers: *what can this user see, how much of it is risky, and what should you fix before enabling Copilot?*

---

## 2. How It Works — The 90-Second Version

1. **Deploy the app** — M365 admin uploads a Teams App to the org catalog (2 clicks), or users visit a URL
2. **User signs in** — "Sign in with Microsoft" — delegated permissions only, sees exactly what the user sees
3. **Discovery** — the app enumerates every SharePoint site, OneDrive folder, Teams channel, and mail attachment the user can access
4. **Classification** — file metadata is pattern-matched against risk rules; Microsoft Search API probes for sensitive content keywords — all without downloading a single file
5. **User report** — individual risk profile generated entirely in-browser, exportable as CSV/JSON
6. **Collation** — M365 admin imports multiple user reports into the dashboard to build an organisation-wide risk posture view

**All processing happens in the browser. No data leaves the device. No backend server.**

---

## 3. Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                     AI DATA READINESS                            │
│                                                                  │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │              SINGLE APPLICATION (TypeScript)                │ │
│  │                                                             │ │
│  │  ┌────────────┐  ┌───────────────┐  ┌───────────────────┐  │ │
│  │  │  DISCOVERY  │  │ CLASSIFICA-   │  │   COLLATION &     │  │ │
│  │  │   ENGINE    │─▶│    TION       │─▶│    DASHBOARD      │  │ │
│  │  │             │  │   ENGINE      │  │                   │  │ │
│  │  └──────┬──────┘  └──────┬────────┘  └───────────────────┘  │ │
│  │         │                │                                   │ │
│  │    Graph API        Graph Search       Client-side only      │ │
│  │   (delegated)      API + JS regex      (IndexedDB + CSV)    │ │
│  │                                                              │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                  │
│  Deployment:  ① Microsoft Teams Tab App (primary)               │
│               ② Progressive Web App (secondary)                 │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 3.1 Key Architectural Decisions

| Decision | Rationale |
|---|---|
| **Single codebase, two deployment targets** | The Teams Tab App *is* the PWA, just wrapped in a Teams manifest. Same code, same experience. |
| **No backend** | SMBs don't want to host servers. All auth is MSAL.js → Microsoft identity platform. All data stays in-browser (IndexedDB). |
| **Delegated permissions only** | The app sees exactly what the user sees — no more, no less. No admin consent required for basic scan. |
| **Graph Search API replaces ripgrep** | Microsoft already indexes all M365 content (including OCR). We query the index, not the files. |
| **Client-side classification engine** | JavaScript regex runs against file metadata in-browser. No file content is downloaded. |

---

## 4. Phase 1 — Discovery Engine

### 4.1 Objective

Enumerate **every M365 data surface** accessible to the signed-in user, collecting metadata only.

### 4.2 Microsoft Graph API Endpoints Used

| Surface | Graph API Endpoint | What We Get |
|---|---|---|
| **SharePoint sites** | `GET /sites?search=*` | All sites the user can access |
| **SharePoint document libraries** | `GET /sites/{id}/drives` | All document libraries per site |
| **SharePoint files** | `GET /drives/{id}/root/children` (recursive) | File tree with metadata |
| **OneDrive for Business** | `GET /me/drive/root/children` (recursive) | Full OneDrive contents |
| **Teams & channels** | `GET /me/joinedTeams`, `/teams/{id}/channels` | All Teams the user belongs to |
| **Teams channel files** | `GET /teams/{id}/channels/{id}/filesFolder` → drive items | Files shared in channels |
| **Teams chat attachments** | `GET /me/chats/{id}/messages` → attachment metadata | Files sent in 1:1 and group chats |
| **Outlook attachments** | `GET /me/messages?$filter=hasAttachments eq true` | Email attachment metadata |
| **Microsoft 365 Groups** | `GET /me/memberOf` | Group memberships (explains *why* user has access) |
| **Sharing permissions** | `GET /drives/{id}/items/{id}/permissions` | Per-item sharing info — who else can see this? |
| **Sensitivity labels** | `GET /drives/{id}/items/{id}?$select=sensitivityLabel` | Existing MIP labels (if configured) |

### 4.3 Required Delegated Permissions (Scopes)

```
Sites.Read.All
Files.Read.All
Team.ReadBasic.All
Channel.ReadBasic.All
ChannelMessage.Read.All
Chat.Read
Mail.Read
User.Read
Group.Read.All
```

> **Note:** These are all *delegated* scopes — they operate within the signed-in user's own permissions boundary. No tenant-wide admin consent is required for the basic scan. The M365 admin can optionally grant org-wide admin consent for smoother UX.

### 4.4 Output: Discovery Manifest

Stored in IndexedDB and exportable as CSV:

| Column | Description |
|---|---|
| `source_type` | `sharepoint` · `onedrive` · `teams_channel` · `teams_chat` · `outlook` |
| `source_name` | Human-readable source (e.g., "Marketing SharePoint", "#general channel") |
| `source_url` | Web URL to the source container |
| `file_path` | Relative path within the container |
| `file_name` | Filename including extension |
| `extension` | Normalised lowercase extension |
| `size_bytes` | File size |
| `last_modified` | ISO 8601 timestamp |
| `last_modified_by` | UPN of last editor |
| `sharing_scope` | `private` · `specific_people` · `org_wide` · `anyone` · `external` |
| `sharing_link_count` | Number of active sharing links |
| `sensitivity_label` | MIP label if applied, else `none` |
| `permissions_role` | `owner` · `write` · `read` |
| `depth` | Directory depth from source root |

### 4.5 Pagination & Rate Limiting

Graph API responses are paginated and throttled. The Discovery Engine:

- Follows `@odata.nextLink` automatically
- Implements exponential backoff on `429 Too Many Requests`
- Uses `$top=999` batch sizes where supported
- Runs enumeration in parallel across independent surfaces (SharePoint sites enumerate concurrently)
- Shows real-time progress: *"Scanning site 12 of 47 — Marketing Hub — 2,341 files found"*

---

## 5. Phase 2 — Classification Engine

### 5.1 The ripgrep Question — Solved Differently, Possibly Better

The original architecture used `ripgrep` for content-level regex scanning. In the M365-native model, we replace this with **two complementary engines:**

#### Engine A: Metadata Pattern Matching (JavaScript, in-browser)

Runs regex patterns against the Discovery Manifest — filenames, paths, extensions, sharing scopes. This is fast (milliseconds for thousands of files) and covers the majority of classification rules.

```javascript
// Example: runs against every row in the discovery manifest
const rules = [
  { id: 'PII-001', pattern: /passport|national.?insurance|ni.?number/i, severity: 'critical', category: 'pii' },
  { id: 'SEC-001', pattern: /\.(pem|key|pfx|p12|jks|kdbx)$/i, severity: 'critical', category: 'security' },
  { id: 'SEC-002', pattern: /(password|credential|secret|\.env)$/i, severity: 'critical', category: 'security' },
  // ... full ruleset
];
```

#### Engine B: Microsoft Search API Content Probing (Graph API)

For content-level detection — finding sensitive *content* inside files without downloading them — we use the Microsoft Search API with KQL (Keyword Query Language):

```
POST /search/query
{
  "requests": [{
    "entityTypes": ["driveItem"],
    "query": { "queryString": "\"national insurance\" OR \"NI number\" OR \"passport number\"" },
    "from": 0, "size": 500
  }]
}
```

**Why this is arguably better than ripgrep:**

| | ripgrep (local) | Microsoft Search API |
|---|---|---|
| **Scope** | Only locally-hydrated files | All M365 content, everywhere |
| **Content indexing** | Raw file scanning | Pre-indexed by Microsoft, including OCR |
| **File download** | Must download to scan | No download — queries the index |
| **PDF/Image content** | Needs separate OCR tooling | Built-in OCR, text extraction |
| **Performance** | Depends on disk I/O + file count | Returns in seconds regardless of corpus size |
| **DLP trigger risk** | May trigger endpoint DLP | No trigger — it's a search query |

**Limitation:** KQL is not full regex. It supports wildcards (`*`), phrase matching (`"exact phrase"`), and boolean operators — but not arbitrary regex patterns. For complex patterns (e.g., NI number format `\b[A-Z]{2}\d{6}[A-Z]\b`), we rely on Engine A metadata matching and flag items for manual review.

### 5.2 Classification Taxonomy

#### 5.2.1 PII & Personal Data Indicators

| Rule ID | Detection Method | Pattern | Severity |
|---|---|---|---|
| `PII-001` | Metadata + Search | Government identity docs (passport, NI, driving licence) | 🔴 Critical |
| `PII-002` | Metadata + Search | UK payroll & tax records (P45, P60, P11D, payslips) | 🔴 Critical |
| `PII-003` | Metadata + Search | Health & medical data (UK GDPR Art. 9 special category) | 🔴 Critical |
| `PII-004` | Metadata | Recruitment PII (CVs, application forms, interview notes) | 🟠 High |
| `PII-005` | Search (KQL) | Content containing NI number patterns, card numbers | 🔴 Critical |
| `PII-006` | Search (KQL) | Content containing `"date of birth"`, `"home address"` | 🟠 High |

#### 5.2.2 Corporate Sensitivity Indicators

| Rule ID | Detection Method | Pattern | Severity |
|---|---|---|---|
| `CORP-001` | Metadata | Classification labels in filenames (confidential, restricted) | 🟠 High |
| `CORP-002` | Metadata + Search | Board-level governance docs (minutes, board packs) | 🟠 High |
| `CORP-003` | Metadata + Search | M&A, acquisition, due diligence materials | 🔴 Critical |
| `CORP-004` | Metadata + Search | Legal privilege / litigation hold material | 🔴 Critical |
| `CORP-005` | Metadata + Search | HR process docs (disciplinary, grievance, redundancy) | 🟠 High |
| `CORP-006` | Sensitivity Label | Files with MIP labels that contradict their sharing scope | 🔴 Critical |

#### 5.2.3 Security & Technical Risk Indicators

| Rule ID | Detection Method | Pattern | Severity |
|---|---|---|---|
| `SEC-001` | Metadata | Private keys & certificates (.pem, .key, .pfx, .p12) | 🔴 Critical |
| `SEC-002` | Metadata | Credential files (password.*, .env, credentials.*) | 🔴 Critical |
| `SEC-003` | Metadata | SSH key material (id_rsa, id_ed25519) | 🔴 Critical |
| `SEC-004` | Metadata | Database dumps & backups (.sql, .bak, *dump*) | 🟠 High |
| `SEC-005` | Search (KQL) | Hardcoded secrets (api_key, secret_key, connection_string) | 🔴 Critical |
| `SEC-006` | Metadata | Infrastructure state files (terraform.tfstate, kubeconfig) | 🔴 Critical |

#### 5.2.4 Sharing & Access Anomaly Indicators

| Rule ID | Detection Method | Pattern | Severity |
|---|---|---|---|
| `ACC-001` | Permissions API | Files/folders shared with `Everyone except external users` | 🟠 High |
| `ACC-002` | Permissions API | Files with anonymous sharing links (`anyone` scope) | 🔴 Critical |
| `ACC-003` | Permissions API | Files shared with external users | 🟠 High |
| `ACC-004` | Metadata | SharePoint sites the user can access outside their department | 🟡 Medium |
| `ACC-005` | Metadata | Files older than configurable retention threshold | 🟡 Medium |
| `ACC-006` | Permissions API | Items where the user has `owner` or `write` but is no longer in a relevant group | 🟠 High |
| `ACC-007` | Sensitivity Label | Items with no sensitivity label in high-risk locations | 🟡 Medium |

### 5.3 Output: Classification Report

Stored in IndexedDB alongside discovery data, exportable as CSV/JSON:

| Column | Description |
|---|---|
| `rule_id` | Classification rule that triggered |
| `severity` | `critical` · `high` · `medium` · `low` · `info` |
| `category` | `pii` · `corporate` · `security` · `access_anomaly` |
| `detection_method` | `metadata_regex` · `search_api` · `permissions_api` · `sensitivity_label` |
| `source_type` | Inherited from discovery manifest |
| `source_name` | Human-readable source name |
| `file_path` | Path to the flagged item |
| `web_url` | Direct link to the item in M365 |
| `match_context` | What triggered the rule (redacted if content match) |
| `sharing_scope` | Current sharing state of the item |
| `recommendation` | Suggested remediation action |

---

## 6. Phase 3 — Collation Dashboard

### 6.1 Objective

Aggregate individual user classification reports into an **organisation-wide data risk posture view**. This is the screen the M365 admin (or the MSP advising them) will use to prioritise remediation before enabling Copilot.

### 6.2 Data Ingestion

The dashboard operates in the same application:

1. **Drag-and-drop CSV/JSON import** — bulk-import multiple exported user reports
2. **In-app multi-user scan** — if the signed-in user is an M365 admin with appropriate permissions, scan multiple users from a single session
3. **Scheduled re-import** — compare current scan against previous scans to track posture improvement

### 6.3 Dashboard Views

#### 6.3.1 Copilot Readiness Score

The headline metric. A weighted composite score (0–100) answering: **"How safe is it to enable Copilot right now?"**

Weighting factors:
- Critical finding density (40%)
- Over-sharing prevalence — `anyone` and `org_wide` links (25%)
- Sensitivity label coverage — percentage of sensitive files with labels (15%)
- Stale data ratio — files beyond retention period in sensitive categories (10%)
- User coverage — percentage of workforce scanned (10%)

Presented as a large gauge with thresholds:
- 🟢 **80–100:** Low risk — ready for Copilot enablement
- 🟡 **50–79:** Moderate risk — remediate high-severity findings first
- 🔴 **0–49:** High risk — significant data hygiene issues to address

#### 6.3.2 Data Risk Matrix

Interactive heatmap:

| | PII | Corporate | Security | Access Anomaly |
|---|---|---|---|---|
| **🔴 Critical** | count | count | count | count |
| **🟠 High** | count | count | count | count |
| **🟡 Medium** | count | count | count | count |
| **ℹ️ Info** | count | count | count | count |

Click any cell to drill into underlying findings.

#### 6.3.3 SharePoint Site Risk Map

Treemap visualisation of all accessible SharePoint sites, sized by file count and coloured by risk density. Immediately shows which sites are the biggest exposure surface.

#### 6.3.4 Over-Sharing Analysis

Dedicated view for the most common Copilot risk vector:

- Files shared with `Everyone except external users` — grouped by site/owner
- Anonymous sharing links — with creation date and expiry status
- External sharing — files accessible to outside-organisation users
- Sharing link "sprawl" — items with 5+ active sharing links

#### 6.3.5 User Risk Profiles

Per-user cards showing:

- Total files discovered across all M365 surfaces
- Finding count by severity
- Normalised risk score
- Top triggered rules
- Data surfaces accessed (volume breakdown)
- **Outlier badge** — flagged if access pattern deviates from department norm

#### 6.3.6 Compliance & Remediation Tracker

| Finding | Location | Owner | Status | SLA | Acknowledged |
|---|---|---|---|---|---|
| PII-001: Passport scans in Marketing SP | marketing.sharepoint.com | J. Smith | 🟡 In Progress | 72h | ✅ |
| ACC-002: Anonymous link on HR folder | hr.sharepoint.com/docs | Unassigned | 🔴 Open | 24h | ❌ |

Features:
- **Acknowledgement workflow** — finding owners acknowledge, remediate, or accept risk with justification
- **SLA tracking** — configurable response windows by severity
- **Trend tracking** — compare scan-over-scan to show posture improvement
- **Export** — PDF report for board reporting, audit evidence, or Copilot enablement sign-off

#### 6.3.7 Posture Insights

Contextual insights generated from the aggregated data:

- *"23% of your finance team can access HR disciplinary files via the 'All Staff' SharePoint group"*
- *"If User X were compromised, Copilot could surface 847 sensitive files across 12 SharePoint sites"*
- *"The 'Operations' SharePoint site has 142 anonymous sharing links, 89 of which have no expiry date"*
- *"34 files containing 'confidential' in the filename are shared with Everyone"*
- *"Your Copilot readiness score has improved from 31 to 67 since the last scan"*

---

## 7. Deployment

### 7.1 Primary: Microsoft Teams Tab App

**Audience:** Any M365 tenant — no Intune, no AD, no endpoint management required.

**Deployment steps:**

1. M365 admin downloads the Teams App package (`.zip` containing `manifest.json`)
2. Uploads to the [Teams Admin Center → Manage Apps → Upload](https://admin.teams.microsoft.com/policies/manage-apps)
3. Optionally pins the app for all users via Teams App Setup Policy
4. Users see "AI Data Readiness" in their Teams sidebar → click → sign in → scan

**Technical detail:** The Teams Tab is an iframe loading the same web application. Authentication uses Teams SSO (`@microsoft/teams-js` SDK) for seamless sign-in — no popup, no redirect, no password entry.

### 7.2 Secondary: Progressive Web App (PWA)

**Audience:** Users without Teams installed, or for quick ad-hoc assessments.

**Deployment:** Host the same web application on any static host (Cloudflare Pages, Azure Static Web Apps, GitHub Pages). Users visit the URL, click "Sign in with Microsoft", and run the scan.

The PWA can be "installed" to the desktop/home screen for repeat use. Authentication uses MSAL.js with popup or redirect flow.

### 7.3 Same Codebase, Same Experience

```
src/
├── app/                    # Shared application code
│   ├── discovery/          # Graph API enumeration engine
│   ├── classification/     # Pattern matching + Search API classification
│   ├── dashboard/          # Collation dashboard views
│   └── export/             # CSV/JSON/PDF export
├── teams/                  # Teams-specific wrapper
│   ├── manifest.json       # Teams App manifest
│   └── auth-teams.ts       # Teams SSO authentication
├── pwa/                    # PWA-specific wrapper
│   ├── manifest.json       # PWA manifest
│   └── auth-msal.ts        # MSAL.js browser authentication
└── shared/                 # Shared utilities, types, rules
```

---

## 8. Security & Privacy

| Concern | Mitigation |
|---|---|
| **Data residency** | All processing is client-side. No data is sent to any server. No backend exists. |
| **Credential handling** | MSAL.js / Teams SSO tokens are ephemeral, in-memory only. Never persisted to disk or IndexedDB. |
| **Content exposure** | Search API queries return metadata and snippets — full file content is never downloaded. Snippets are truncated and redacted before storage. |
| **Scan auditability** | Every Graph API call appears in the M365 Unified Audit Log. The scan is fully transparent to tenant admins. |
| **Permissions model** | Delegated permissions only. The app cannot see anything the user can't already see. |
| **Open source** | Fully auditable codebase. No obfuscation. No telemetry. No analytics. |

---

## 9. Entra ID App Registration

The M365 admin creates a single App Registration in Entra ID:

| Setting | Value |
|---|---|
| **Name** | AI Data Readiness |
| **Supported account types** | Single tenant (this organisation only) |
| **Redirect URI (PWA)** | `https://your-domain.com/auth/callback` |
| **Redirect URI (Teams)** | `https://your-domain.com/auth/teams` |
| **API Permissions** | `Sites.Read.All`, `Files.Read.All`, `Team.ReadBasic.All`, `Channel.ReadBasic.All`, `ChannelMessage.Read.All`, `Chat.Read`, `Mail.Read`, `User.Read`, `Group.Read.All` (all delegated) |
| **Admin consent** | Optional — improves UX by removing per-user consent prompts |

---

## 10. Compliance Framework Mapping

| Framework | Relevant Controls | How This Tool Helps |
|---|---|---|
| **UK GDPR** | Art. 5(1)(f), Art. 25, Art. 30, Art. 32 | Identifies PII sprawl across M365; evidences data mapping |
| **ISO 27001:2022** | A.5.9, A.5.10, A.5.12, A.5.13, A.8.3, A.8.10 | Access review evidence; information classification audit |
| **Cyber Essentials Plus** | Access Control, Secure Configuration | Validates least-privilege; finds credential exposure |
| **PCI DSS v4.0** | Req. 3, Req. 12.3 | Locates cardholder data outside authorised locations |
| **NIST CSF 2.0** | ID.AM, PR.AC, PR.DS, DE.CM | Asset inventory; access control review |

---

## 11. Stretch Goal: Endpoint Discovery Scripts

> **For organisations with endpoint management (Intune, GPO, RMM, or in-house admins / MSP support).**

The M365-native scan covers cloud data surfaces. For organisations that also need to assess **local filesystems, network shares, and mapped drives**, the original script-based Discovery Engine remains available as an optional extension.

### 11.1 What Endpoint Scripts Add

| Surface | Method | Platform |
|---|---|---|
| **Local filesystem** | Native `dir` / `ls` / `Get-ChildItem` recursion | Win / Mac / Linux |
| **Mapped network drives** | UNC path enumeration via `net use` / `mount` | Win / Mac / Linux |
| **Git repositories** | `.git` folder detection + remote URL extraction | Cross-platform |
| **Database connection strings** | Config file pattern matching | Cross-platform |

### 11.2 Script Variants

```
scripts/
├── Invoke-DataDiscovery.ps1      # Windows (PowerShell 5.1+ / 7+)
├── data-discovery.sh             # Linux / macOS (Bash 4+)
└── data-discovery.bat            # Legacy Windows fallback (cmd.exe)
```

### 11.3 Content-Level Scanning with ripgrep

When running on the endpoint, scripts use `ripgrep` for deep content-level regex scanning of locally available files:

- Full regex support (NI number format, card number patterns, etc.)
- Scans file content directly — not dependent on any cloud index
- Binary file detection and exclusion
- Configurable include/exclude path patterns

### 11.4 Deployment Requirements

| Requirement | Detail |
|---|---|
| **Execution policy** | `Set-ExecutionPolicy Bypass -Scope Process` or `-ExecutionPolicy Bypass` flag |
| **Admin privileges** | Not required — runs as standard user |
| **ripgrep** | Bundled portable binary or pre-installed from package manager |
| **Deployment method** | Intune script deployment, GPO login script, RMM task, or manual execution |

### 11.5 Output Integration

Endpoint script output produces the same CSV schema as the M365 scan, with additional `source_type` values (`local`, `network_share`, `git_repo`). These CSVs can be imported directly into the Collation Dashboard alongside M365 scan results for a **unified data risk posture** across both cloud and endpoint.

### 11.6 Classification Rules

```
scripts/rules/
├── pii-rules.yaml             # PII detection patterns (UK-focused)
├── corporate-rules.yaml       # Corporate sensitivity patterns
├── security-rules.yaml        # Security/technical risk patterns
└── access-rules.yaml          # Access anomaly patterns
```

Rules are YAML-defined for easy authoring and extension. See `docs/rule-authoring.md` for syntax.

---

## 12. Project Structure

```
ai-data-readiness/
├── README.md                          # This file
├── index.html                         # Application entry point
├── src/
│   ├── app/
│   │   ├── discovery/                 # Graph API enumeration engine
│   │   ├── classification/            # Metadata regex + Search API classification
│   │   ├── dashboard/                 # Collation dashboard views & visualisations
│   │   └── export/                    # CSV, JSON, PDF export
│   ├── teams/
│   │   ├── manifest.json              # Teams App manifest
│   │   └── auth-teams.ts              # Teams SSO wrapper
│   ├── pwa/
│   │   ├── manifest.json              # PWA manifest
│   │   └── auth-msal.ts              # MSAL.js browser auth
│   └── shared/
│       ├── rules/                     # Classification rule definitions
│       ├── types/                     # TypeScript type definitions
│       └── utils/                     # Shared utilities
├── scripts/                           # [Stretch] Endpoint discovery scripts
│   ├── Invoke-DataDiscovery.ps1
│   ├── data-discovery.sh
│   ├── data-discovery.bat
│   └── rules/                         # ripgrep-compatible rule definitions
├── docs/
│   ├── screenshot.png                 # Dashboard screenshot
│   ├── deployment-guide.md            # Step-by-step deployment
│   ├── entra-app-registration.md      # App registration walkthrough
│   ├── rule-authoring.md              # Custom rule syntax
│   └── compliance-mapping.md          # Framework control mapping
└── examples/
    ├── sample-discovery.csv
    ├── sample-classification.csv
    └── sample-collation-import/
```

---

## 13. Roadmap

| Phase | Milestone | Status |
|---|---|---|
| **v0.1** | Entra ID app registration + MSAL.js auth flow | 🔲 Planned |
| **v0.2** | SharePoint + OneDrive discovery (enumerate + metadata) | 🔲 Planned |
| **v0.3** | Teams + Outlook discovery | 🔲 Planned |
| **v0.4** | Metadata classification engine (JS regex against manifest) | 🔲 Planned |
| **v0.5** | Microsoft Search API content probing | 🔲 Planned |
| **v0.6** | Sharing & permissions analysis (over-sharing detection) | 🔲 Planned |
| **v0.7** | User risk report — individual scan export | 🔲 Planned |
| **v0.8** | Teams App packaging + Teams SSO | 🔲 Planned |
| **v0.9** | Collation Dashboard — multi-user import, heatmap, risk matrix | 🔲 Planned |
| **v1.0** | Copilot Readiness Score + remediation tracker + PDF export | 🔲 Planned |
| **v1.1** | *Stretch:* Endpoint discovery scripts (PowerShell/Bash) | 🔲 Planned |
| **v1.2** | *Stretch:* Posture trend tracking (scan-over-scan comparison) | 🔲 Planned |

---

## 14. Key Design Principles

1. **M365-native** — works where the data lives, using the APIs Microsoft provides
2. **Zero install** — Teams App or PWA; no agents, no endpoints, no servers
3. **User-context, not admin-context** — see exactly what a real user (and therefore Copilot) can see
4. **Forensically inert** — read-only API calls; no files downloaded, modified, or moved
5. **Air-gapped capable** — once loaded, the dashboard works fully offline against exported data
6. **Incrementally deployable** — scan one user today, ten tomorrow, the whole company next week
7. **Copilot Readiness focused** — every feature answers: *"Is it safe to turn on Copilot?"*

---

<p align="center">
  <sub>Built by <a href="https://realistsec.com">RealistSec</a> · AI Supported, Human Verified</sub>
</p>
