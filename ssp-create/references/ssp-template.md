# SSP Section Template

**Purpose:** Use this template to structure `SSP.md`. Populate each section from codebase findings.
For sections that cannot be inferred from code, insert:
`[TO BE COMPLETED BY SYSTEM OWNER: <guidance>]`

---

```markdown
# System Security Plan (SSP)
**Document version:** 1.0 (Draft — Shift-Left Assessment)
**Generated:** [date]
**System name:** [infer from package.json/pyproject.toml/README, or ask user]
**System owner:** [TO BE COMPLETED BY SYSTEM OWNER: name and agency/organisation]
**SSP type:** [Cloud Low/Medium/High-Risk | On-Premises Low-Risk | SaaS | Generative AI | Digital Services | Sandbox]
**Prepared by:** Shift-Left SSP Assessor (automated analysis)
**Next review date:** [TO BE COMPLETED BY SYSTEM OWNER: set review cadence, recommend annually]

---

## 1. System Overview

### 1.1 Purpose and Description
[Infer from README.md introduction. Describe what the system does, who the users are, and its
primary business function. If README is not available: TO BE COMPLETED BY SYSTEM OWNER]

### 1.2 System Classification
| Attribute | Value |
|-----------|-------|
| Deployment type | [Cloud / On-Premises / SaaS] |
| Risk materiality level | [Low / Medium / High / CII] |
| Development & maintenance | [In-house / Outsourced-Local / Outsourced-Offshore / Mixed] |
| GenAI component | [Yes / No / Under consideration] |
| Data classification (highest) | [TO BE COMPLETED BY SYSTEM OWNER: Official-Open / Official-Closed / Sensitive / Restricted] |

### 1.3 System Boundary
[Describe what is in scope: applications, APIs, databases, infrastructure components.
Infer from Dockerfile, docker-compose.yml, IaC files, and directory structure.
Components detected: [list from codebase scan]]

---

## 2. System Architecture

### 2.1 Architecture Overview
[Infer from directory structure, README, and infrastructure files. Describe the high-level
architecture: e.g. 3-tier web application, microservices, serverless, etc.]

### 2.2 Components Detected
| Component | Type | Technology | Notes |
|-----------|------|-----------|-------|
| [e.g. Web frontend] | Presentation | [React / Vue / Angular — inferred from package.json] | |
| [e.g. API service] | Application | [Node.js / Python / Java — inferred from language files] | |
| [e.g. Database] | Data | [PostgreSQL / MySQL / MongoDB — inferred from connection strings] | |
| [e.g. Cache] | Infrastructure | [Redis — inferred from imports/config] | |

### 2.3 Network Architecture
[Infer from IaC configs, docker-compose, or kubernetes manifests. Note VPCs, subnets, load
balancers, and external integrations detected. If not inferable: TO BE COMPLETED BY SYSTEM OWNER]

### 2.4 External Integrations
[List third-party services called — infer from API client imports, environment variable names
(e.g. `STRIPE_KEY`, `SENDGRID_API_KEY`), and outbound HTTP calls in code]

---

## 3. Technology Stack

### 3.1 Languages and Frameworks
| Category | Technology | Version / Details |
|----------|-----------|------------------|
| Language | [inferred] | [from package.json/pyproject.toml/go.mod] |
| Web framework | [inferred] | |
| ORM / Database client | [inferred] | |
| Authentication library | [inferred] | |
| Logging framework | [inferred] | |
| Testing framework | [inferred] | |

### 3.2 Infrastructure
| Component | Technology | Notes |
|-----------|-----------|-------|
| Container runtime | [Docker / containerd — from Dockerfile] | |
| Orchestration | [Kubernetes / ECS / none — from IaC] | |
| Cloud provider | [AWS / Azure / GCP — from SDK imports or IaC] | |
| CI/CD platform | [GitHub Actions / GitLab CI / Jenkins — from CI config] | |

### 3.3 Key Dependencies (Security-Relevant)
[List dependencies that directly affect the security posture, inferred from lock files and imports:
authentication libs, cryptography libs, HTTP clients, serialisation libs]

---

## 4. Data Flows

### 4.1 Data In Scope
[Infer from model/schema files and field names. Describe what types of data the system processes.
Flag any PII fields detected: [list from DP scan]]

### 4.2 Data Flow Description
[Describe how data moves through the system: user input → API → processing → storage → output.
Infer from route definitions and controller logic.]

### 4.3 Data at Rest
[Describe where data is stored and whether encryption is applied. Infer from DB config and
storage service references.]

### 4.4 Data in Transit
[Describe inter-component communication. Note whether TLS is enforced end-to-end.]

---

## 5. Control Implementation Summary

*Detailed assessment is in ASSESSMENT-REPORT.md. This section summarises the implementation
posture per domain.*

### AC — Access Control
[One paragraph summarising auth mechanisms found: frameworks, MFA status, RBAC/ABAC patterns,
credential management approach. Note any gaps flagged in the assessment.]

### AS — Application Security
[One paragraph: input validation approach, secrets management, access control enforcement,
output encoding. Note any injection risks or hardcoded secrets found.]

### SD — Secure Development
[One paragraph: CI/CD maturity, SAST tools, secret scanning, dependency management, branch
protection. Note gaps in the secure development pipeline.]

### LM — Logging & Monitoring
[One paragraph: logging framework, security event coverage, centralised logging, alerting.
Note any gaps in observability.]

### NS — Network Security
[One paragraph: TLS configuration, network segmentation (inferred from IaC), WAF,
security headers. Note any non-TLS endpoints found.]

### SC — Software Supply Chain
[One paragraph: dependency pinning, SBOM generation, container image security, package
verification. Note any unpinned dependencies or missing SBOM.]

### DP — Data Protection
[One paragraph: encryption at rest and in transit, PII handling, data classification,
retention controls. Note any sensitive data without encryption.]

### PM — Security Programme Management
[One paragraph: security policy documentation, vulnerability management process,
incident response readiness. Note missing documentation.]

---

## 6. Assumptions and Residual Risks

### 6.1 Automated Assessment Limitations
This SSP was generated by automated static code analysis. The following areas require
manual verification and cannot be fully assessed from code alone:

- Operational security procedures (security training, incident response drills)
- Runtime behaviour (dynamic application security testing not performed)
- Infrastructure configuration not captured in IaC (manually configured resources)
- Third-party/vendor security posture (requires vendor assessments)
- Physical security controls (for on-premises systems)
- Data classification accuracy (system owner must validate sensitivity of all data types)

### 6.2 Assumed Controls
The following controls are assumed to be in place at the platform/cloud provider level and
were not individually verified during this assessment:
[TO BE COMPLETED BY SYSTEM OWNER: list platform-managed controls, e.g. cloud provider physical
security, managed database encryption, CDN DDoS protection]

### 6.3 Accepted Risks
[TO BE COMPLETED BY SYSTEM OWNER: document any risks formally accepted by the system owner
with justification and compensating controls]

---

## 7. Review and Approval

| Role | Name | Date | Signature |
|------|------|------|-----------|
| System Owner | [TO BE COMPLETED] | | |
| Security Officer | [TO BE COMPLETED] | | |
| Approving Authority | [TO BE COMPLETED] | | |

**IM8 Portal submission status:** [Draft — not yet submitted]

---

*This document was initially generated by the Shift-Left SSP Assessor tool based on automated
codebase analysis. It must be reviewed, validated, and completed by the System Owner before
submission to the IM8 Portal.*
```
