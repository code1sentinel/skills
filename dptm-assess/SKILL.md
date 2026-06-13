---
name: dptm-assess
description: Assess an organisation's Data Protection Trust Mark (DPTM) readiness against
  Singapore IMDA's certification framework and produce a gap report with a remediation
  roadmap aligned to the Personal Data Protection Act (PDPA). Use when checking DPTM
  readiness, identifying PDPA compliance gaps, evaluating data protection practices,
  or preparing for an IMDA DPTM audit. Trigger on: "DPTM assessment", "Data Protection
  Trust Mark", "DPTM readiness", "dptm-assess", "PDPA compliance", "IMDA certification",
  "data protection assessment", "DPTM gap report", "PDPA gap", "assess DPTM",
  "data protection mark", "how ready am I for DPTM".
---

## Context

Ask these five questions before running any commands — do not proceed until all are answered:

- **Q1 Sector:** Financial Services | Healthcare | Education | Retail & E-commerce | HR / Recruitment | Government-Linked | Technology / SaaS | Other
- **Q2 Org size:** SME (≤ 200 employees) | Mid-market (201–1 000) | Enterprise (> 1 000)
- **Q3 Personal data categories handled:** *(select all that apply)* Contact info (name/email/phone) | NRIC / FIN / Passport | Financial data | Health / medical records | Biometric data | Children's data (< 18 yrs) | Sensitive / special categories (race, religion, political views) | None of the above
- **Q4 Cross-border data transfers:** Yes — list destination countries | No
- **Q5 Automated decision-making / AI processing of personal data:** Yes — describe briefly | No

After collecting Q1–Q5, invite the user to upload supporting artifacts before proceeding:

> **Optional — upload evidence artifacts now.**
> Attach any documents that demonstrate compliance with DPTM domain criteria. Accepted formats: PDF, DOCX, XLSX, images (PNG/JPG), or plain text. Useful uploads include:
>
> | DPTM Domain | Example artifacts |
> |---|---|
> | Management & Governance | Data protection policy, DPO appointment letter or org chart, board/management data governance commitment, data inventory / Record of Processing Activities (ROPA), data classification policy |
> | Operationalisation of Policies | Consent collection forms or screenshots, data retention & disposal schedule, DPIA (Data Protection Impact Assessment), cross-border transfer agreements (standard contractual clauses), data accuracy procedures |
> | Communication & Transparency | Published privacy notice / policy URL or document, individual access & correction request procedures, marketing opt-out mechanism screenshots, data breach notification templates |
> | Accountability & Audit | Internal audit report, staff PDPA training completion records, data breach incident log or post-breach review, complaints register, vendor / data intermediary contracts with data protection clauses, existing certifications (ISO 27001, SOC 2) |
>
> You may upload multiple files. Type **"no uploads"** to skip and rely on automated scans only.

For each uploaded artifact, extract and record the following before running shell scans:
- File name and stated document type
- Document owner / author and version/date if present
- Which DPTM domain(s) and PDPA obligation(s) it addresses
- Key evidence statements (DPO name/role, consent mechanisms, retention periods, transfer safeguards, training dates, audit scope)
- Any gaps, exceptions, expiry dates, or action items noted within the document itself

Treat uploaded artifacts as primary evidence — they override ❓ (insufficient evidence) for the domains they cover and may upgrade a ❌ to ✅ if the document substantiates the obligation. Cite every artifact in the report as: *[filename, section/page, date]*.

If a document is password-protected, corrupted, or unreadable, note it as ❓ with reason.

Governance & policy evidence — run all in parallel:
```
find . -not -path './.git/*' \( -iname "privacy-policy*" -o -iname "data-protection-policy*" -o -iname "pdpa*" -o -iname "personal-data*" -o -iname "data-governance*" \) 2>/dev/null | head -10
find . -not -path './.git/*' \( -iname "dpo*" -o -iname "data-protection-officer*" -o -iname "privacy-officer*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "ropa*" -o -iname "record-of-processing*" -o -iname "data-inventory*" -o -iname "data-mapping*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "dpia*" -o -iname "pia*" -o -iname "privacy-impact*" -o -iname "data-impact*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "data-classification*" -o -iname "data-handling*" -o -iname "information-classification*" \) 2>/dev/null | head -5
```

