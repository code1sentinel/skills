---
name: dptm-assess
description: Assess an organisation's Data Protection Trustmark (DPTM) readiness against
  Singapore Standard SS 714:2025 ("Data protection trustmark") jointly developed by IMDA
  and PDPC, and produce a gap report aligned to the four certification principles and the
  11 obligations of the Personal Data Protection Act (PDPA). Use when checking DPTM
  readiness, identifying PDPA compliance gaps, evaluating data protection practices, or
  preparing for an IMDA-appointed certification body audit. Trigger on: "DPTM assessment",
  "Data Protection Trustmark", "DPTM readiness", "dptm-assess", "SS 714", "PDPA compliance",
  "IMDA certification", "data protection assessment", "DPTM gap report", "PDPA gap",
  "assess DPTM", "data protection mark", "how ready am I for DPTM".
---

## Context

Ask these five questions before running any commands — do not proceed until all are answered:

- **Q1 Sector:** Financial Services | Healthcare | Education | Retail & E-commerce | HR / Recruitment | Government-Linked Company | Technology / SaaS | Other
- **Q2 Org size:** SME (≤ 200 employees) | Mid-market (201–1 000) | Enterprise (> 1 000)
- **Q3 Personal data categories handled** *(select all that apply)*: Contact info (name / email / phone) | NRIC / FIN / Passport | Financial data | Health / medical records | Biometric data | Children's data (< 18 yrs) | Race / religion / political views (sensitive categories) | None of the above
- **Q4 Cross-border data transfers:** Yes — list destination countries | No
- **Q5 Automated decision-making or AI processing of personal data:** Yes — describe briefly | No

After collecting Q1–Q5, invite the user to upload supporting artifacts before proceeding:

> **Optional — upload evidence artifacts now.**
> Attach any documents that demonstrate compliance with DPTM certification criteria under SS 714:2025. Accepted formats: PDF, DOCX, XLSX, images (PNG/JPG), or plain text. Useful uploads include:
>
> | SS 714:2025 Principle | PDPA Obligation(s) | Example artifacts |
> |---|---|---|
> | **Clause 6 — Governance and Transparency** | Accountability (s11) · Openness (s20) | DPO appointment letter or job description, data protection policy, data breach management plan, data inventory / Record of Processing Activities (ROPA), data protection impact assessment (DPIA), management commitment statement |
> | **Clause 7 — Management of Personal Data** | Consent (s13–s17) · Purpose Limitation (s18) · Notification (s20) · Transfer Limitation (s26) · Do Not Call (Part IX) | Consent collection forms or screenshots, purpose statements, privacy notice, cross-border transfer agreements (standard contractual clauses), DNC registry compliance evidence |
> | **Clause 8 — Care of Personal Data** | Protection (s24) · Accuracy (s23) · Retention Limitation (s25) · Data Intermediary (s4) | Security controls documentation, data accuracy procedures, retention and disposal schedule, data intermediary / vendor contracts with data protection clauses |
> | **Clause 9 — Safeguarding Individuals' Rights** | Access and Correction (s21–s22) · Withdrawal of Consent (s16) · Data Breach Notification (s26B–s26K) | Access and correction request procedures, consent withdrawal mechanism screenshots, data breach notification procedure, incident log or post-breach review report, staff PDPA training completion records |
>
> You may upload multiple files. Type **"no uploads"** to skip and rely on automated scans only.

For each uploaded artifact, extract and record the following before running shell scans:
- File name and stated document type
- Document owner / author and version/date if present
- Which SS 714:2025 clause(s) and PDPA obligation(s) it addresses
- Key evidence statements (DPO name/role, consent mechanisms, purpose statements, retention periods, transfer safeguards, training dates, audit scope, breach response timelines)
- Any gaps, exceptions, expiry dates, or outstanding action items noted within the document itself

Treat uploaded artifacts as primary evidence — they override ❓ (insufficient evidence) for the clauses they cover and may upgrade ❌ to ✅ if the document substantiates the PDPA obligation. Cite every artifact in the report as: *[filename, section/page, date]*.

If a document is password-protected, corrupted, or unreadable, note it as ❓ with reason.

Clause 6 — Governance and Transparency evidence — run all in parallel:
```
find . -not -path './.git/*' \( -iname "data-protection-policy*" -o -iname "privacy-policy*" -o -iname "pdpa*" -o -iname "personal-data-policy*" -o -iname "data-governance*" \) 2>/dev/null | head -10
find . -not -path './.git/*' \( -iname "dpo*" -o -iname "data-protection-officer*" -o -iname "privacy-officer*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "ropa*" -o -iname "record-of-processing*" -o -iname "data-inventory*" -o -iname "data-mapping*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "dpia*" -o -iname "pia*" -o -iname "privacy-impact*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "breach-management*" -o -iname "data-breach-plan*" -o -iname "breach-response*" \) 2>/dev/null | head -5
grep -rn "PDPA\|Personal Data Protection Act\|IMDA\|PDPC\|data protection officer\|DPO" --include="*.md" --include="*.txt" --include="*.yaml" . 2>/dev/null | head -5
```

