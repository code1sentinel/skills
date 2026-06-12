# Assessment Report — Skills (GenAI RAG System)

> **Generated:** 2026-06-08
> **Assessed against:** Singapore Government ICT&SS Control Catalog (info.standards.tech.gov.sg)
> **SSP type:** Cloud Low-Risk + Generative AI (RAG)
> **Catalog source:** ⚠️ Fallback — all live catalog URLs returned 403 (server-side auth required). Used embedded reference at `./shift-left-ssp-assessor/references/control-catalog.md`. Verify against live catalog at info.standards.tech.gov.sg when site access is available.
> **Control scope:** L0 (Must-Have) assessed; L1 & L2 marked N/A for Low-risk system (upgrade risk level to Medium to require L1)

---

## Executive Summary

The assessed codebase is in an early/bootstrap stage with no application code, CI/CD pipeline, or infrastructure configuration present. Of the 21 in-scope Level 0 controls, **5 are definitively non-compliant** (clear evidence of absence) and **16 require manual verification** (no code to assess against). Zero controls are confirmed compliant. The most critical immediate gaps are in **Secure Development** (no CI/CD or secret scanning), **Software Supply Chain** (no lock files), and **Security Programme Management** (no SECURITY.md or vulnerability policy). Before any GenAI RAG application code is written and deployed, these foundational controls must be established — particularly the prompt injection defences (AI-1) and data privacy safeguards (AI-2) required for all GenAI systems.

---

## Compliance Summary

| Domain | Controls | ✅ | ❌ | 🔵 | ❓ |
|--------|----------|:--:|:--:|:--:|:--:|
| AC – Access Control | 7 | 0 | 0 | 4 | 3 |
| AS – Application Security | 7 | 0 | 0 | 4 | 3 |
| SD – Secure Development | 7 | 0 | 2 | 4 | 1 |
| LM – Logging & Monitoring | 6 | 0 | 0 | 4 | 2 |
| NS – Network Security | 6 | 0 | 0 | 4 | 2 |
| SC – Software Supply Chain | 6 | 0 | 1 | 4 | 1 |
| DP – Data Protection | 6 | 0 | 0 | 4 | 2 |
| PM – Security Programme Mgmt | 6 | 0 | 2 | 4 | 0 |
| GenAI – AI Controls | 5 | 0 | 0 | 3 | 2 |
| **TOTAL** | **56** | **0** | **5** | **35** | **16** |

**Overall compliance score: 0%** *(0 of 5 definitively-assessed controls are compliant)*
*16 L0 controls are ❓ Unassessed — no application code present to evaluate against*

### Legend
| Symbol | Meaning |
|--------|---------|
| ✅ | **Compliant** — clear evidence of implementation found in codebase |
| ❌ | **Non-Compliant** — requirement absent, violated, or counterevidence found |
| 🔵 | **N/A** — not applicable (L1/L2 controls not required at Low risk level, or feature not present) |
| ❓ | **Unassessed** — insufficient evidence; no application code to evaluate; manual review required |

---

## Compliance Matrix

