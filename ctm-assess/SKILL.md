---
name: ctm-assess
description: Assess an organisation's Cyber Trust Mark (CTM) preparedness tier against Singapore
  CSA's five-tier certification framework and produce a gap report with a remediation roadmap.
  Use when checking CTM readiness, determining which CTM tier is achievable, identifying
  security gaps against CTM criteria, or preparing for a CSA CTM audit. Trigger on:
  "CTM assessment", "Cyber Trust Mark", "CTM readiness", "ctm-assess", "CTM tier",
  "CSA certification", "cybersecurity mark", "assess CTM", "CTM gap report",
  "CTM preparedness", "how ready am I for CTM".
---

## Context

Ask these five questions before running any commands — do not proceed until all are answered:

- **Q1 Sector:** Financial Services | Healthcare | Government / Public Sector | Retail & E-commerce | Manufacturing | Technology | Critical Information Infrastructure (CII) | Other
- **Q2 Org size:** SME (≤ 200 employees) | Mid-market (201–1 000) | Enterprise (> 1 000)
- **Q3 Target tier:** Tier 1 – Essential | Tier 2 – Enhanced | Tier 3 – Proven | Tier 4 – Distinguished | Tier 5 – Leading | Assess current state (no target)
- **Q4 Cloud footprint:** None (on-prem only) | AWS | Azure | GCP | Multi-cloud | Hybrid
- **Q5 AI / GenAI systems:** Yes — describe briefly | No

After collecting Q1–Q5, invite the user to upload supporting artifacts before proceeding:

> **Optional — upload evidence artifacts now.**
> Attach any documents that demonstrate compliance with CTM domain criteria. Accepted formats: PDF, DOCX, XLSX, images (PNG/JPG), or plain text. Useful uploads include:
>
> | CTM Domain | Example artifacts |
> |---|---|
> | Cyber Governance & Oversight | Information security policy, ISMS scope statement, board risk appetite statement, org chart with security roles |
> | Cyber Risk Management | Risk register, risk assessment report, BIA, threat model |
> | Cyber Security Management | Pentest / vulnerability scan reports, patch management policy, MFA config screenshots, SIEM/logging architecture diagram, backup schedule, DR test results |
> | Supply Chain Management | Vendor risk assessment forms, third-party security questionnaires, supplier contracts with security clauses |
> | Cyber Crisis Management | Incident response plan, BCP/DRP, tabletop exercise records, post-incident review reports |
>
> You may upload multiple files. Type **"no uploads"** to skip and rely on automated scans only.

For each uploaded artifact, extract and record the following before running shell scans:
- File name and stated document type
- Document owner / author and version/date if present
- Which CTM domain(s) and criteria it addresses
- Key evidence statements (policy commitments, control descriptions, test outcomes, dates, scope)
- Any gaps, exceptions, or expiry dates noted within the document itself

Treat uploaded artifacts as primary evidence — they override ❓ (insufficient evidence) for the domains they cover and may upgrade a ❌ to ✅ if the document substantiates the control. Cite every artifact in the report as: *[filename, section/page, date]*.

If a document is password-protected, corrupted, or unreadable, note it as ❓ with reason.

Governance & policy evidence — run all in parallel:
```
find . -not -path './.git/*' \( -iname "security-policy*" -o -iname "infosec-policy*" -o -iname "isms*" -o -iname "acceptable-use*" -o -iname "data-classification*" \) 2>/dev/null | head -10
find . -not -path './.git/*' \( -iname "risk-register*" -o -iname "risk-assessment*" -o -iname "risk-management*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "incident-response*" -o -iname "ir-plan*" -o -iname "runbook*" -o -iname "playbook*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "bcp*" -o -iname "drp*" -o -iname "business-continuity*" -o -iname "disaster-recovery*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "vendor-assessment*" -o -iname "third-party*" -o -iname "supplier*" -o -iname "supply-chain*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "privacy-policy*" -o -iname "pdpa*" -o -iname "data-protection*" \) 2>/dev/null | head -5
```

Technical controls evidence — run all in parallel:
```
find . -not -path './.git/*' \( -name ".pre-commit-config.yaml" -o -name ".gitleaks.toml" -o -name ".secrets.baseline" \) 2>/dev/null
find . -path "./.github/workflows/*.yml" 2>/dev/null | xargs grep -l "semgrep\|codeql\|sonarqube\|trivy\|snyk\|gitleaks\|dependabot\|checkov\|tfsec" 2>/dev/null
find . -not -path './.git/*' \( -name "Dockerfile" -o -name "docker-compose*.yml" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -name "*.tf" -o -name "*.tfvars" \) 2>/dev/null | head -5
find . -not -path './.git/*' -path "./.github/workflows/*.yml" | head -5 | xargs grep -l "mfa\|2fa\|totp\|authy\|authenticator" 2>/dev/null
find . -not -path './.git/*' \( -name "SECURITY.md" -o -name "SECURITY.txt" \) 2>/dev/null
grep -rn "MFA\|multi.factor\|two.factor\|2FA\|TOTP" --include="*.md" --include="*.yaml" --include="*.yml" . 2>/dev/null | head -5
grep -rn "backup\|snapshot\|replication\|s3.*bucket\|blob.*storage\|gcs.*bucket" --include="*.tf" --include="*.yml" --include="*.yaml" . 2>/dev/null | head -5
```