Clause 7 — Management of Personal Data evidence — run all in parallel:
```
grep -rn "consent\|opt.in\|opt.out\|checkbox\|agree\|permission\|marketing.preference\|unsubscribe\|withdraw" --include="*.py" --include="*.ts" --include="*.js" --include="*.html" --include="*.tsx" -l 2>/dev/null | head -10
grep -rn "purpose\|collection.purpose\|use.of.data\|data.use\|purpose.limitation" --include="*.py" --include="*.ts" --include="*.js" --include="*.md" -l 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "consent-form*" -o -iname "consent-banner*" -o -iname "cookie-policy*" -o -iname "cookie-consent*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "privacy-notice*" -o -iname "privacy-statement*" -o -iname "notification*" \) 2>/dev/null | head -5
grep -rn "DNC\|do.not.call\|marketing.opt" --include="*.py" --include="*.ts" --include="*.md" . 2>/dev/null | head -5
```

Clause 7 — Cross-border transfer evidence *(run only if Q4 = Yes)*:
```
find . -not -path './.git/*' \( -iname "SCC*" -o -iname "DPA*" -o -iname "data-processing-agreement*" -o -iname "transfer-impact*" \) 2>/dev/null | head -5
grep -rn "cross.border\|transfer.*overseas\|international.*transfer\|adequacy\|standard.contractual\|binding.corporate\|transfer.limitation" --include="*.md" --include="*.yaml" --include="*.txt" . 2>/dev/null | head -5
```

Clause 8 — Care of Personal Data evidence — run all in parallel:
```
grep -rn "encrypt\|kms\|vault\|aes\|tls\|ssl\|pseudonymis\|anonymis\|mask\|tokeniz\|hash" --include="*.py" --include="*.ts" --include="*.go" --include="*.tf" --include="*.yaml" -l 2>/dev/null | head -8
grep -rn "rbac\|role.based\|iam\|least.privilege\|access.control\|pii.*access\|sensitive.*access" --include="*.py" --include="*.ts" --include="*.tf" --include="*.yaml" . 2>/dev/null | head -5
grep -rn "audit.log\|access.log\|data.access.*log\|cloudtrail\|cloudwatch\|activity.log" --include="*.tf" --include="*.yaml" --include="*.yml" . 2>/dev/null | head -5
grep -rn "retention\|ttl\|expire\|purge\|delete.*after\|scheduled.*delete\|data.*lifecycle" --include="*.py" --include="*.ts" --include="*.go" --include="*.yaml" --include="*.tf" . 2>/dev/null | head -8
grep -rn "DELETE.*WHERE.*date\|purge\|archive\|soft.delete\|hard.delete" --include="*.py" --include="*.ts" --include="*.go" --include="*.sql" . 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "retention*" -o -iname "disposal*" -o -iname "deletion-policy*" -o -iname "data-lifecycle*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "vendor*" -o -iname "data-intermediary*" -o -iname "processor*" -o -iname "third-party-data*" \) 2>/dev/null | head -5
grep -rn "nric\|fin\|passport\|email\|phone\|dob\|date.of.birth\|address\|full.name\|health\|medical\|biometric" --include="*.py" --include="*.ts" --include="*.go" --include="*.java" -l 2>/dev/null | head -10
```

Clause 9 — Safeguarding Individuals' Rights evidence — run all in parallel:
```
grep -rn "access.request\|correction.request\|data.subject\|right.to.access\|right.to.correct\|withdrawal.of.consent\|dsar" --include="*.py" --include="*.ts" --include="*.js" --include="*.md" . 2>/dev/null | head -8
find . -not -path './.git/*' \( -iname "dsar*" -o -iname "access-request*" -o -iname "correction-request*" -o -iname "data-subject*" \) 2>/dev/null | head -5
grep -rn "breach.notification\|notify.*pdpc\|3.calendar.days\|mandatory.notification\|reportable.breach\|s26B\|s26" --include="*.md" --include="*.yaml" --include="*.txt" . 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "breach*" -o -iname "incident-log*" -o -iname "data-breach*" \) 2>/dev/null | head -5
find . -not -path './.git/*' \( -iname "training*" -o -iname "pdpa-training*" -o -iname "data-protection-training*" \) 2>/dev/null | head -5
grep -rn "contact.*privacy\|privacy@\|dpo@\|dataprotection@" --include="*.md" --include="*.html" --include="*.txt" . 2>/dev/null | head -5
```