| Control ID | Domain | Level | Control Name | Status | Evidence / Finding | Remediation |
|:----------|:-------|:-----:|:------------|:------:|:-------------------|:------------|
| AC-1 | Access Control | L0 | Default Credentials | ❓ | No application code or config files found. No hardcoded credentials detected (positive). Cannot confirm first-login credential-change enforcement without app code. | Implement and verify once auth layer is built || AC-2 | Access Control | L0 | Least Privilege | ❓ | No application code found. No RBAC definitions, route guards, or permission middleware present. | Design and implement RBAC/ABAC before deploying the RAG API |
| AC-3 | Access Control | L0 | Account Management | ❓ | No application code found. No account lifecycle or deprovisioning logic present. | Define account management process; implement account inventory |
| AC-4 | Access Control | L1 | Multi-Factor Authentication | 🔵 | N/A — L1 control; not required at Low risk level | Consider implementing for privileged/admin access regardless |
| AC-5 | Access Control | L1 | Credential Rotation | 🔵 | N/A — L1 control; not required at Low risk level | — |
| AC-6 | Access Control | L1 | Privileged Access Management | 🔵 | N/A — L1 control; not required at Low risk level | — |
| AC-7 | Access Control | L2 | Zero Trust / Identity-Based Access | 🔵 | N/A — L2 control; Good-to-Have | — |
| AS-1 | Application Security | L0 | Input Validation | ❓ | No application code found. For a RAG system, this includes validating user query inputs before they reach the retrieval layer and LLM. Prompt injection is a specific AS-1 concern for GenAI (see also AI-1). | Implement input validation schema when building the query API |
| AS-2 | Application Security | L0 | Secrets Management | ❓ | No secrets found hardcoded in the repo (positive finding). However, no secrets manager SDK (AWS Secrets Manager, Azure Key Vault, HashiCorp Vault) is configured. LLM API keys, vector DB credentials, and cloud credentials must be stored in a vault. | Configure secrets manager before committing any credentials |
| AS-3 | Application Security | L0 | Access Control Enforcement | ❓ | No application code found. No auth middleware, guards, or decorators present. | Implement auth enforcement middleware on all RAG API routes |
| AS-4 | Application Security | L1 | Output Encoding | 🔵 | N/A — L1 control; not required at Low risk level | — |
| AS-5 | Application Security | L1 | Secure Error Handling | 🔵 | N/A — L1 control; not required at Low risk level | — |
| AS-6 | Application Security | L1 | Dependency Security | 🔵 | N/A — L1 control; not required at Low risk level (however, no lock files were found — see SC-1) | Address under SC-1 |
| AS-7 | Application Security | L2 | Security Response Headers | 🔵 | N/A — L2 control; Good-to-Have | — |
| SD-1 | Secure Development | L0 | SSDLC Implementation | ❌ | No CI/CD pipeline found (`.github/workflows/`, `.gitlab-ci.yml`, etc. absent). No security checks, code review gates, or automated testing configured. | Establish CI/CD pipeline with at minimum: build, test, and merge gate checks before first code deployment |
| SD-2 | Secure Development | L0 | Secret Scanning | ❌ | No secret scanning configuration found: no `.gitleaks.toml`, `.secrets.baseline`, `.pre-commit-config.yaml`, or GitHub push protection evidence. Given the system uses LLM API keys and cloud credentials, this is a high-priority gap. | Add Gitleaks or detect-secrets as a pre-commit hook and/or GitHub Actions check immediately |
| SD-3 | Secure Development | L0 | Protected Branches | ❓ | Cannot assess branch protection settings from repository files alone — requires verification in GitHub/GitLab repository settings. | Verify and enable branch protection on `main`: require PR reviews and status checks before merge |
| SD-4 | Secure Development | L1 | Dependency Scanning | 🔵 | N/A — L1 control; not required at Low risk level | — |
| SD-5 | Secure Development | L1 | Static Analysis (SAST) | 🔵 | N/A — L1 control; not required at Low risk level | — |
| SD-6 | Secure Development | L1 | Container Image Scanning | 🔵 | N/A — no Dockerfile present; not applicable | — |
| SD-7 | Secure Development | L2 | IaC Security Scanning | 🔵 | N/A — L2 control; no IaC files present | — |
| LM-1 | Logging & Monitoring | L0 | Security Event Logging | ❓ | No application code found. No logging framework imports or security event log statements. For GenAI systems, logging of auth events and AI interactions (AI-4) is essential. | Design logging strategy alongside application layer; log auth events and LLM call outcomes |
| LM-2 | Logging & Monitoring | L0 | Log Integrity | ❓ | No log forwarding or storage configuration found. | Configure centralised, tamper-resistant log storage before deployment |
| LM-3 | Logging & Monitoring | L1 | Centralised Logging | 🔵 | N/A — L1 control; not required at Low risk level | — |
| LM-4 | Logging & Monitoring | L1 | Log Retention | 🔵 | N/A — L1 control; not required at Low risk level | — |
| LM-5 | Logging & Monitoring | L1 | Alerting & Anomaly Detection | 🔵 | N/A — L1 control; not required at Low risk level | — |
| LM-6 | Logging & Monitoring | L2 | SOC Integration | 🔵 | N/A — L2 control; Good-to-Have | — |
| NS-1 | Network Security | L0 | TLS Enforcement | ❓ | No TLS or SSL configuration files found. No IaC to confirm cloud-level TLS termination. Cloud provider managed TLS is common but must be explicitly confirmed. | Confirm TLS is enforced end-to-end: user → API, API → vector DB, API → LLM provider |
| NS-2 | Network Security | L0 | Network Segmentation | ❓ | No IaC found (no `.tf`, CloudFormation, or Kubernetes manifests). Network segmentation configuration cannot be assessed. | Define VPC, subnets, and security groups in IaC before cloud deployment |
| NS-3 | Network Security | L1 | HTTPS Enforcement & HSTS | 🔵 | N/A — L1 control; not required at Low risk level | — |
| NS-4 | Network Security | L1 | Web Application Firewall | 🔵 | N/A — L1 control; not required at Low risk level | — |
| NS-5 | Network Security | L1 | DDoS Protection | 🔵 | N/A — L1 control; not required at Low risk level | — |
| NS-6 | Network Security | L2 | Micro-Segmentation | 🔵 | N/A — L2 control; Good-to-Have | — |
| SC-1 | Software Supply Chain | L0 | Dependency Pinning | ❌ | No lock files found anywhere in the repository (`package-lock.json`, `yarn.lock`, `requirements.txt`, `go.sum`, `poetry.lock`, etc. all absent). No dependency manifest of any kind detected. This is expected for the current documentation-only stage but must be addressed immediately when application code is introduced. | Add a dependency manifest and lock file as the first commit when introducing application code |
| SC-2 | Software Supply Chain | L0 | Third-Party Vetting | ❓ | No approved package list or vetting process documentation found. For a RAG system, the LLM orchestration library (LangChain, LlamaIndex, etc.) and vector DB client represent significant third-party risk. | Document a vetting process for new dependencies; review LLM framework supply chain before adoption |
| SC-3 | Software Supply Chain | L1 | Software Bill of Materials | 🔵 | N/A — L1 control; not required at Low risk level | — |
| SC-4 | Software Supply Chain | L1 | Container Base Image Security | 🔵 | N/A — no Dockerfile present | — |
| SC-5 | Software Supply Chain | L2 | Package Signing & Verification | 🔵 | N/A — L2 control; Good-to-Have | — |
| SC-6 | Software Supply Chain | L2 | Build Provenance & Attestation | 🔵 | N/A — L2 control; Good-to-Have | — |
| DP-1 | Data Protection | L0 | Encryption at Rest | ❓ | No storage configuration or encryption library usage found. For a RAG system, the vector database and any document store must have encryption at rest enabled. | Confirm cloud-managed encryption at rest for all storage services before deployment |
| DP-2 | Data Protection | L0 | Encryption in Transit | ❓ | No TLS or network configuration found (see NS-1). All data in transit — including user queries sent to the LLM provider API — must use TLS. | Verify TLS for all connections, particularly the API call to the external LLM provider |
| DP-3 | Data Protection | L1 | Data Classification | 🔵 | N/A — L1 control; not required at Low risk level. Note: the System Owner should classify the RAG corpus data regardless, as it may justify upgrading the risk level. | — |
| DP-4 | Data Protection | L1 | Data Minimisation & Retention | 🔵 | N/A — L1 control; not required at Low risk level | — |
| DP-5 | Data Protection | L1 | Backup & Recovery | 🔵 | N/A — L1 control; not required at Low risk level | — |
| DP-6 | Data Protection | L2 | Data Loss Prevention | 🔵 | N/A — L2 control; Good-to-Have | — |
| PM-1 | Security Programme Mgmt | L0 | Security Policies | ❌ | No `SECURITY.md`, `.github/SECURITY.md`, or security policy document found anywhere in the repository. This is a foundational document required for all systems before IM8 submission. | Create `SECURITY.md` covering: responsible disclosure process, data handling policy, access control policy |
| PM-2 | Security Programme Mgmt | L0 | Vulnerability Management | ❌ | No vulnerability management process, patch SLA, or security scanning schedule documented. No CI scanning configured. | Document vulnerability management process; define patch SLA (recommend: critical ≤ 7 days, high ≤ 30 days) |
| PM-3 | Security Programme Mgmt | L1 | Risk Assessment | 🔵 | N/A — L1 control; not required at Low risk level | — |
| PM-4 | Security Programme Mgmt | L1 | Security Awareness Training | 🔵 | N/A — L1 control; not required at Low risk level | — |
| PM-5 | Security Programme Mgmt | L1 | Incident Response Plan | 🔵 | N/A — L1 control; not required at Low risk level | — |
| PM-6 | Security Programme Mgmt | L2 | Penetration Testing | 🔵 | N/A — L2 control; Good-to-Have | — |
| AI-1 | GenAI | L0 | LLM Input/Output Validation | ❓ | No LLM or RAG pipeline code found in the current repository. The RAG component has been declared but not yet committed. For all GenAI systems this is a mandatory L0 control — prompt injection defences must be designed into the system from day one. | Implement input sanitisation before LLM calls and output guardrails before returning responses; add to design spec now |
| AI-2 | GenAI | L0 | Data Privacy in AI Pipelines | ❓ | No RAG pipeline code found. Cannot assess whether PII scrubbing is applied before queries are sent to external LLM providers. If the RAG corpus or user queries contain any PII or sensitive data, this is critical. | Design PII scrubbing/masking into the RAG pipeline architecture before implementation begins |
| AI-3 | GenAI | L1 | Model Access Control | 🔵 | N/A — L1 control; not required at Low risk level | — |
| AI-4 | GenAI | L1 | AI Output Logging | 🔵 | N/A — L1 control; not required at Low risk level | — |
| AI-5 | GenAI | L2 | Adversarial Testing | 🔵 | N/A — L2 control; Good-to-Have | — |

