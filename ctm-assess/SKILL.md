---
name: ctm-assess
description: Assess an organisation's Cyber Trust Mark (CTM) preparedness tier against
  Singapore CSA's SS 712:2025 ("Tiered cybersecurity standards for organisations") and
  produce a gap report with a remediation roadmap. Use when checking CTM readiness,
  determining which of the five named tiers is achievable, identifying gaps against the
  22 official cybersecurity preparedness domains, or preparing for a CSA-appointed
  certification body audit. Trigger on: "CTM assessment", "Cyber Trust Mark", "CTM
  readiness", "ctm-assess", "SS 712", "CSA certification", "Supporter tier",
  "Practitioner tier", "Promoter tier", "Performer tier", "Advocate tier",
  "cybersecurity preparedness tier", "CTM gap report", "CTM preparedness",
  "how ready am I for CTM".
---

## Context

Ask these five questions before running any commands — do not proceed until all are answered:

- **Q1 Sector:** Financial Services | Healthcare | Government / Public Sector | Retail & E-commerce | Manufacturing | Technology | Critical Information Infrastructure (CII) | Other
- **Q2 Org size:** SME (≤ 200 employees) | Mid-market (201–1 000) | Enterprise (> 1 000)
- **Q3 Target tier** *(per SS 712:2025)*: Supporter (10 domains) | Practitioner (13 domains) | Promoter (16 domains) | Performer (19 domains) | Advocate (22 domains) | Assess current state (use CSA risk assessment to determine)
- **Q4 Technology scope** *(select all that apply)*: Traditional IT only | Cloud (specify provider) | Operational Technology (OT) / ICS / SCADA | AI / ML systems
- **Q5 Software development in-house:** Yes (triggers Secure SDLC domain) | No

After collecting Q1–Q5, invite the user to upload supporting artifacts before proceeding:

> **Optional — upload evidence artifacts now.**
> Attach any documents that demonstrate compliance with CTM domain criteria. Accepted formats: PDF, DOCX, XLSX, images (PNG/JPG), or plain text. Useful uploads include:
>
> | CTM Domain Cluster | Example artifacts |
> |---|---|
> | **Cyber Governance and Oversight** (Governance · Policies & Procedures · Risk Management · Cyber Strategy · Compliance · Audit) | Information security policy, risk register, cyber strategy document, compliance register, internal audit report, board cyber risk appetite statement |
> | **Cyber Education** (Training and Awareness) | Staff security training completion records, phishing simulation results, awareness programme schedule |
> | **Information Asset Protection** (Asset Management · Data Protection & Privacy · Backups · BYOD · System Security · Anti-virus/Anti-malware · Secure SDLC) | Asset inventory, data classification policy, backup schedule and test results, BYOD policy, hardening baselines, AV/EDR configuration, SDLC security gates |
> | **Secure Access and Environment** (Access Control · Cyber Threat Management · Third-party Risk & Oversight · Vulnerability Assessment · Physical/Environmental Security · Network Security) | IAM policy, MFA screenshots, threat intel subscription evidence, vendor risk assessments, vulnerability scan / pentest report, physical security policy, network diagram and firewall rules |
> | **Cybersecurity Resilience** (Incident Response · Business Continuity / Disaster Recovery) | Incident response plan, BCP/DRP, tabletop exercise records, DR test results, post-incident review reports |
> | **Cloud Security** *(if Q4 includes Cloud)* | Cloud security architecture, shared responsibility matrix, CSPM configuration |
> | **OT Security** *(if Q4 includes OT)* | OT/ICS asset inventory, OT network segmentation diagram, OT-specific incident response plan |
> | **AI Security** *(if Q4 includes AI)* | AI governance policy, model risk assessment, AI incident response playbook |
>
> You may upload multiple files. Type **"no uploads"** to skip and rely on automated scans only.

For each uploaded artifact, extract and record the following before running shell scans:
- File name and stated document type
- Document owner / author and version/date if present
- Which of the 22 CTM domains (and cluster) it addresses
- Key evidence statements (policy commitments, control descriptions, test outcomes, dates, scope)
- Any gaps, exceptions, or expiry dates noted within the document itself

Treat uploaded artifacts as primary evidence — they override ❓ (insufficient evidence) for the domains they cover and may upgrade ❌ to ✅ if the document substantiates the control. Cite every artifact in the report as: *[filename, section/page, date]*.

If a document is password-protected, corrupted, or unreadable, note it as ❓ with reason.