AI / automated decision-making evidence *(run only if Q5 = Yes)*:
```
grep -r "automated.decision\|profiling\|scoring\|recommendation.*personal\|personaliz\|personalise" --include="*.py" --include="*.ts" --include="*.go" -l 2>/dev/null | head -5
grep -r "explainability\|fairness\|bias\|human.review\|human.in.the.loop\|override\|model.risk" --include="*.py" --include="*.ts" -l 2>/dev/null
grep -r "anthropic\|openai\|langchain\|llama_index\|litellm\|transformers\|bedrock\|vertex" --include="*.py" --include="*.ts" -l 2>/dev/null | head -5
```

## Constraints

- **Official standard:** SS 714:2025 "Data protection trustmark" jointly developed by IMDA and PDPC, grounded in the 11 obligations of Singapore's PDPA and international benchmarks (Australia, Hong Kong, EU). Assessment certification is conducted by SAC-accredited certification bodies appointed by IMDA. Full criteria are in the paid standard; this assessment uses the four officially published certification principles.
- **Clause names are fixed** (SS 714:2025): Clause 6 — Governance and Transparency | Clause 7 — Management of Personal Data | Clause 8 — Care of Personal Data | Clause 9 — Safeguarding Individuals' Rights. Never rename or merge clauses.
- Every clause gets exactly one status: ✅ (compliant) | ❌ (gap found) | ❓ (insufficient evidence) — no blends
- ❌ requires a specific file path or artifact citation confirming absence or failure — never a guess
- ❓ only when there is genuinely no evidence in either the repo scans or uploaded artifacts (e.g. training records held in an LMS not accessible here)
- Uploaded artifacts are primary evidence — they override ❓ and may upgrade ❌ to ✅ if substantiated; never silently ignore an uploaded file
- Every finding must cite the specific PDPA obligation by section number: s11 (Accountability) · s13–s17 (Consent) · s18 (Purpose Limitation) · s20 (Notification / Openness) · s21–s22 (Access and Correction) · s23 (Accuracy) · s24 (Protection) · s25 (Retention Limitation) · s26 (Transfer Limitation) · s26B–s26K (Data Breach Notification) · Part IX (Do Not Call)
- Q3 personal data categories govern severity: health, biometric, children's data, and NRIC gaps are always rated **Critical** regardless of clause
- Q4 = Yes (cross-border transfers) makes Transfer Limitation (s26) a mandatory finding if adequate safeguards are unsubstantiated
- Q5 = Yes (AI/automated decisions) requires assessment of algorithmic transparency and human-review mechanisms; absence is a mandatory finding
- DPTM certification requires **all four clauses** to be ✅ — one ❌ blocks certification
- Never recommend "overhaul your data processes" — every gap gets a specific, actionable fix with effort estimate (hours/days/weeks)
- Maximum 15 gap findings, ordered by PDPA legal risk (mandatory obligations before best-practice gaps)
- Sector-specific overlaps from Q1 must be called out (e.g. MAS Notice 644 for Financial Services, MOH data governance guidelines for Healthcare, MCCY guidelines for children's data)

## Structure

1. **Evidence Index** — table: Filename | Document Type | Date/Version | SS 714:2025 Clause(s) | PDPA Obligation(s) | Evidence Quality (Primary / Supporting / Unreadable); list "none uploaded" if user skipped
2. **Executive summary** — 3 sentences: DPTM certification readiness verdict (Ready / Conditional / Not Ready), headline blocking clause, top recommended action
3. **DPTM Readiness Scorecard** — table: 4 clauses as rows; columns = Clause name | PDPA Obligations covered | Status (✅ / ❌ / ❓) | Evidence source (📄 artifact | 🔍 scan | ❓ gap) | Blocking finding summary; overall certification verdict at bottom
4. **Clause-by-clause findings** — for each of the 4 SS 714:2025 clauses:
   - Status with evidence citation *[filename, section/page]* or scan finding
   - PDPA obligations met and specific gaps with section reference
   - Distinction: missing-document gap vs. missing-control gap
5. **Gap remediation roadmap** — ordered table: Gap | SS 714:2025 Clause | PDPA Section | Severity (Critical / High / Medium) | Finding | Recommended Fix | Effort
6. **Sector-specific callouts** — regulatory overlaps relevant to Q1 (e.g. MAS Notice 644, MOH guidelines, PDPC Advisory Guidelines on AI, COPPA-equivalent obligations for children's data, ASEAN cross-border transfer framework)
7. **Next-steps checklist** — ≤ 10 items grouped by Quick Win (< 1 week) / Sprint (1–4 weeks) / Project (> 1 month); flag items closable by uploading an existing document the user hasn't shared yet
8. **Write output** to `DPTM-assessment.md` at repo root (or alongside `SSP.md` if present)
9. **Commit:** `git add DPTM-assessment.md && git commit -m "Add DPTM preparedness assessment report (SS 714:2025)" && git push`
