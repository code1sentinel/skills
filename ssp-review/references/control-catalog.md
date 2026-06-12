# Embedded Control Catalog Reference

**Source:** Singapore Government ICT&SS Control Catalog — info.standards.tech.gov.sg/control-catalog/
**Usage:** Load this file when live WebFetch of the catalog returns 403 or fails.
**Note:** This is a curated reference snapshot. Always verify against the live catalog when available.

---

## Control Level Legend

| Level | Label | Obligation |
|-------|-------|------------|
| **L0** | Must-Have | Mandatory for ALL systems |
| **L1** | Should-Have | Strongly recommended; expected for Medium-risk systems and above |
| **L2** | Good-to-Have | Best practice; targeted at High and CII systems |

---

## AC — Access Control
Source: https://info.standards.tech.gov.sg/control-catalog/cybersecurity/ac/

| ID | Level | Control Name | Requirement Summary | Codebase Evidence To Look For |
|----|-------|-------------|--------------------|-----------------------------|
| AC-1 | L0 | Default Credentials | Change all default credentials before deployment; prompt password change on first login | Absence of hardcoded `admin`/`password`/`root`/`changeme` in config; first-login force-change logic |
| AC-2 | L0 | Least Privilege | Deny access by default; grant only minimum permissions required per function | RBAC definitions, permission checks in route handlers, no wildcard `*` permissions |
| AC-3 | L0 | Account Management | Maintain account inventory; remove unnecessary accounts; review access quarterly | Account lifecycle code, deprovisioning logic, scheduled review mechanisms |
| AC-4 | L1 | Multi-Factor Authentication | Enforce MFA for all privileged and remote access | TOTP/OTP/authenticator integrations, MFA enforcement middleware |
| AC-5 | L1 | Credential Rotation | Rotate API keys, access tokens, and static credentials at defined intervals; automate where possible | Credential TTL settings, rotation scripts, no long-lived static API keys in source |
| AC-6 | L1 | Privileged Access Management | Use Just-In-Time (JIT) access and Privileged Identity Management (PIM) for admin accounts | PIM/JIT integrations, short-lived admin session tokens, PAM tool references |
| AC-7 | L2 | Zero Trust / Identity-Based Access | Adopt Zero Trust architecture; use Identity Aware Proxies (IAP) or Secure Service Edge (SSE) for private resource access | IAP configs, SSE references, Zero Trust network access patterns |

---

## AS — Application Security
Source: https://info.standards.tech.gov.sg/control-catalog/cybersecurity/as/

| ID | Level | Control Name | Requirement Summary | Codebase Evidence To Look For |
|----|-------|-------------|--------------------|-----------------------------|
| AS-1 | L0 | Input Validation | Validate all inputs against expected type, structure, or format; use strict schema validation to prevent injection | Validation libs (Zod, Joi, Pydantic, javax.validation); absence of unvalidated user input reaching DB/shell |
| AS-2 | L0 | Secrets Management | Store secrets in an approved secrets manager (AWS Secrets Manager, Azure Key Vault, HashiCorp Vault); never in source code or config files | No API keys/tokens in source; references to secrets manager SDK; env vars loaded from vault |
| AS-3 | L0 | Access Control Enforcement | Perform authorisation checks on all authenticated requests using middleware or filters | Auth middleware applied globally or per-route; no missing `@Authorize`/guard decorators |
| AS-4 | L1 | Output Encoding | Encode output to prevent XSS and injection; use framework-provided encoding | DOMPurify, htmlspecialchars, auto-escaping templates (Jinja2, Handlebars, React JSX) |
| AS-5 | L1 | Secure Error Handling | Do not expose stack traces, system paths, or sensitive data in error responses | Generic error messages in production; error logging separate from error responses; no debug mode in prod |
| AS-6 | L1 | Dependency Security | Use only vetted, up-to-date dependencies; no packages with known critical CVEs | Dependency scanning in CI; lock files present; no packages flagged by `npm audit`/`safety check` |
| AS-7 | L2 | Security Response Headers | Implement CSP, HSTS, X-Frame-Options, X-Content-Type-Options, Referrer-Policy | Helmet.js or equivalent; CSP directives defined; HSTS header configured |

---

## SD — Secure Development
Source: https://info.standards.tech.gov.sg/control-catalog/cybersecurity/sd/