---

## Gap Report

*Only controls assessed as ❌ Non-Compliant appear here. ❓ Unassessed controls require manual action and are listed in the 🟠 section below.*

---

### 🔴 Critical — Level 0 Non-Compliant (must fix before IM8 submission)

| # | Control ID | Level | Finding | Recommended Fix | Effort |
|:-:|:----------|:-----:|:--------|:----------------|:------:|
| 1 | SD-1 | L0 | No CI/CD pipeline configured. No automated build, test, or security gate exists. Any code can be merged without review or testing. | Create `.github/workflows/ci.yml` with build, lint, and test jobs; require checks to pass before merge | Low |
| 2 | SD-2 | L0 | No secret scanning configured. LLM API keys and cloud credentials could be accidentally committed without detection. | Add Gitleaks pre-commit hook: `pip install detect-secrets && detect-secrets scan > .secrets.baseline`; add GitHub secret scanning action | Low |
| 3 | SC-1 | L0 | No dependency manifest or lock file found. Once application code is introduced, dependencies will be unversioned and untracked. | On first `npm install` / `pip install` / `go mod init`: commit `package-lock.json` / `requirements.txt` / `go.sum` to the repo | Low |
| 4 | PM-1 | L0 | No `SECURITY.md` or security policy document. There is no responsible disclosure process or security guidance for contributors. | Create `SECURITY.md` with: security contact, vulnerability reporting process, data handling summary | Low |
| 5 | PM-2 | L0 | No vulnerability management process documented. No patch SLA defined. No evidence of security scanning schedule. | Add a vulnerability management section to `SECURITY.md`; configure Dependabot or Renovate once dependencies exist | Low |