Governance & oversight evidence — run all in parallel:
```
find . -not -path './.git/*' \( -iname "security-policy*" -o -iname "infosec-policy*" -o -iname "isms*" -o -iname "cyber-strategy*" -o -iname "acceptable-use*" \) 2>/dev/null | head -10
find . -not -path './.git/*' \( -iname "risk-register*" -o -iname "risk-assessment*" -o -iname "risk-management*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "compliance*" -o -iname "audit-report*" -o -iname "internal-audit*" \) 2>/dev/null | head -5
grep -rn "ISO.27001\|SOC.2\|PCI.DSS\|NIST\|CIS.Controls\|MAS.TRM\|CSA.STAR\|SS.712" --include="*.md" --include="*.txt" --include="*.yaml" . 2>/dev/null | head -5
```

Training and awareness evidence:
```
find . -not -path './.git/*' \( -iname "security-awareness*" -o -iname "training*" -o -iname "phishing*" -o -iname "awareness-programme*" \) 2>/dev/null | head -5
```

Information asset protection evidence — run all in parallel:
```
find . -not -path './.git/*' \( -iname "asset-inventory*" -o -iname "asset-register*" -o -iname "cmdb*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "data-classification*" -o -iname "data-handling*" -o -iname "privacy-policy*" -o -iname "pdpa*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "backup*" -o -iname "disaster-recovery*" -o -iname "drp*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "byod*" -o -iname "mobile-device*" -o -iname "mdm*" \) 2>/dev/null | head -3
find . -not -path './.git/*' \( -name ".pre-commit-config.yaml" -o -name ".gitleaks.toml" -o -name ".secrets.baseline" \) 2>/dev/null
find . -path "./.github/workflows/*.yml" 2>/dev/null | xargs grep -l "semgrep\|codeql\|sonarqube\|trivy\|snyk\|gitleaks\|dependabot\|checkov\|tfsec\|sast\|dast" 2>/dev/null
grep -rn "backup\|snapshot\|replication\|s3.*bucket\|blob.*storage\|gcs.*bucket" --include="*.tf" --include="*.yml" --include="*.yaml" . 2>/dev/null | head -5
```

Secure access and environment evidence — run all in parallel:
```
grep -rn "rbac\|role.based\|iam\|least.privilege\|zero.trust\|privileged.access\|pam\|ldap\|saml\|oauth\|oidc" --include="*.tf" --include="*.yml" --include="*.yaml" --include="*.md" . 2>/dev/null | head -8
grep -rn "MFA\|multi.factor\|two.factor\|2FA\|TOTP" --include="*.md" --include="*.yaml" --include="*.yml" . 2>/dev/null | head -5
grep -rn "password_policy\|password.complexity\|min_length\|account.lockout" --include="*.tf" --include="*.yml" --include="*.yaml" . 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "vendor-assessment*" -o -iname "third-party*" -o -iname "supplier*" -o -iname "supply-chain*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "pentest*" -o -iname "penetration-test*" -o -iname "vulnerability-report*" -o -iname "vulnerability-scan*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "physical-security*" -o -iname "clean-desk*" -o -iname "cctv*" -o -iname "access-card*" \) 2>/dev/null | head -3
grep -rn "siem\|splunk\|elastic\|cloudwatch\|datadog\|sentinel\|threat.intel\|ioc\|soc" --include="*.tf" --include="*.yml" --include="*.yaml" --include="*.md" . 2>/dev/null | head -5
grep -rn "encrypt\|kms\|vault\|tls\|ssl\|certificate\|firewall\|waf\|network.segment\|vlan\|dmz" --include="*.tf" --include="*.yml" --include="*.yaml" -l 2>/dev/null | head -5
find . -path "./.github/workflows/*.yml" 2>/dev/null | xargs grep -l "dependabot\|renovate\|trivy\|snyk" 2>/dev/null
```

Cybersecurity resilience evidence:
```
find . -not -path './.git/*' \( -iname "incident-response*" -o -iname "ir-plan*" -o -iname "runbook*" -o -iname "playbook*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "bcp*" -o -iname "business-continuity*" -o -iname "drp*" -o -iname "disaster-recovery*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "tabletop*" -o -iname "exercise*" -o -iname "post-incident*" \) 2>/dev/null | head -3
```

Cloud security evidence *(run only if Q4 includes Cloud)*:
```
find . -not -path './.git/*' \( -name "*.tf" -o -name "*.cfn.yml" -o -name "*.cfn.yaml" \) 2>/dev/null | head -5
find . -path "./.github/workflows/*.yml" 2>/dev/null | xargs grep -l "checkov\|tfsec\|prowler\|cloud.custodian\|cspm" 2>/dev/null
grep -rn "shared.responsibility\|cloud.security\|cspm\|cwpp\|ciem" --include="*.md" --include="*.yaml" . 2>/dev/null | head -5
```

