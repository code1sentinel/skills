---
name: ssp-review
description: >
  Assess a codebase for compliance against the Singapore Government ICT&SS control catalog and
  generate a compliance matrix with a prioritised gap report. Use when: a team wants to check
  IM8 compliance, assess security controls in a codebase, identify compliance gaps, generate a
  compliance report for a cloud or on-premises system, evaluate against info.standards.tech.gov.sg
  controls, or do a shift-left compliance assessment. Trigger on: "assess compliance", "check IM8
  compliance", "security assessment", "compliance matrix", "gap report", "scan for security gaps",
  "ICT&SS assessment", "control catalog assessment", "shift-left assessment", "assess my codebase",
  "ssp-review", "review SSP".
---

# Shift-Left SSP Assessor

Assess a codebase against the Singapore Government ICT&SS control catalog at
[info.standards.tech.gov.sg](https://info.standards.tech.gov.sg/control-catalog/) and produce a
compliance matrix with a prioritised gap report.

**Output:** `ASSESSMENT-REPORT.md` written alongside `SSP.md` (or at the codebase root)

> **Pair with** `/ssp-create` to generate the SSP document first, then run this skill
> to assess it.

**Status symbols:**
| Symbol | Meaning |
|--------|---------|
| ✅ | **Compliant** — clear evidence of implementation found in codebase |
| ❌ | **Non-Compliant** — requirement absent, violated, or counterevidence found |
| 🔵 | **N/A** — not applicable given system type or risk level |
| ❓ | **Unassessed** — insufficient evidence; manual review required |

---

## Workflow

Create a checklist and work through each step in order.

---

### Step 1 — Classify the System

First, check whether `SSP.md` exists in the codebase root or `security/` directory.
If it contains a **System Classification** table, read the four values from there and skip the
questions — confirm the values with the user in one line before proceeding.

Otherwise, ask all four questions in a single message:

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

**Control scope by risk level:**

| Level | Label | Obligation | Assessed? |
|-------|-------|------------|-----------|
| **L0** | Must-Have | Mandatory for ALL systems | Always |
| **L1** | Should-Have | Strongly recommended; expected Medium+ | Medium / High / CII |
| **L2** | Good-to-Have | Best practice; High and CII | High / CII only |

Low risk: L0 assessed; L1 and L2 marked 🔵 N/A
Medium risk: L0 + L1 assessed; L2 marked 🔵 N/A
High / CII: L0 + L1 + L2 all assessed

---

### Step 2 — Fetch Live Control Catalog

Fetch the current catalog from the source of truth. Run all fetches in parallel:

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

If Q4 = Yes, also fetch: `https://info.standards.tech.gov.sg/ssp/gen-ai/`

**If any domain returns 403 or fails:**
> 📋 Load the fallback catalog: read `./references/control-catalog.md`

Note per-domain whether live or fallback was used — include this in the assessment report.

---

### Step 3 — Scan Codebase for Control Evidence

Run Glob and Grep searches per control domain. Maximise parallel calls. Look for *evidence for
or against* each control — not an exhaustive file listing.

#### AC — Access Control
- `Grep` for hardcoded credentials: `password\s*=\s*["'][^"']+`, `api_key\s*=`, `"admin"`, `"root"`, `"changeme"`, `"password123"`
- `Glob` for `.env*` files; check if `.env` is in `.gitignore`
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
- `Grep` for Content-Security-Policy configuration

#### SD — Secure Development
- `Glob` for CI/CD configs: `.github/workflows/*.yml`, `.gitlab-ci.yml`, `Jenkinsfile`, `azure-pipelines.yml`, `.circleci/config.yml`
- In any CI config found, check for: SAST (semgrep, codeql, sonarqube), secret scanning (gitleaks, trufflehog), dependency scanning (snyk, dependabot, trivy)
- `Glob` for secret-scanning config: `.gitleaks.toml`, `.secrets.baseline`, `.pre-commit-config.yaml`
- `Glob` for dependency update config: `.github/dependabot.yml`, `renovate.json`, `.renovaterc`
- `Glob` for containers: `Dockerfile`, `docker-compose*.yml` — check for pinned base image tags
- `Grep` for branch protection hints: `required_status_checks`, `branch-protection`

#### LM — Logging & Monitoring
- `Grep` for logging framework imports: `winston`, `pino`, `morgan`, `log4j`, `logback`, `structlog`, `logging`, `slog`, `zap`, `zerolog`
- `Grep` for security event logging: `auth`, `login`, `logout`, `unauthorized`, `forbidden`, `audit`
- `Grep` for structured/JSON logging: `json_logs`, `format: json`, `JSON.stringify`
- `Glob` for monitoring configs: `prometheus.yml`, `grafana/`, `datadog.yaml`, `newrelic.yml`, `otel*`
- `Glob` for log shipping: `fluent*.conf`, `filebeat.yml`, `logstash*.conf`

#### NS — Network Security
- `Grep` for TLS config: `ssl_cert`, `tls_cert`, `tlsMinVersion`, `SSLContext`, `ssl.wrap_socket`
- `Grep` for HTTPS enforcement: `https_only`, `HSTS`, `Strict-Transport-Security`, `ssl_redirect`
- `Grep` for security headers middleware: `helmet`, `SecurityHeadersMiddleware`, `cors(`
- `Grep` for non-TLS hardcoded URLs: `http://` (excluding localhost and dev URLs)
- `Glob` for IaC security group configs: `*.tf`, `*.cfn.yml`, `cloudformation*.json`
- `Glob` for WAF config: `waf*.yml`, `waf*.json`, `modsecurity*`

#### SC — Software Supply Chain
- `Glob` for lock files: `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`, `requirements.txt`, `Pipfile.lock`, `poetry.lock`, `go.sum`, `Cargo.lock`, `Gemfile.lock`
- Check a sample of dependencies for version pinning (no `*` or `latest`)
- `Glob` for SBOM generation: `syft*`, `cyclonedx*`, `spdx*` in CI configs or scripts
- `Grep` for container scanning in CI: `trivy`, `snyk container`, `grype`, `clair`
- `Grep` for package signing: `cosign`, `sigstore`
- If Q3 = Outsourced (Offshore or Mixed): look for vendor security agreements in `docs/`

#### DP — Data Protection
- `Grep` for encryption lib usage: `crypto`, `cryptography`, `KMSClient`, `SecretManagerClient`, `bcrypt`, `argon2`, `AES`, `fernet`
- `Grep` for PII field names: `email`, `phone`, `nric`, `passport`, `dob`, `date_of_birth`, `address`, `full_name`, `national_id`
- `Grep` for data masking: `mask`, `redact`, `anonymize`, `pseudonymize`
- `Glob` for backup/retention config: `backup*.yml`, `backup*.sh`, `retention*`
- Check `README.md` or `docs/` for data classification statements

#### PM — Security Programme Management
- `Glob` for `SECURITY.md`, `.github/SECURITY.md`, `docs/security*`
- `Glob` for incident response docs: `docs/runbook*`, `docs/incident*`, `runbooks/`, `playbooks/`
- `Glob` for `CHANGELOG.md` — check for security patch entries
- `Grep` in `README.md` for security sections

#### GenAI Controls (if Q4 = Yes)
- `Grep` for prompt injection defences: `sanitize`, `guardrail`, `content_filter`, `moderate`, `injection`
- `Grep` for PII scrubbing before LLM calls: `scrub`, `strip_pii`, `anonymize`, `redact`
- `Grep` for LLM output filtering or validation
- `Grep` for AI-specific rate limiting or abuse detection
- `Grep` for AI audit logging patterns: `log.*prompt`, `log.*response`, `ai_audit`

---

### Step 4 — Generate Assessment Report

Write `ASSESSMENT-REPORT.md` alongside `SSP.md`, or at the codebase root if no SSP exists yet.

Use this exact structure:

```markdown
# Assessment Report — [System Name]

> **Generated:** [date]
> **Assessed against:** Singapore Government ICT&SS Control Catalog (info.standards.tech.gov.sg)
> **SSP type:** [Cloud Low-Risk / Medium-Risk / High-Risk / On-Premises / SaaS / + GenAI]
> **Catalog source:** Live (fetched [date]) | Fallback (`./references/control-catalog.md`) — [note per domain]

---

## Executive Summary

[2–3 sentences: overall compliance posture, headline finding, recommended immediate action]

---

## Compliance Summary

| Domain | Controls | ✅ | ❌ | 🔵 | ❓ |
|--------|----------|----|----|----|----|
| AC – Access Control | | | | | |
| AS – Application Security | | | | | |
| SD – Secure Development | | | | | |
| LM – Logging & Monitoring | | | | | |
| NS – Network Security | | | | | |
| SC – Software Supply Chain | | | | | |
| DP – Data Protection | | | | | |
| PM – Security Programme Mgmt | | | | | |
| **TOTAL** | **N** | **N** | **N** | **N** | **N** |

**Overall compliance score:** X% *(of assessed controls — excludes N/A and Unassessed)*

### Legend
| Symbol | Meaning |
|--------|---------|
| ✅ | **Compliant** — clear evidence of implementation found in codebase |
| ❌ | **Non-Compliant** — requirement absent, violated, or counterevidence found |
| 🔵 | **N/A** — not applicable given system type or risk level |
| ❓ | **Unassessed** — insufficient evidence; manual review required |

---

## Compliance Matrix

| Control ID | Domain | Level | Control Name | Status | Evidence / Finding | Remediation |
|-----------|--------|-------|-------------|--------|--------------------|-------------|
| AC-1 | Access Control | L0 | Default Credentials | ✅/❌/🔵/❓ | [what was found] | [fix or —] |
| ... | | | | | | |

---

## Gap Report

*Only ❌ Non-Compliant controls appear here. ❓ Unassessed controls are listed in 🟠 below.*

### 🔴 Critical — Level 0 Non-Compliant (must fix before IM8 submission)

| # | Control ID | Level | Finding | Recommended Fix | Effort |
|---|-----------|-------|---------|-----------------|--------|

### 🟠 High — Level 1 Non-Compliant + Level 0 Unassessed

| # | Control ID | Level | Finding | Recommended Fix | Effort |
|---|-----------|-------|---------|-----------------|--------|

### 🟡 Medium — Level 2 Non-Compliant

| # | Control ID | Level | Finding | Recommended Fix | Effort |
|---|-----------|-------|---------|-----------------|--------|

---

## Assessment Notes

- Controls marked ❓ require manual review by a qualified security practitioner
- This is a static code analysis assessment; runtime behaviour and operational controls require separate evaluation
- Re-run `/ssp-review` after addressing 🔴 Critical gaps to verify closure
- Catalog source: [live or fallback — note any domains that could not be fetched]
```

---

### Step 5 — Commit and Push

```bash
git add ASSESSMENT-REPORT.md
git commit -m "Add IM8 compliance assessment report"
git push -u origin <current-branch>
```

---

## Wrap Up

```
## Assessment Complete ✅

System:       [name]
Deployment:   [Cloud / On-Premises / SaaS]   Risk level: [Low / Medium / High / CII]
GenAI:        [Yes / No]                      Dev model:  [In-house / Outsourced]

Controls in scope: N
  ✅ Compliant:      N     ❌ Non-Compliant:  N
  🔵 N/A:            N     ❓ Unassessed:     N
Compliance score: X% (of assessed controls)

File written:
  📊 ASSESSMENT-REPORT.md — Compliance matrix + gap report

Top critical gaps (Level 0 ❌):
  1. [Control ID] — [one-line finding → quick fix]
  2. [Control ID] — ...

Next steps:
  → Address all 🔴 Critical gaps (Level 0 ❌) before IM8 portal submission
  → Review ❓ Unassessed controls with your security team
  → Re-run /ssp-review after remediation to verify closure

⚠️  Static code analysis only. Operational controls (PM, training, incident drills)
    require separate manual verification.
```