---

### 🟠 High — Level 1 Non-Compliant + Level 0 Unassessed (❓ controls requiring urgent manual verification)

*These L0 controls could not be confirmed from code alone. Treat as gaps until verified.*

| # | Control ID | Level | Finding | Manual Action Required | Effort |
|:-:|:----------|:-----:|:--------|:-----------------------|:------:|
| 1 | AC-1 | L0 | No auth code to assess. Default credential handling unknown. | Confirm that no default admin credentials exist; verify first-login password change flow once auth is built | Med |
| 2 | AC-2 | L0 | No RBAC or permission code found. Least-privilege posture unknown. | Design and document RBAC model before building the RAG API; define roles: user, admin, operator | Med |
| 3 | AC-3 | L0 | No account management code found. | Define account lifecycle process; document provisioning/deprovisioning procedures | Med |
| 4 | AS-1 | L0 | No input validation code. User query inputs to the RAG pipeline are unvalidated. | Design input validation schema (e.g. max length, allowed characters, content type) for query API inputs | Med |
| 5 | AS-2 | L0 | No secrets manager configured. LLM API keys not yet present but must not be stored in code. | Set up AWS Secrets Manager / Azure Key Vault / HashiCorp Vault before any credentials are needed | Low |
| 6 | AS-3 | L0 | No auth enforcement middleware. All routes currently unprotected. | Implement auth middleware on all API routes before first deployment | Med |
| 7 | SD-3 | L0 | Branch protection status unknown — cannot assess from repo files. | Verify in GitHub settings: enable required reviews (min 1) and required status checks on `main` | Low |
| 8 | LM-1 | L0 | No logging framework. No security event logging present. | Choose and integrate a logging framework; ensure auth events, access denials, and LLM call outcomes are logged | Med |
| 9 | LM-2 | L0 | No log forwarding configuration. Logs would be lost if containers restart. | Configure log forwarding to a persistent, tamper-resistant destination (CloudWatch, GCS, Azure Monitor) | Med |
| 10 | NS-1 | L0 | No TLS configuration found. TLS posture of cloud deployment unknown. | Confirm TLS at cloud load balancer; enforce TLS for all outbound calls (LLM provider, vector DB) | Med |
| 11 | NS-2 | L0 | No IaC or network configuration found. Network segmentation unconfirmed. | Define VPC, subnet, and security group rules in IaC before cloud deployment | Med |
| 12 | SC-2 | L0 | No third-party vetting process documented for dependencies. | Document approval criteria for new dependencies; conduct security review of LLM framework before adoption | Med |
| 13 | DP-1 | L0 | No storage configuration. Encryption at rest for vector DB and document store unconfirmed. | Confirm encryption at rest is enabled on all storage services at cloud deployment time | Low |
| 14 | DP-2 | L0 | No TLS configuration. Encryption in transit for LLM provider calls unconfirmed. | Explicitly verify TLS is used for all outbound API calls; do not accept self-signed certs in production | Low |
| 15 | AI-1 | L0 | No LLM code present. Prompt injection defences absent from design. | Add prompt injection defence requirements to design spec now; implement input sanitisation and output guardrails when building RAG pipeline | High |
| 16 | AI-2 | L0 | No RAG pipeline code. PII handling in AI pipeline unverified. | Determine whether RAG corpus or user queries may contain PII; design PII scrubbing step before LLM call | High |

---

## Assessment Notes

- **Catalog source:** All 9 live catalog URLs at `info.standards.tech.gov.sg` returned HTTP 403. The fallback embedded catalog was used. Verify findings against the live catalog once access is available — control requirements may have been updated.
- **Early-stage codebase:** This is a documentation-only repository. Most ❓ Unassessed controls are unassessable because application code does not yet exist, not because evidence was hidden. Reassess once RAG pipeline code is committed.
- **GenAI risk note:** Even at Low risk classification, AI-1 and AI-2 are L0 Must-Have controls. Prompt injection and PII leakage in RAG pipelines are not hypothetical risks — they must be designed in from the start.
- **Risk level reconsideration:** If the RAG corpus contains Official-Closed, Sensitive, or Restricted data, the system risk level should be upgraded to Medium or High, which would bring L1 controls into scope.
- **Re-run this assessment** after: (1) CI/CD and secret scanning are set up, (2) application code is committed, (3) IaC is written. The compliance score should improve significantly.
