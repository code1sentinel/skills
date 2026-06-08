---
name: shift-left-ssp-assessor
description: >
  Scan a codebase and generate a Singapore Government IM8/ICT&SS-aligned System Security Plan (SSP)
  plus a compliance assessment report with matrix and gap analysis. Use when: a team wants to do a
  shift-left security assessment, check IM8 or ICT&SS compliance, generate an SSP for IM8 submission,
  assess a project against the Singapore Government control catalog at info.standards.tech.gov.sg,
  identify security gaps early in development, or evaluate a codebase against government cybersecurity
  controls. Trigger on: "assess my codebase", "generate SSP", "check IM8 compliance", "shift-left
  assessment", "security compliance check", "scan for security gaps", "ICT&SS assessment", "government
  security standards", "control catalog assessment", "IM8 SSP".
---

# Shift-Left SSP Assessor

Scan a codebase to generate a Singapore Government IM8-aligned **System Security Plan (SSP)** and a
**compliance assessment report** against the control catalog at
[info.standards.tech.gov.sg](https://info.standards.tech.gov.sg/control-catalog/).

Designed for third parties to run at project start for proactive, shift-left compliance — before
formal IM8 portal submission.

**Outputs produced in the assessed codebase:**
- `SSP.md` — System Security Plan (IM8-ready)
- `ASSESSMENT-REPORT.md` — Compliance matrix (✅ ❌ ⚠️ 🔵 ❓) + prioritised gap report (🔴 🟠 🟡 🟢)

---

## Workflow

Create a todo checklist at the start and work through each step in order.

---

### Step 1 — Classify the System

Ask all four questions in a single message. Do not proceed to Step 2 until you have answers.

---

> **Q1 — Deployment type**
> Is the system cloud-based, on-premises, or SaaS?
> Options: **Cloud** | **On-Premises** | **SaaS**

> **Q2 — Risk materiality**
> What is the risk materiality level of the system?
> Options: **Low** | **Medium** | **High** | **Critical Information Infrastructure (CII)**

> **Q3 — Development & maintenance** *(skip entirely if SaaS)*
> Is the system's development and maintenance outsourced? If so, where is the team based?
> Options: **In-house** | **Outsourced – Local** | **Outsourced – Offshore** | **Outsourced – Mixed**

> **Q4 — GenAI component**
> Does the system include a Generative AI component (LLM, image generation, RAG pipeline, etc.)?
> Options: **Yes** | **No** | **Under consideration**

---

**Why each answer matters:**

| Q | Impact |
|---|--------|
| Q1 | Selects the SSP template and scopes applicable control domains (e.g. network security differs for SaaS) |
| Q2 | Determines control levels: Low → L0 only required; Medium → L0+L1; High/CII → L0+L1+L2 assessed |
| Q3 | Elevates scrutiny on SC (Software Supply Chain) and PM controls; offshore adds data sovereignty checks |
| Q4 | Triggers GenAI-specific SSP template at `info.standards.tech.gov.sg/ssp/gen-ai/` and additional controls |

**Control level reference:**

| Level | Label | Obligation |
|-------|-------|------------|
| **L0** | Must-Have | Mandatory for ALL systems regardless of risk |
| **L1** | Should-Have | Strongly recommended; expected from Medium-risk systems upward |
| **L2** | Good-to-Have | Best-practice; targeted at High and CII systems |

---

### Step 2 — Fetch Live Control Catalog

The source of truth is **info.standards.tech.gov.sg**. Fetch the live catalog for each domain using
WebFetch. Run all fetches in parallel:

```
https://info.standards.tech.gov.sg/control-catalog/cybersecurity/ac/   (Access Control)
https://info.standards.tech.gov.sg/control-catalog/cybersecurity/as/   (Application Security)
https://info.standards.tech.gov.sg/control-catalog/cybersecurity/sd/   (Secure Development)
https://info.standards.tech.gov.sg/control-catalog/cybersecurity/lm/   (Logging & Monitoring)
https://info.standards.tech.gov.sg/control-catalog/cybersecurity/ns/   (Network Security)
https://info.standards.tech.gov.sg/control-catalog/cybersecurity/sc/   (Software Supply Chain)
https://info.standards.tech.gov.sg/control-catalog/cybersecurity/dp/   (Data Protection)
https://info.standards.tech.gov.sg/control-catalog/cybersecurity/pm/   (Security Programme Mgmt)
```

If Q4 = Yes, also fetch:
```
https://info.standards.tech.gov.sg/ssp/gen-ai/
```

**If any domain returns 403 or fails to load:**
> 📋 Load the fallback catalog: read `./references/control-catalog.md`

Note which domains used the live catalog vs. the fallback. Include this in the final report so
the assessor knows to re-verify against the live site when access is available.

---

### Step 3 — Scan Codebase

Run Glob and Grep searches for each control domain. Maximise parallel calls. The goal is to
find *evidence for or against* each control — not to exhaustively catalogue every file.

#### AC — Access Control
- `Grep` for hardcoded credentials: patterns like `password\s*=\s*["'][^"']+`, `api_key\s*=`, `"admin"`, `"root"`, `"changeme"`, `"password123"`
- `Glob` for `.env*` files; check if `.env` is listed in `.gitignore`
- `Grep` for auth framework imports: `passport`, `oauth`, `jwt`, `oidc`, `spring-security`, `devise`, `authlib`, `casbin`
- Look for RBAC / permission check middleware in route handlers
- `Grep` for MFA patterns: `totp`, `otp`, `mfa`, `2fa`, `authenticator`
- `Grep` for rate-limiting: `rateLimit`, `throttle`, `express-rate-limit`, `slowDown`

#### AS — Application Security
- `Grep` for input validation libs: `zod`, `joi`, `yup`, `pydantic`, `marshmallow`, `javax.validation`, `express-validator`, `cerberus`
- `Grep` for raw SQL construction (injection risk): `\bexecute\b.*\+`, `f"SELECT`, `f"INSERT`, `"SELECT.*" +`, `string.Format.*SELECT`
- `Grep` for ORM usage (safer): `sequelize`, `sqlalchemy`, `hibernate`, `activerecord`, `prisma`, `gorm`, `typeorm`
- `Grep` for output encoding: `DOMPurify`, `htmlspecialchars`, `encodeURIComponent`, `escapeHtml`, `markupsafe`
- `Grep` for secrets in source: `sk-[a-zA-Z0-9]{20,}`, `AKIA[A-Z0-9]{16}`, `xox[baprs]-`, `ghp_`, `-----BEGIN (RSA|EC|OPENSSH|PGP)`
- `Grep` for Content-Security-Policy header configuration

#### SD — Secure Development
- `Glob` for CI/CD configs: `.github/workflows/*.yml`, `.gitlab-ci.yml`, `Jenkinsfile`, `azure-pipelines.yml`, `.circleci/config.yml`, `bitbucket-pipelines.yml`
- In any CI config found, check for: SAST (semgrep, codeql, sonarqube, checkmarx, veracode), secret scanning (gitleaks, trufflehog, detect-secrets), dependency scanning (snyk, dependabot, trivy, grype)
- `Glob` for secret-scanning config: `.gitleaks.toml`, `.secrets.baseline`, `.pre-commit-config.yaml`
- `Glob` for dependency update config: `.github/dependabot.yml`, `renovate.json`, `.renovaterc`
- `Glob` for containers: `Dockerfile`, `docker-compose*.yml` — check for pinned base image tags vs. `latest`
- `Grep` for branch protection hints in CI: `required_status_checks`, `branch-protection`, `protected`

#### LM — Logging & Monitoring
- `Grep` for logging framework imports: `winston`, `pino`, `morgan`, `log4j`, `logback`, `structlog`, `logging`, `slog`, `zap`, `zerolog`
- `Grep` for security event logging: `auth`, `login`, `logout`, `unauthorized`, `forbidden`, `access denied`, `audit`
- `Grep` for structured/JSON logging: `JSON.stringify`, `json_logs`, `format: json`
- `Glob` for monitoring configs: `prometheus.yml`, `grafana/`, `datadog.yaml`, `newrelic.yml`, `otel-collector*`, `opentelemetry*`
- `Glob` for log shipping: `fluent*.conf`, `filebeat.yml`, `logstash*.conf`

#### NS — Network Security
- `Grep` for TLS config: `ssl_cert`, `tls_cert`, `tlsMinVersion`, `SSLContext`, `ssl.wrap_socket`
- `Grep` for HTTPS enforcement: `301`, `https_only`, `HSTS`, `Strict-Transport-Security`, `ssl_redirect`
- `Grep` for security headers middleware: `helmet`, `SecurityHeadersMiddleware`, `cors(`, `CORS`
- `Grep` for non-TLS hardcoded URLs: `http://` (excluding localhost and development URLs)
- `Glob` for IaC firewall/security group configs: `*.tf`, `*.cfn.yml`, `cloudformation*.json`, `security_group`
- `Glob` for WAF config: `waf*.yml`, `waf*.json`, `modsecurity*`

#### SC — Software Supply Chain
- `Glob` for lock files: `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`, `requirements.txt`, `Pipfile.lock`, `poetry.lock`, `go.sum`, `Cargo.lock`, `Gemfile.lock`, `composer.lock`
- Check a sample of dependencies in lock files for version pinning (no `*` or `latest`)
- `Glob` for SBOM generation: `syft*`, `cyclonedx*`, `spdx*` in CI configs or scripts
- `Grep` for container scanning in CI: `trivy`, `snyk container`, `grype`, `clair`, `anchore`
- `Grep` for package signing: `npm audit signatures`, `cosign`, `sigstore`
- If Q3 = Outsourced (Offshore or Mixed): look for vendor security agreements or data processing agreements in `docs/`

#### DP — Data Protection
- `Grep` for encryption lib usage: `crypto`, `cryptography`, `KMSClient`, `SecretManagerClient`, `bcrypt`, `argon2`, `AES`, `fernet`
- `Grep` for PII-related field names in models/schemas: `email`, `phone`, `nric`, `passport`, `dob`, `date_of_birth`, `address`, `full_name`, `national_id`
- `Grep` for data masking / redaction: `mask`, `redact`, `anonymize`, `pseudonymize`, `[REDACTED]`
- `Glob` for backup/retention config: `backup*.yml`, `backup*.sh`, `retention*`
- Check `README.md` or `docs/` for data classification statements

#### PM — Security Programme Management
- `Glob` for security policy files: `SECURITY.md`, `.github/SECURITY.md`, `docs/security*`, `security/`
- `Glob` for incident response docs: `docs/runbook*`, `docs/incident*`, `runbooks/`, `playbooks/`
- `Glob` for `CHANGELOG.md` — check for security patch entries
- `Grep` in `README.md` for security sections
- If Q3 = Outsourced: look for vendor/third-party risk management docs in `docs/`

---

### Step 4 — Generate SSP

Write `SSP.md` in the root of the assessed codebase (or `security/SSP.md` if a `security/` dir exists).

> 📋 Load the SSP section template: read `./references/ssp-template.md`

Populate each section using codebase findings from Step 3 and the classification answers from Step 1.
Where information cannot be inferred from the codebase, use a placeholder:
`[TO BE COMPLETED BY SYSTEM OWNER: <guidance text>]`

---

### Step 5 — Generate Assessment Report

Write `ASSESSMENT-REPORT.md` in the same directory as `SSP.md`.

Use this exact structure:

---

```markdown
# Assessment Report — [System Name]

> **Generated:** [date]
> **Assessed against:** Singapore Government ICT&SS Control Catalog (info.standards.tech.gov.sg)
> **SSP type:** [Cloud Low/Medium/High / On-Premises / SaaS / GenAI]
> **Catalog source:** Live (fetched [date]) | Fallback (./references/control-catalog.md) — [note per domain]

---

## Executive Summary

[2–3 sentences: overall compliance posture, headline finding, recommended immediate action]

---

## Compliance Summary

| Domain | Controls | ✅ | ❌ | ⚠️ | 🔵 | ❓ |
|--------|----------|----|----|----|----|-----|
| AC – Access Control | | | | | | |
| AS – Application Security | | | | | | |
| SD – Secure Development | | | | | | |
| LM – Logging & Monitoring | | | | | | |
| NS – Network Security | | | | | | |
| SC – Software Supply Chain | | | | | | |
| DP – Data Protection | | | | | | |
| PM – Security Programme Mgmt | | | | | | |
| **TOTAL** | **N** | **N** | **N** | **N** | **N** | **N** |

**Overall compliance score:** X% *(of assessed controls — excludes N/A and Unassessed)*

### Legend
| Symbol | Meaning |
|--------|---------|
| ✅ | **Compliant** — clear evidence of implementation found in codebase |
| ❌ | **Non-Compliant** — requirement absent, violated, or counterevidence found |
| ⚠️ | **Partial** — some implementation present but gaps remain |
| 🔵 | **N/A** — not applicable given system type or classification |
| ❓ | **Unassessed** — insufficient evidence; manual review required |

---

## Compliance Matrix

| Control ID | Domain | Level | Control Name | Status | Evidence / Finding | Remediation |
|-----------|--------|-------|-------------|--------|--------------------|-------------|
| AC-1 | Access Control | L0 | Default Credentials | ✅/❌/⚠️/🔵/❓ | [what was found] | [fix] |
| ... | | | | | | |

---

## Gap Report

*Only controls assessed as ❌ Non-Compliant or ⚠️ Partial appear here.*

### 🔴 Critical — Level 0 Non-Compliant (fix before IM8 submission)

| # | Control ID | Level | Finding | Recommended Fix | Effort |
|---|-----------|-------|---------|-----------------|--------|
| 1 | [ID] | L0 | [what is missing/broken] | [specific fix] | Low / Med / High |

### 🟠 High — Level 0 Partial, or Level 1 Non-Compliant

| # | Control ID | Level | Finding | Recommended Fix | Effort |
|---|-----------|-------|---------|-----------------|--------|

### 🟡 Medium — Level 1 Partial, or Level 2 Non-Compliant

| # | Control ID | Level | Finding | Recommended Fix | Effort |
|---|-----------|-------|---------|-----------------|--------|

### 🟢 Low — Level 2 Partial

| # | Control ID | Level | Finding | Recommended Fix | Effort |
|---|-----------|-------|---------|-----------------|--------|

---

## Assessment Notes

- Controls marked ❓ require manual review by a qualified security practitioner
- This assessment is based on static code analysis; runtime behaviour and operational controls (e.g. PM, incident response drills) require separate evaluation
- Re-run this assessment after addressing 🔴 Critical gaps, before IM8 portal submission
- Catalog source: [live or fallback — note any domains that could not be fetched]
```

---

### Step 6 — Commit and Push

```bash
git add SSP.md ASSESSMENT-REPORT.md
git commit -m "Add IM8 shift-left SSP and compliance assessment report"
git push -u origin <current-branch>
```

---

## Wrap Up

Present a summary card to the user:

```
## Shift-Left Assessment Complete ✅

System:       [name]
Deployment:   [Cloud / On-Premises / SaaS]   Risk level: [Low / Medium / High / CII]
GenAI:        [Yes / No]                      Dev model:  [In-house / Outsourced]

Controls assessed: N
  ✅ Compliant:      N     ❌ Non-Compliant:  N
  ⚠️  Partial:        N     🔵 N/A:            N     ❓ Unassessed: N
Compliance score: X% (of assessed controls)

Files written:
  📄 SSP.md               — System Security Plan (ready for IM8 portal)
  📊 ASSESSMENT-REPORT.md — Compliance matrix + gap report

Top critical gaps (Level 0 ❌):
  1. [Control ID] — [one-line finding → quick fix]
  2. [Control ID] — ...

Next steps:
  → Address all 🔴 Critical gaps (Level 0 non-compliant) before IM8 submission
  → Review ❓ Unassessed controls with your security team
  → Re-run this skill after remediation to verify gaps are closed

⚠️  This is a shift-left automated assessment based on static code analysis.
    Operational controls (PM, incident response, security training) need separate manual review.
```