Consent & collection evidence:
```
grep -rn "consent\|opt.in\|opt.out\|checkbox\|agree\|permission\|marketing.preference" --include="*.py" --include="*.ts" --include="*.js" --include="*.html" --include="*.tsx" -l 2>/dev/null | head -10
grep -rn "consent\|opt.in\|opt.out\|unsubscribe\|withdraw" --include="*.md" --include="*.yaml" --include="*.yml" . 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "consent-form*" -o -iname "consent-banner*" -o -iname "cookie-policy*" -o -iname "cookie-consent*" \) 2>/dev/null | head -5
grep -rn "purpose\|collection.purpose\|use.of.data\|data.use" --include="*.py" --include="*.ts" --include="*.js" -l 2>/dev/null | head -5
```

Data retention & disposal evidence:
```
find . -not -path './.git/*' \( -iname "retention*" -o -iname "disposal*" -o -iname "deletion-policy*" -o -iname "data-lifecycle*" \) 2>/dev/null | head -5
grep -rn "retention\|ttl\|expire\|purge\|delete.*after\|scheduled.*delete\|data.*lifecycle" --include="*.py" --include="*.ts" --include="*.go" --include="*.yaml" --include="*.tf" . 2>/dev/null | head -8
grep -rn "DELETE.*WHERE.*date\|purge\|archive\|soft.delete\|hard.delete" --include="*.py" --include="*.ts" --include="*.go" --include="*.sql" . 2>/dev/null | head -5
```

Cross-border transfer evidence *(run only if Q4 = Yes)*:
```
grep -rn "cross.border\|transfer.*overseas\|international.*transfer\|adequacy\|standard.contractual\|SCC\|BCR\|binding.corporate" --include="*.md" --include="*.yaml" --include="*.txt" . 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "SCC*" -o -iname "DPA*" -o -iname "data-processing-agreement*" -o -iname "transfer-impact*" \) 2>/dev/null | head -5
```

Individual rights & transparency evidence:
```
grep -rn "access.request\|correction.request\|data.subject\|right.to\|erasure\|portability\|withdraw.consent" --include="*.py" --include="*.ts" --include="*.js" --include="*.md" . 2>/dev/null | head -8
find . -not -path './.git/*' \( -iname "privacy-notice*" -o -iname "data-subject*" -o -iname "rights-request*" -o -iname "dsar*" \) 2>/dev/null | head -5
grep -rn "contact.*privacy\|privacy@\|dpo@\|dataprotection@\|feedback.*personal" --include="*.md" --include="*.html" --include="*.txt" . 2>/dev/null | head -5
```

Security controls for personal data — run all in parallel:
```
grep -rn "encrypt\|kms\|vault\|aes\|tls\|ssl\|pseudonymis\|anonymis\|mask\|tokeniz" --include="*.py" --include="*.ts" --include="*.go" --include="*.tf" --include="*.yaml" -l 2>/dev/null | head -8
grep -rn "rbac\|role.based\|iam\|least.privilege\|access.control\|pii.*access\|sensitive.*access" --include="*.py" --include="*.ts" --include="*.tf" --include="*.yaml" . 2>/dev/null | head -5
grep -rn "audit.log\|access.log\|data.access.*log\|cloudtrail\|cloudwatch" --include="*.tf" --include="*.yaml" --include="*.yml" . 2>/dev/null | head -5
grep -rn "nric\|fin\|passport\|email\|phone\|dob\|date.of.birth\|address\|full.name\|health\|medical" --include="*.py" --include="*.ts" --include="*.go" --include="*.java" -l 2>/dev/null | head -10
```

Breach & incident management evidence:
```
find . -not -path './.git/*' \( -iname "breach*" -o -iname "incident*" -o -iname "data-breach*" -o -iname "security-incident*" \) 2>/dev/null | head -5
grep -rn "breach.notification\|notify.*pdpc\|72.hour\|mandatory.notification\|reportable.breach" --include="*.md" --include="*.yaml" --include="*.txt" . 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "runbook*" -o -iname "playbook*" -o -iname "incident-response*" \) 2>/dev/null | head -5
```

Vendor & third-party data management evidence:
```
find . -not -path './.git/*' \( -iname "vendor*" -o -iname "third-party*" -o -iname "data-intermediary*" -o -iname "processor*" \) 2>/dev/null | head -5
grep -rn "data.intermediary\|data.processor\|processor.agreement\|subprocessor\|vendor.*pdpa\|third.party.*data" --include="*.md" --include="*.yaml" --include="*.txt" . 2>/dev/null | head -5
```

