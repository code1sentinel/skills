---
name: shift-left-ssp-creator
description: >
  Scan a codebase and generate a Singapore Government IM8/ICT&SS-aligned System Security Plan
  (SSP). Use when: a team needs to create an SSP for IM8 portal submission, document a system's
  security posture, generate an SSP from a codebase, create a system security plan for a new
  project, or prepare an IM8 SSP document for a cloud or on-premises system. Trigger on:
  "create SSP", "generate SSP", "write SSP", "create system security plan", "IM8 SSP",
  "document my system", "prepare SSP for IM8", "SSP for cloud system", "shift-left SSP creator".
---

# Shift-Left SSP Creator

Scan a codebase to generate a Singapore Government IM8-aligned **System Security Plan (SSP)**
based on the system's actual architecture, technology stack, and declared configuration.

The SSP follows the structure from [info.standards.tech.gov.sg/ssp/](https://info.standards.tech.gov.sg/ssp/)
and is ready for review and submission to the IM8 portal once the System Owner completes
all `[TO BE COMPLETED BY SYSTEM OWNER]` placeholders.

**Output:** `SSP.md` written to the root of the assessed codebase (or `security/SSP.md` if a `security/` dir exists)

> **Pair with** `/shift-left-ssp-assessor` to generate the compliance matrix and gap report after the SSP is created.

---

## Workflow

Create a checklist and work through each step in order.

---

### Step 1 — Classify the System

First, check whether `SSP.md` already exists in the codebase root or `security/` directory.
If it does and contains a System Classification table, read the answers from there and skip to Step 2.

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

Wait for answers before continuing. The SSP type is determined as follows:

| Q1 | Q2 | Q4 | SSP Type |
|----|----|----|---------|
| Cloud | Low | No | Cloud Low-Risk |
| Cloud | Medium | No | Cloud Medium-Risk |
| Cloud | High/CII | No | Cloud High-Risk |
| On-Premises | Low | No | On-Premises Low-Risk |
| SaaS | Any | No | SaaS |
| Any | Any | Yes | [above type] + Generative AI |

Fetch the applicable SSP template for reference:
```
https://info.standards.tech.gov.sg/ssp/                   (overview)
https://info.standards.tech.gov.sg/ssp/low-risk-cloud/    (if Cloud Low)
https://info.standards.tech.gov.sg/ssp/medium-risk-cloud/ (if Cloud Medium)
https://info.standards.tech.gov.sg/ssp/high-risk-cloud/   (if Cloud High/CII)
https://info.standards.tech.gov.sg/ssp/low-risk-on-premises/ (if On-Premises)
https://info.standards.tech.gov.sg/ssp/gen-ai/            (if GenAI = Yes)
```
If these return 403, load the fallback:
> 📋 Read `./references/ssp-template.md`

---

### Step 2 — Scan Codebase for System Information

Run searches to infer the system's architecture, tech stack, and data profile.
Maximise parallel calls.

#### System Identity
- Read `README.md` — extract system name, purpose, and description
- `Glob` for `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, `pom.xml`, `build.gradle` — extract project name and version
- `Glob` for `CHANGELOG.md` — note current version

#### Technology Stack
- Detect primary language from file extensions: `.py`, `.js`, `.ts`, `.go`, `.java`, `.cs`, `.rb`, `.rs`
- `Glob` for framework config: `next.config.*`, `nuxt.config.*`, `angular.json`, `fastapi`, `django`, `flask`, `spring`, `rails`
- `Glob` for dependency manifests: `package.json`, `requirements.txt`, `go.mod`, `Cargo.toml`, `pom.xml`
- Read one dependency file to identify key libraries (auth, ORM, HTTP client, logging, crypto)

#### Architecture & Infrastructure
- `Glob` for `Dockerfile`, `docker-compose*.yml` — identify containerised services
- `Glob` for IaC: `*.tf`, `*.cfn.yml`, `cloudformation*.json`, `k8s/`, `helm/` — identify cloud provider and services
- `Glob` for `kubernetes/*.yml`, `helm/*.yml`, `manifests/` — identify orchestration
- `Glob` for CI/CD: `.github/workflows/*.yml`, `.gitlab-ci.yml`, `Jenkinsfile`, `azure-pipelines.yml`

#### Data Profile
- `Glob` for schema/model files: `models/`, `schemas/`, `entities/`, `migrations/`
- `Grep` for PII field names in models: `email`, `phone`, `nric`, `passport`, `dob`, `address`, `full_name`
- `Grep` for database connection strings or ORM config to identify data stores
- `Grep` for external API clients: `requests`, `axios`, `fetch`, `HttpClient`, `RestTemplate`

#### GenAI / RAG specifics (if Q4 = Yes)
- `Grep` for LLM client imports: `anthropic`, `openai`, `langchain`, `llama_index`, `llamaindex`, `transformers`, `litellm`
- `Grep` for vector DB clients: `pinecone`, `weaviate`, `qdrant`, `chromadb`, `pgvector`, `faiss`
- `Grep` for embedding patterns: `embed`, `embedding`, `vectorize`, `encode`
- `Grep` for RAG pipeline patterns: `retriev`, `augment`, `context_window`, `system_prompt`
- `Glob` for prompt template files: `prompts/`, `templates/*.txt`, `system_prompt*`

#### Security Documentation
- `Glob` for `SECURITY.md`, `.github/SECURITY.md`, `docs/security*`
- `Glob` for architecture docs: `docs/architecture*`, `docs/design*`, `ADR/`, `decisions/`
- `Glob` for `docs/data-flow*`, `docs/network*`, `docs/runbook*`

---

### Step 3 — Generate SSP

Write `SSP.md` to the codebase root (or `security/SSP.md` if `security/` exists).

> 📋 Load the SSP section template: read `./references/ssp-template.md`

Populate every section from the Step 2 scan results. Apply these rules:

- **Infer aggressively from code** — tech stack, architecture, data types, external integrations
- **Use placeholders for gaps** — `[TO BE COMPLETED BY SYSTEM OWNER: <specific guidance>]`
- **Flag GenAI-specific sections** — if Q4 = Yes, add a GenAI subsection in Section 2 and Section 4
- **Name actual files** — when describing the tech stack, cite the specific file you found it in
  (e.g. "Python 3.x (inferred from `pyproject.toml`)")
- **Don't invent what isn't there** — if network architecture cannot be inferred, say so and use placeholder

---

### Step 4 — Commit and Push

```bash
git add SSP.md        # or security/SSP.md
git commit -m "Add IM8 System Security Plan (shift-left draft)"
git push -u origin <current-branch>
```

---

## Wrap Up

```
## SSP Created ✅

System:       [name]
SSP type:     [Cloud Low-Risk / On-Premises / SaaS / GenAI etc.]
File:         SSP.md

Sections completed from codebase:
  ✅ System Overview         ✅ Technology Stack
  ✅ System Architecture     [✅/⚠] Data Flows
  [✅/⚠] Control Summary    ✅ Residual Risks

Placeholders remaining: N × [TO BE COMPLETED BY SYSTEM OWNER]
  → Open SSP.md and resolve each placeholder before IM8 portal submission

Next step: Run /shift-left-ssp-assessor to generate the compliance matrix
and gap report against the ICT&SS control catalog.
```
