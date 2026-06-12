# System Security Plan (SSP)

**Document version:** 1.0 (Draft — Shift-Left Assessment)
**Generated:** 2026-06-08
**System name:** skills — Claude Code Skill Collection (GenAI RAG System)
**System owner:** [TO BE COMPLETED BY SYSTEM OWNER: name and agency/organisation]
**SSP type:** Cloud Low-Risk + Generative AI (RAG)
**Prepared by:** Shift-Left SSP Assessor (automated static analysis)
**Next review date:** [TO BE COMPLETED BY SYSTEM OWNER: recommend annually or on major release]

---

## 1. System Overview

### 1.1 Purpose and Description

This system is a collection of Claude Code skills including a GenAI RAG (Retrieval-Augmented Generation) component. As inferred from `README.md`, the repository is described as "Collection of skills that I use" — a tooling and automation system built on top of a large language model (LLM). The system appears to be in an early/bootstrap stage with foundational skill definitions committed but application-layer code not yet present in the repository.

The system includes a Generative AI component (RAG pipeline) as confirmed by the system owner, which introduces additional controls from the Singapore Government GenAI SSP template.

### 1.2 System Classification

| Attribute | Value |
|-----------|-------|
| Deployment type | Cloud |
| Risk materiality level | Low |
| Development & maintenance | In-house |
| GenAI component | Yes — RAG pipeline |
| Data classification (highest) | [TO BE COMPLETED BY SYSTEM OWNER: Official-Open / Official-Closed / Sensitive / Restricted] |

> **Note:** Data classification must be confirmed by the System Owner before IM8 portal submission. The RAG pipeline's retrieval corpus and any user query data must be classified appropriately — if the corpus includes Official-Closed or higher data, the risk materiality level should be reassessed.

### 1.3 System Boundary

**In scope (detected from codebase):**
- Skill definition files (Markdown-based, Claude Code skill framework)
- Control catalog reference data
- SSP template reference data

**Not yet present / to be confirmed by System Owner:**
- RAG pipeline application code (LLM orchestration, retrieval, embedding)
- API layer / web interface
- Vector database / document store
- Authentication and access control layer
- Infrastructure-as-Code (IaC) definitions
- CI/CD pipeline

---

## 2. System Architecture

### 2.1 Architecture Overview

Based on static analysis, the repository is in an early development stage. The current commit history contains skill definition documents only. The described GenAI RAG architecture (as declared by the system owner) would typically comprise:

1. **User interface / API** — Accepts user queries
2. **Retrieval layer** — Searches a vector database or document index for relevant context
3. **LLM orchestration** — Constructs augmented prompts and calls an LLM provider API
4. **Data ingestion pipeline** — Indexes source documents into the retrieval store
5. **Output layer** — Returns generated responses to users

None of these components are present in the current codebase. Architecture confirmation is required from the System Owner.

### 2.2 Components Detected

| Component | Type | Technology | Notes |
|-----------|------|-----------|-------|
| Skill definitions | Documentation | Markdown | Present — shift-left-ssp-assessor skill |
| Control catalog reference | Data | Markdown | Present — embedded fallback for IM8 controls |
| RAG pipeline | Application | Not yet committed | Declared by system owner; code not present |
| LLM provider client | Integration | Unknown | Not yet detected |
| Vector database | Data | Unknown | Not yet detected |
| CI/CD | Infrastructure | None detected | No `.github/workflows/`, `.gitlab-ci.yml`, etc. |

### 2.3 Network Architecture

No IaC files detected. Network architecture cannot be inferred from the current codebase.

[TO BE COMPLETED BY SYSTEM OWNER: Describe VPCs, subnets, security groups, load balancers, CDN, and cloud provider region. Include a network diagram.]

### 2.4 External Integrations

No external API client code detected in current codebase.

[TO BE COMPLETED BY SYSTEM OWNER: List all external services the system calls, e.g. LLM provider API (OpenAI, Anthropic, Azure OpenAI), vector database service, identity provider, logging platform.]

---

## 3. Technology Stack

### 3.1 Languages and Frameworks

| Category | Technology | Version / Details |
|----------|-----------|------------------|
| Language | Not yet committed | No source files detected |
| Web framework | Not yet committed | — |
| LLM orchestration | Not yet committed | — (LangChain / LlamaIndex / custom suspected for RAG) |
| Authentication library | Not yet committed | — |
| Logging framework | Not yet committed | — |

[TO BE COMPLETED BY SYSTEM OWNER: Confirm technology stack once application code is committed.]

### 3.2 Infrastructure

| Component | Technology | Notes |
|-----------|-----------|-------|
| Cloud provider | Not determined | No IaC or SDK imports detected |
| Container runtime | None detected | No Dockerfile found |
| Orchestration | None detected | No Kubernetes/ECS manifests found |
| CI/CD platform | None detected | No CI config found |

### 3.3 Key Dependencies (Security-Relevant)

No dependency manifests found (`package.json`, `requirements.txt`, `go.mod`, `Cargo.toml`, etc.). No lock files detected. This is a critical gap — dependency management must be established before the system is deployed.

---

## 4. Data Flows

### 4.1 Data In Scope

No data model or schema files detected. Based on the declared RAG architecture, the following data types are expected to be in scope:

- **User queries** — Text input submitted by users; may contain PII depending on use case
- **Retrieval corpus** — Source documents indexed in the vector store; sensitivity depends on content
- **LLM prompts** — Augmented prompts constructed from user queries + retrieved context; inherits sensitivity of both
- **LLM responses** — Generated outputs; may contain sensitive information from the corpus
- **Embeddings** — Vector representations of documents/queries

[TO BE COMPLETED BY SYSTEM OWNER: Confirm data types, sensitivity classification, and whether any PII or Sensitive data is processed by the RAG pipeline.]

### 4.2 Data Flow Description

[TO BE COMPLETED BY SYSTEM OWNER: Describe the flow: user input → retrieval → prompt augmentation → LLM call → response. Include a data flow diagram.]

### 4.3 Data at Rest

[TO BE COMPLETED BY SYSTEM OWNER: Describe the vector database, document store, and any caches. Confirm encryption at rest is enabled.]

### 4.4 Data in Transit

[TO BE COMPLETED BY SYSTEM OWNER: Confirm TLS is enforced for all connections: user ↔ API, API ↔ vector DB, API ↔ LLM provider.]

---

## 5. Control Implementation Summary

*Detailed assessment is in ASSESSMENT-REPORT.md. This section summarises the current implementation posture. As the codebase is in an early stage, most domains show gaps or require manual confirmation.*

### AC — Access Control
No authentication or authorisation code is present in the current codebase. Access control mechanisms for the RAG API, LLM provider credentials, and vector database access have not been implemented or documented yet. The System Owner must define the access control architecture before development proceeds.

### AS — Application Security
No application code is present to assess. No hardcoded secrets or credentials were found in the current repository (positive finding). However, there is no secrets manager configuration, no input validation framework, and no evidence of access control enforcement middleware. As the RAG pipeline handles user-supplied queries, prompt injection defence (AI-1) is a critical upcoming requirement.

### SD — Secure Development
No CI/CD pipeline is configured. No secret scanning (`.gitleaks.toml`, pre-commit hooks, GitHub push protection) is in place. No dependency lock files are present. The secure development pipeline must be established before any code handling user data or LLM calls is deployed. These are Level 0 gaps requiring immediate action.

### LM — Logging & Monitoring
No logging framework or monitoring configuration is present. For a GenAI system, AI output logging (AI-4) is particularly important for detecting anomalous outputs, bias, and misuse. Logging infrastructure must be designed alongside the application layer.

### NS — Network Security
No network configuration, IaC, or TLS setup is present. Cloud network segmentation, TLS enforcement, and infrastructure security controls must be addressed when the system is deployed to cloud infrastructure.

### SC — Software Supply Chain
No dependency manifests or lock files are present. For a RAG system, the supply chain includes LLM orchestration libraries (e.g., LangChain, LlamaIndex), embedding libraries, and vector database clients — all of which must be pinned and scanned. An SBOM should be generated once dependencies are introduced.

### DP — Data Protection
No data handling code or storage configuration is present. Given the RAG architecture, data protection is high-stakes: the retrieval corpus and user queries may contain sensitive information. Encryption at rest and in transit, PII scrubbing before LLM calls (AI-2), and data classification must be addressed at design time.

### PM — Security Programme Management
No `SECURITY.md`, security policy, or incident response documentation is present. Foundational programme management documents should be created early in the project, even before the application code is fully developed.

---

## 6. Assumptions and Residual Risks

### 6.1 Automated Assessment Limitations

This SSP was generated by automated static code analysis. The following require manual verification:

- Runtime behaviour and dynamic security testing (not performed)
- Cloud infrastructure configuration (no IaC present to analyse)
- LLM provider security posture (requires vendor assessment of Anthropic/OpenAI/Azure OpenAI or chosen provider)
- Vector database security configuration (requires deployment-time review)
- Operational procedures: security training, incident response drills, on-call runbooks
- Prompt injection resilience (requires adversarial testing beyond static analysis)
- Data classification accuracy (System Owner must validate all data types in the RAG pipeline)

### 6.2 Assumed Controls

[TO BE COMPLETED BY SYSTEM OWNER: List controls assumed to be managed by the cloud platform, e.g.:
- Cloud provider physical security (AWS/Azure/GCP data centre)
- Managed TLS termination at load balancer
- Cloud provider DDoS baseline protection
- LLM provider API-layer rate limiting]

### 6.3 Accepted Risks

[TO BE COMPLETED BY SYSTEM OWNER: Document any risks formally accepted, with justification and compensating controls.]

---

## 7. Review and Approval

| Role | Name | Date | Signature |
|------|------|------|-----------|
| System Owner | [TO BE COMPLETED] | | |
| Security Officer | [TO BE COMPLETED] | | |
| Approving Authority | [TO BE COMPLETED] | | |

**IM8 Portal submission status:** Draft — not yet submitted

---

*This document was generated by the Shift-Left SSP Assessor tool based on automated codebase analysis (2026-06-08). It must be reviewed, validated, and completed by the System Owner before submission to the IM8 Portal. All `[TO BE COMPLETED BY SYSTEM OWNER]` placeholders must be resolved prior to submission.*