OT security evidence *(run only if Q4 includes OT)*:
```
find . -not -path './.git/*' \( -iname "ot-security*" -o -iname "ics*" -o -iname "scada*" -o -iname "ot-network*" \) 2>/dev/null | head -5
grep -rn "IEC.62443\|NERC.CIP\|ot.segment\|air.gap\|purdue.model" --include="*.md" --include="*.yaml" --include="*.txt" . 2>/dev/null | head -5
```

AI security evidence *(run only if Q4 includes AI)*:
```
grep -r "sanitize\|guardrail\|content_filter\|prompt.injection\|moderate\|llm.guard\|model.risk" --include="*.py" --include="*.ts" -l 2>/dev/null
grep -r "anthropic\|openai\|langchain\|llama_index\|litellm\|transformers\|bedrock\|vertex" --include="*.py" --include="*.ts" -l 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "ai-governance*" -o -iname "model-risk*" -o -iname "ai-policy*" \) 2>/dev/null | head -3
```

## Constraints

- **Official standard:** SS 712:2025 "Tiered cybersecurity standards for organisations" published by CSA. The full criteria are in the paid standard; this assessment uses the publicly documented 22-domain, 5-tier framework.
- **Tier names are fixed:** Supporter (10 domains) → Practitioner (13) → Promoter (16) → Performer (19) → Advocate (22). Never invent alternative tier names.
- **Domain names are fixed:** Use only the 22 officially named domains across the 5 clusters (Cyber Governance and Oversight, Cyber Education, Information Asset Protection, Secure Access and Environment, Cybersecurity Resilience) as stated by CSA. Do not rename or merge domains.
- Every domain gets exactly one status: ✅ (evidenced) | ❌ (gap found) | ❓ (insufficient evidence) — no blends
- ❌ requires a specific file path or artifact citation as evidence of absence or failure — never a guess
- ❓ only when there is genuinely no evidence in either the repo scans or uploaded artifacts
- Uploaded artifacts are primary evidence — they override ❓ and may upgrade ❌ to ✅ if substantiated
- Never silently ignore an uploaded file; every artifact must appear in the Evidence Index
- The achieved tier is the highest tier where **all** in-scope domains are ✅ — one ❌ blocks the tier
- Q4 scope gates additional domains: Cloud Security, OT Security, and AI Security (2025 additions to SS 712) are only assessed if the corresponding technology is in scope
- Q5 = Yes gates the Secure SDLC domain assessment
- CII sectors (Q1 = CII) must be assessed against Tier 5 (Advocate) as CSA mandates Level 5 for CII owners by end-2027
- Never recommend "rewrite from scratch" — every gap gets a specific, actionable fix with effort estimate
- Maximum 15 gap findings, ordered by tier impact (domains blocking lower tiers first)
- Sector-specific risks from Q1 must be called out (e.g. MAS TRM for Financial Services, MOH guidelines for Healthcare)
- Always cite the specific CTM domain name and cluster in every finding

## Structure

1. **Evidence Index** — table: Filename | Document Type | Date/Version | CTM Domain(s) | Evidence Quality (Primary / Supporting / Unreadable); list "none uploaded" if user skipped
2. **Executive summary** — 3 sentences: current tier verdict (use official tier name), headline blocking domain, top recommended action
3. **CTM Tier Readiness Scorecard** — table: 22 domains as rows grouped by cluster; columns = Supporter / Practitioner / Promoter / Performer / Advocate; cell = ✅ / ❌ / ❓ / N/S (not in scope); evidence source tagged (📄 artifact | 🔍 scan | ❓ gap); current tier highlighted and target tier gap marked
4. **Domain-by-domain findings** — for each assessed domain (grouped by cluster):
   - Status (cite artifact *[filename, section/page]* or scan finding)
   - Specific gaps with actionable fix
5. **Gap remediation roadmap** — ordered table: Gap | CTM Domain | Cluster | Tier Blocked | Finding | Recommended Fix | Effort
6. **Sector-specific callouts** — regulatory overlaps relevant to Q1 (e.g. MAS TRM, MOH, PDPA, CSA CII mandate)
7. **Next-steps checklist** — ≤ 10 items grouped by Quick Win (< 1 week) / Sprint (1–4 weeks) / Project (> 1 month); flag items closable by uploading an existing document
8. **Write output** to `CTM-assessment.md` at repo root (or alongside `SSP.md` if present)
9. **Commit:** `git add CTM-assessment.md && git commit -m "Add CTM preparedness assessment report (SS 712:2025)" && git push`
