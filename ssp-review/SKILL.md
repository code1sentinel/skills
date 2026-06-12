---
name: ssp-review
description: Assess a codebase against the Singapore Government ICT&SS control catalog and produce
  a compliance matrix with a prioritised gap report. Use when checking IM8 compliance, identifying
  security gaps, generating a compliance report, or evaluating a codebase against
  info.standards.tech.gov.sg controls. Trigger on: "assess compliance", "check IM8", "security
  assessment", "compliance matrix", "gap report", "scan for gaps", "ICT&SS assessment",
  "control catalog", "shift-left assessment", "assess my codebase", "ssp-review", "review SSP".
---

## Context

Read system classification from SSP.md if present (ask user Q1–Q4 if not):
```
grep -A 8 "System Classification" SSP.md 2>/dev/null || echo "SSP.md not found"
```

Fetch live control catalog — run all in parallel (fallback: read `./references/control-catalog.md`):
```
curl -s https://info.standards.tech.gov.sg/control-catalog/cybersecurity/ac/
curl -s https://info.standards.tech.gov.sg/control-catalog/cybersecurity/as/
curl -s https://info.standards.tech.gov.sg/control-catalog/cybersecurity/sd/
curl -s https://info.standards.tech.gov.sg/control-catalog/cybersecurity/lm/
curl -s https://info.standards.tech.gov.sg/control-catalog/cybersecurity/ns/
curl -s https://info.standards.tech.gov.sg/control-catalog/cybersecurity/sc/
curl -s https://info.standards.tech.gov.sg/control-catalog/cybersecurity/dp/
curl -s https://info.standards.tech.gov.sg/control-catalog/cybersecurity/pm/
```

Control evidence — run all in parallel:
```
grep -rn "password\s*=\s*['\"][^'\"]\|api_key\s*=\|\"admin\"\|\"changeme\"" --include="*.py" --include="*.ts" --include="*.go" --include="*.java" --include="*.yml" . 2>/dev/null | head -10
grep -r "passport\|oauth\|jwt\|oidc\|casbin\|spring-security\|authlib" --include="*.py" --include="*.ts" --include="*.go" -l 2>/dev/null
grep -r "zod\|joi\|pydantic\|marshmallow\|javax.validation\|express-validator" --include="*.py" --include="*.ts" --include="*.js" -l 2>/dev/null
grep -r "SELECT.*+\|f\"INSERT\|f\"SELECT\|string.Format.*SELECT" --include="*.py" --include="*.ts" --include="*.go" --include="*.java" -n 2>/dev/null | head -10
grep -r "sk-[a-zA-Z0-9]\{20,\}\|AKIA[A-Z0-9]\{16\}\|ghp_\|-----BEGIN" . --include="*.py" --include="*.ts" --include="*.js" --include="*.go" 2>/dev/null | head -5
find . -not -path './.git/*' -name ".gitleaks.toml" -o -name ".secrets.baseline" -o -name ".pre-commit-config.yaml" 2>/dev/null
find . -path "./.github/workflows/*.yml" 2>/dev/null | xargs grep -l "semgrep\|codeql\|sonarqube\|trivy\|snyk\|gitleaks\|dependabot" 2>/dev/null
find . -not -path './.git/*' -name "package-lock.json" -o -name "requirements.txt" -o -name "go.sum" -o -name "Cargo.lock" -o -name "poetry.lock" 2>/dev/null | head -5
find . -not -path './.git/*' -name "SECURITY.md" -o -name "runbook*" -o -name "incident*" 2>/dev/null
grep -r "winston\|pino\|log4j\|structlog\|zerolog\|zap" --include="*.py" --include="*.ts" --include="*.go" --include="*.java" -l 2>/dev/null
grep -r "bcrypt\|argon2\|AES\|KMSClient\|SecretManagerClient\|fernet" --include="*.py" --include="*.ts" --include="*.go" -l 2>/dev/null
```

GenAI controls *(run only if Q4 = Yes)*:
```
grep -r "sanitize\|guardrail\|content_filter\|injection\|moderate" --include="*.py" --include="*.ts" -l 2>/dev/null
grep -r "scrub\|strip_pii\|anonymize\|redact" --include="*.py" --include="*.ts" -l 2>/dev/null
```

## Constraints

- Every control gets exactly one status: ✅ ❌ 🔵 ❓ — no partial, no blends
- ❌ requires a specific file path or confirmed absence as evidence — never a guess
- 🔵 N/A only for: L1/L2 controls below the system's risk level, or a feature confirmed absent (e.g. no Dockerfile → container controls N/A)
- ❓ only when there is genuinely no code to assess against (early-stage repo, config not in repo)
- Gap report lists ❌ Non-Compliant only — ❓ Unassessed goes in 🟠 section as a separate table
- Control scope by risk: Low → L0 only; Medium → L0+L1; High/CII → L0+L1+L2

## Structure

1. Executive summary: 2–3 sentences — posture, headline gap, recommended first action
2. Compliance summary table: domain rows × (✅ ❌ 🔵 ❓) columns + overall score %
3. Full compliance matrix: Control ID | Domain | Level | Name | Status | Evidence / Finding | Remediation
4. Gap report: 🔴 L0 ❌ → 🟠 L1 ❌ + L0 ❓ → 🟡 L2 ❌, each with finding, fix, effort
5. Written to `ASSESSMENT-REPORT.md` alongside `SSP.md`
6. Commit: `git add ASSESSMENT-REPORT.md && git commit -m "Add IM8 compliance assessment report" && git push`