Patch & vulnerability management evidence:
```
find . -not -path './.git/*' \( -name "package-lock.json" -o -name "requirements.txt" -o -name "go.sum" -o -name "Cargo.lock" -o -name "poetry.lock" -o -name "Gemfile.lock" \) 2>/dev/null | head -8
find . -path "./.github/workflows/*.yml" 2>/dev/null | xargs grep -l "dependabot\|renovate\|trivy\|snyk" 2>/dev/null
grep -rn "renovate\|dependabot" --include="*.json" --include="*.yml" . 2>/dev/null | head -5
```

Monitoring & logging evidence:
```
grep -rn "siem\|splunk\|elastic\|cloudwatch\|datadog\|grafana\|prometheus\|sumologic\|sentinel" --include="*.tf" --include="*.yml" --include="*.yaml" --include="*.md" . 2>/dev/null | head -5
grep -rn "winston\|pino\|log4j\|structlog\|zerolog\|zap\|bunyan" --include="*.py" --include="*.ts" --include="*.go" --include="*.java" -l 2>/dev/null
grep -rn "audit.log\|auditd\|cloudtrail\|activity.log\|access.log" --include="*.tf" --include="*.yml" --include="*.yaml" . 2>/dev/null | head -5
```

Access control & identity evidence:
```
grep -rn "rbac\|role.based\|iam\|least.privilege\|zero.trust\|privileged.access\|pam\|ldap\|saml\|oauth\|oidc" --include="*.tf" --include="*.yml" --include="*.yaml" --include="*.md" . 2>/dev/null | head -8
grep -rn "password_policy\|password.policy\|password.complexity\|min_length\|account.lockout" --include="*.tf" --include="*.yml" --include="*.yaml" . 2>/dev/null | head -5
grep -rn "encrypt\|kms\|vault\|hsm\|tls\|ssl\|certificate\|AES\|RSA" --include="*.tf" --include="*.yml" --include="*.yaml" -l 2>/dev/null | head -5
```

Awareness & training evidence:
```
find . -not -path './.git/*' \( -iname "security-awareness*" -o -iname "training*" -o -iname "phishing*" -o -iname "awareness*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "pentest*" -o -iname "penetration-test*" -o -iname "red-team*" -o -iname "vulnerability-report*" \) 2>/dev/null | head -5
grep -rn "ISO.27001\|SOC.2\|PCI.DSS\|NIST\|CIS.Controls\|MAS.TRM\|CSA.STAR" --include="*.md" --include="*.txt" --include="*.yaml" . 2>/dev/null | head -5
```

AI/GenAI controls *(run only if Q5 = Yes)*:
```
grep -r "sanitize\|guardrail\|content_filter\|prompt.injection\|moderate\|llm.guard" --include="*.py" --include="*.ts" -l 2>/dev/null
grep -r "anthropic\|openai\|langchain\|llama_index\|litellm\|transformers\|bedrock\|vertex" --include="*.py" --include="*.ts" -l 2>/dev/null | head -5
```

## Constraints

- Every CTM domain gets exactly one tier rating: T1 ✅ | T2 ✅ | T3 ✅ | T4 ✅ | T5 ✅ or ❌ (not met) or ❓ (insufficient evidence)
- ❌ requires a specific file path or artifact citation confirming absence OR a concrete finding — never a guess
- ❓ only when there is genuinely no evidence in either the repo scans or uploaded artifacts (e.g. training completion records not provided)
- Uploaded artifacts take precedence over shell-scan inferences — a policy document beats a grepped keyword
- Never silently ignore an uploaded file; every artifact must appear in the Evidence Index at the top of the report
- The achieved preparedness tier is the highest tier where ALL domains meet the criteria — a single domain gap caps the tier
- Never recommend "rewrite from scratch" or "rebuild your security program" — every gap gets a specific, actionable fix with effort estimate (hours/days/weeks)
- Maximum 15 gap findings, ordered by CTM tier impact (Tier 1 gaps before Tier 2, etc.)
- Sector-specific risks from Q1 must be called out (e.g. MAS TRM for Financial Services, MOH for Healthcare)
- If Q5 = Yes (AI/GenAI), AI governance controls are mandatory at Tier 3+; flag their absence explicitly
- Always cite the specific CTM domain and criterion number in every finding

## Structure

1. **Evidence Index** — table of every artifact used: Filename | Document Type | Date/Version | CTM Domain(s) | Evidence Quality (Primary / Supporting / Unreadable); list "none uploaded" if user skipped
2. **Executive summary** — 3 sentences: current preparedness tier, headline blocker, top recommended action
3. **CTM Tier Readiness Scorecard** — table: Domain × Tier 1–5 columns, cell = ✅ / ❌ / ❓, evidence source tagged (📄 artifact | 🔍 scan | ❓ gap); current tier verdict and target tier gap highlighted
4. **Domain-by-domain findings** — for each of the 5 CTM domains:
   - Tier achieved (cite artifact *[filename, section/page]* or scan finding as evidence)
   - Specific gaps blocking the next tier, distinguishing missing-document gaps from missing-control gaps
5. **Gap remediation roadmap** — ordered table: Gap | CTM Domain | Tier Impact | Finding | Recommended Fix | Effort
6. **Sector-specific callouts** — risks and regulatory overlaps relevant to Q1 sector (e.g. MAS TRM, MOH guidelines, PDPA, CSA CII)
7. **Next-steps checklist** — ≤ 10 items grouped by Quick Win (< 1 week) / Sprint (1–4 weeks) / Project (> 1 month); flag items that can be closed by uploading an existing document the user hasn't shared yet
8. **Write output** to `CTM-assessment.md` at repo root (or alongside `SSP.md` if present)
9. **Commit:** `git add CTM-assessment.md && git commit -m "Add CTM preparedness assessment report" && git push`