| ID | Level | Control Name | Requirement Summary | Codebase Evidence To Look For |
|----|-------|-------------|--------------------|-----------------------------|
| SD-1 | L0 | SSDLC Implementation | Implement and maintain a Secure Software Development Lifecycle; integrate security in all phases | Security checks in CI/CD; security requirements in docs; threat modelling artefacts |
| SD-2 | L0 | Secret Scanning | Configure repository to block secrets from being pushed; use push protection or pre-commit hooks | `.gitleaks.toml`, `.secrets.baseline`, GitHub push protection enabled, `detect-secrets` in pre-commit |
| SD-3 | L0 | Protected Branches | Require CI tests to pass before merging to the default branch; use branch protection rules | CI config with required checks; `branch-protection` settings; PR-based merge workflows |
| SD-4 | L1 | Dependency Scanning | Schedule regular automated scans to identify vulnerable dependencies | Dependabot, Renovate, Snyk, or OWASP Dependency-Check in CI; scheduled scan jobs |
| SD-5 | L1 | Static Analysis (SAST) | Integrate SAST tools in CI pipeline to detect code vulnerabilities before merge | Semgrep, CodeQL, SonarQube, Checkmarx, or Veracode steps in CI workflow |
| SD-6 | L1 | Container Image Scanning | Scan container images for OS and package vulnerabilities before deployment | Trivy, Snyk, Grype, or Clair in CI; scan on build or push to registry |
| SD-7 | L2 | IaC Security Scanning | Scan Infrastructure-as-Code templates for misconfigurations | Checkov, tfsec, kics, or cfn-nag in CI pipeline scanning Terraform, CloudFormation, or Helm charts |

---

## LM — Logging & Monitoring
Source: https://info.standards.tech.gov.sg/control-catalog/cybersecurity/lm/

| ID | Level | Control Name | Requirement Summary | Codebase Evidence To Look For |
|----|-------|-------------|--------------------|-----------------------------|
| LM-1 | L0 | Security Event Logging | Log authentication events, access control failures, privilege changes, and critical operations | Auth success/failure log calls; `audit` log statements; structured security event records |
| LM-2 | L0 | Log Integrity | Protect logs from tampering; forward to centralised, tamper-resistant storage | Log forwarding config; no local-only log storage for security events; append-only log patterns |
| LM-3 | L1 | Centralised Logging | Forward all logs to a centralised SIEM or log management platform | Filebeat, Fluentd, Logstash configs; SIEM integration; CloudWatch/Splunk/Datadog log routing |
| LM-4 | L1 | Log Retention | Retain security-relevant logs for the minimum required period per policy | Retention configuration in log management platform; documented retention policy |
| LM-5 | L1 | Alerting & Anomaly Detection | Configure alerts for failed authentication, privilege escalation, and anomalous access patterns | Alert rules in monitoring platform; threshold-based alerts on auth failures; SIEM correlation rules |
| LM-6 | L2 | SOC Integration | Integrate with a Security Operations Centre for continuous monitoring and threat detection | SOC/MSSP integration configs; escalation runbooks referencing SOC; threat-hunting capability |

---

## NS — Network Security
Source: https://info.standards.tech.gov.sg/control-catalog/cybersecurity/ns/

| ID | Level | Control Name | Requirement Summary | Codebase Evidence To Look For |
|----|-------|-------------|--------------------|-----------------------------|
| NS-1 | L0 | TLS Enforcement | Enforce TLS 1.2+ for all data in transit; disable weak cipher suites and deprecated TLS versions | TLS version config; no plaintext HTTP for sensitive endpoints; certificate configuration |
| NS-2 | L0 | Network Segmentation | Isolate system components using VPCs, subnets, or security groups; restrict traffic by principle of least privilege | IaC security group rules; VPC/subnet config; no `0.0.0.0/0` ingress on sensitive ports |
| NS-3 | L1 | HTTPS Enforcement & HSTS | Redirect HTTP to HTTPS; enforce HSTS with appropriate max-age | HTTPS redirect middleware; `Strict-Transport-Security` header; no plaintext HTTP serving |
| NS-4 | L1 | Web Application Firewall | Deploy a WAF for all internet-facing services to filter malicious requests | WAF config files; WAF references in IaC (AWS WAF, Azure Front Door WAF, Cloudflare); managed rule groups |
| NS-5 | L1 | DDoS Protection | Implement DDoS mitigation for internet-facing services | DDoS protection service references; rate-limiting at load balancer or CDN level |
| NS-6 | L2 | Micro-Segmentation | Implement east-west traffic controls within the system to limit lateral movement | Service mesh configs (Istio, Linkerd); network policies in Kubernetes; internal firewall rules |

---

## SC — Software Supply Chain
Source: https://info.standards.tech.gov.sg/control-catalog/cybersecurity/sc/

| ID | Level | Control Name | Requirement Summary | Codebase Evidence To Look For |
|----|-------|-------------|--------------------|-----------------------------|
| SC-1 | L0 | Dependency Pinning | Pin all dependency versions in lock files; do not use floating `latest` or `*` specifiers | `package-lock.json`, `yarn.lock`, `go.sum`, `Pipfile.lock`, `poetry.lock`, `Cargo.lock` present and committed |
| SC-2 | L0 | Third-Party Vetting | Review and approve third-party components before use; maintain a list of approved packages | Approved package list or policy doc; evidence of review before new dependencies are added |
| SC-3 | L1 | Software Bill of Materials (SBOM) | Generate and maintain an SBOM (CycloneDX or SPDX format) for the system | `syft`, `cyclonedx-bom`, or `spdx-sbom-generator` in CI; SBOM artefact in release pipeline |
| SC-4 | L1 | Container Base Image Security | Use minimal, verified base images; pin image tags to specific digest; avoid `latest` | Dockerfile base image with digest pin (`@sha256:`); use of distroless or minimal base images |
| SC-5 | L2 | Package Signing & Verification | Verify integrity of third-party packages using cryptographic signatures | `cosign`, `sigstore`, npm package provenance, or GPG verification in CI; signed container images |
| SC-6 | L2 | Build Provenance & Attestation | Use SLSA or equivalent framework to verify the integrity of the build pipeline | SLSA provenance attestation in CI; `slsa-github-generator`; build attestation artefacts |