Training & awareness evidence:
```
find . -not -path './.git/*' \( -iname "training*" -o -iname "awareness*" -o -iname "pdpa-training*" -o -iname "data-protection-training*" \) 2>/dev/null | head -5
grep -rn "PDPA\|Personal Data Protection Act\|IMDA\|PDPC\|data protection awareness" --include="*.md" --include="*.txt" --include="*.yaml" . 2>/dev/null | head -5
```

AI / automated decision-making controls *(run only if Q5 = Yes)*:
```
grep -r "automated.decision\|profiling\|scoring\|recommendation.*personal\|personaliz\|personalise" --include="*.py" --include="*.ts" --include="*.go" -l 2>/dev/null | head -5
grep -r "explainability\|fairness\|bias\|human.review\|human.in.the.loop\|override" --include="*.py" --include="*.ts" -l 2>/dev/null
grep -r "anthropic\|openai\|langchain\|llama_index\|litellm\|transformers\|bedrock\|vertex" --include="*.py" --include="*.ts" -l 2>/dev/null | head -5
```

## Constraints

- Every DPTM domain gets exactly one status: ✅ (compliant) | ❌ (gap found) | ❓ (insufficient evidence) — no blends
- ❌ requires a specific file path or artifact citation as evidence of absence or failure — never a guess
- ❓ only when there is genuinely no evidence in either the repo scans or uploaded artifacts (e.g. consent records held in a CRM not accessible here)
- Uploaded artifacts are primary evidence — they override ❓ for the domains they cover and may upgrade ❌ to ✅ if the document substantiates the PDPA obligation
- Never silently ignore an uploaded file; every artifact must appear in the Evidence Index at the top of the report
- The DPTM certification verdict requires ALL four domains to be compliant — a single domain gap blocks certification
- Every gap must cite the specific PDPA obligation (e.g. s13 Purpose Limitation, s24 Protection Obligation, s26 Data Breach Notification) or DPTM criterion
- Never recommend "overhaul your data processes" — every gap gets a specific, actionable fix with effort estimate (hours/days/weeks)
- Maximum 15 gap findings, ordered by PDPA legal risk (mandatory obligations before best-practice gaps)
- Q3 personal data categories must inform the severity of findings — health, biometric, and children's data gaps are always rated Critical regardless of tier
- If Q4 = Yes (cross-border transfers), adequacy and contractual safeguard gaps are mandatory findings
- If Q5 = Yes (AI/automated decisions), algorithmic transparency and human-review mechanisms must be assessed; flag absence explicitly

## Structure

1. **Evidence Index** — table of every artifact used: Filename | Document Type | Date/Version | DPTM Domain(s) | Evidence Quality (Primary / Supporting / Unreadable); list "none uploaded" if user skipped
2. **Executive summary** — 3 sentences: DPTM certification readiness verdict, headline compliance blocker, top recommended action
3. **DPTM Readiness Scorecard** — table: DPTM Domain × Obligation columns, cell = ✅ / ❌ / ❓, evidence source tagged (📄 artifact | 🔍 scan | ❓ gap); overall certification verdict (Ready / Conditional / Not Ready)
4. **Domain-by-domain findings** — for each of the 4 DPTM domains:
   - Compliance status (cite artifact *[filename, section/page]* or scan finding)
   - PDPA obligations met and specific gaps with clause reference
   - Distinction between missing-document gaps and missing-control gaps
5. **Gap remediation roadmap** — ordered table: Gap | DPTM Domain | PDPA Clause | Severity (Critical / High / Medium) | Finding | Recommended Fix | Effort
6. **Sector-specific callouts** — regulatory overlaps relevant to Q1 sector (e.g. MAS Notice 644 for Financial Services, MOH data governance for Healthcare, PDPC Advisory Guidelines, COPPA-equivalent for children's data)
7. **Next-steps checklist** — ≤ 10 items grouped by Quick Win (< 1 week) / Sprint (1–4 weeks) / Project (> 1 month); flag items that can be closed by uploading an existing document the user hasn't shared yet
8. **Write output** to `DPTM-assessment.md` at repo root (or alongside `SSP.md` if present)
9. **Commit:** `git add DPTM-assessment.md && git commit -m "Add DPTM preparedness assessment report" && git push`