---

## DP — Data Protection
Source: https://info.standards.tech.gov.sg/control-catalog/cybersecurity/dp/

| ID | Level | Control Name | Requirement Summary | Codebase Evidence To Look For |
|----|-------|-------------|--------------------|-----------------------------|
| DP-1 | L0 | Encryption at Rest | Encrypt sensitive data at rest using AES-256 or equivalent; use cloud-managed keys where possible | KMS key references in IaC; encrypted EBS/S3/RDS config; `bcrypt`/`argon2` for password storage |
| DP-2 | L0 | Encryption in Transit | Enforce TLS for all data transmission; no plaintext transmission of sensitive data | TLS config (see NS-1); no plaintext database connections for sensitive data; encrypted message queues |
| DP-3 | L1 | Data Classification | Classify data per Singapore Government classification (Official-Open, Official-Closed, Sensitive, Restricted); apply appropriate controls per class | Data classification annotations in models/schemas/comments; classification policy doc in `docs/` |
| DP-4 | L1 | Data Minimisation & Retention | Collect only necessary data; implement data retention limits and automated deletion | Retention policy in code or config; scheduled purge jobs; minimal PII fields in data models |
| DP-5 | L1 | Backup & Recovery | Maintain regular, tested backups; document and test RTO/RPO objectives | Backup config files or scripts; documented recovery procedures; backup testing evidence |
| DP-6 | L2 | Data Loss Prevention (DLP) | Implement controls to detect and prevent unauthorised exfiltration of sensitive data | DLP tool references; egress monitoring config; data exfiltration alert rules in SIEM |

---

## PM — Security Programme Management
Source: https://info.standards.tech.gov.sg/control-catalog/cybersecurity/pm/

| ID | Level | Control Name | Requirement Summary | Codebase Evidence To Look For |
|----|-------|-------------|--------------------|-----------------------------|
| PM-1 | L0 | Security Policies | Document and maintain security policies covering data handling, access control, and incident response | `SECURITY.md`, `.github/SECURITY.md`, `docs/security-policy*`; responsible disclosure statement |
| PM-2 | L0 | Vulnerability Management | Establish a process for identifying, triaging, and patching vulnerabilities; define SLA for critical patches | Vulnerability disclosure policy; patch SLA documented; evidence of regular security scanning |
| PM-3 | L1 | Risk Assessment | Conduct formal risk assessment; document and track risks in a risk register | Risk register in `docs/`; threat model document; architecture decision records with security considerations |
| PM-4 | L1 | Security Awareness Training | Provide security training for developers and operators; include secure coding practices | Reference to security training programme in docs; security guidelines for developers |
| PM-5 | L1 | Incident Response Plan | Document and test an incident response plan; define roles, escalation, and communication procedures | `docs/incident-response*`, `runbooks/`, `playbooks/`; escalation contact list; incident response runbook |
| PM-6 | L2 | Penetration Testing | Commission annual penetration tests by qualified testers; remediate findings within defined SLA | References to pentest schedule or results in docs; remediation tracking; pentest scope document |

---

## Additional Controls: GenAI Systems
Source: https://info.standards.tech.gov.sg/ssp/gen-ai/

*Apply these additional controls when Q4 = Yes (GenAI component present).*

| ID | Level | Control Name | Requirement Summary | Codebase Evidence To Look For |
|----|-------|-------------|--------------------|-----------------------------|
| AI-1 | L0 | LLM Input/Output Validation | Validate and sanitise all inputs to and outputs from LLM components; implement guardrails | Prompt injection defences; output filtering; input sanitisation before LLM calls |
| AI-2 | L0 | Data Privacy in AI Pipelines | Ensure sensitive data is not inadvertently included in prompts sent to external LLM providers | PII scrubbing before LLM calls; data masking in RAG pipelines; review of what is sent to external APIs |
| AI-3 | L1 | Model Access Control | Restrict access to AI models and APIs; apply least-privilege to LLM service accounts | API key scoping for LLM providers; rate limiting on AI endpoints; model access policies |
| AI-4 | L1 | AI Output Logging | Log AI inputs and outputs for audit, monitoring, and bias detection | Prompt/response logging (with PII removed); AI audit trail; monitoring for anomalous outputs |
| AI-5 | L2 | Adversarial Testing | Test GenAI components for prompt injection, jailbreaking, and data leakage | Evidence of red-teaming; prompt injection test cases; adversarial evaluation in CI |
