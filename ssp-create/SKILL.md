---
name: ssp-create
description: Generate a Singapore Government IM8/ICT&SS-aligned System Security Plan (SSP) from
  a codebase. Use when creating an SSP for IM8 portal submission, documenting a system's security
  posture, or starting a new project. Trigger on: "create SSP", "generate SSP", "write SSP",
  "IM8 SSP", "document my system", "prepare SSP", "system security plan", "ssp-create".
---

## Context

Ask these four questions before scanning (or read from existing SSP.md System Classification table):

- **Q1 Deployment:** Cloud | On-Premises | SaaS
- **Q2 Risk level:** Low | Medium | High | CII
- **Q3 Dev model:** In-house | Outsourced-Local | Outsourced-Offshore | Mixed *(skip if SaaS)*
- **Q4 GenAI:** Yes | No

System identity:
```
head -20 README.md 2>/dev/null
cat package.json 2>/dev/null | grep -E '"name"|"version"|"description"'
cat pyproject.toml 2>/dev/null | grep -E '^name|^version|^description'
cat go.mod 2>/dev/null | head -3
```

Tech stack — language, framework, key dependencies:
```
find . -not -path './.git/*' -name "*.py" -o -name "*.ts" -o -name "*.go" -o -name "*.java" | head -5
cat requirements.txt 2>/dev/null || cat Pipfile 2>/dev/null || cat poetry.lock 2>/dev/null | head -5
cat package.json 2>/dev/null | python3 -c "import sys,json; d=json.load(sys.stdin); print(list(d.get('dependencies',{}).keys())[:10])"
```

Architecture and infrastructure:
```
find . -not -path './.git/*' -name "Dockerfile" -o -name "docker-compose*.yml" | head -5
find . -not -path './.git/*' -name "*.tf" -o -name "*.cfn.yml" -o -name "*.cfn.yaml" | head -5
find . -not -path './.git/*' -path "*/k8s/*" -o -path "*/helm/*" -o -path "*/manifests/*" | head -5
find . -not -path './.git/*' -path "./.github/workflows/*" -name "*.yml" | head -3
```

Data profile — PII fields and external calls:
```
grep -r "email\|phone\|nric\|passport\|dob\|address\|full_name" --include="*.py" --include="*.ts" --include="*.js" --include="*.go" -l 2>/dev/null | head -10
grep -r "requests\.\|axios\|fetch(\|HttpClient\|RestTemplate" --include="*.py" --include="*.ts" --include="*.js" --include="*.java" -l 2>/dev/null | head -5
```

GenAI/RAG specifics *(run only if Q4 = Yes)*:
```
grep -r "anthropic\|openai\|langchain\|llama_index\|litellm\|transformers" --include="*.py" --include="*.ts" -l 2>/dev/null | head -5
grep -r "pinecone\|weaviate\|qdrant\|chromadb\|pgvector\|faiss" --include="*.py" --include="*.ts" -l 2>/dev/null | head -5
find . -not -path './.git/*' -path "*/prompts/*" -o -name "system_prompt*" | head -5
```

Fetch the SSP template from source of truth (fallback: read `./references/ssp-template.md`):
```
curl -s https://info.standards.tech.gov.sg/ssp/ | head -200
```

## Constraints

- Never describe architecture that isn't evidenced in a specific file — cite it by path
- Every unknown value gets `[TO BE COMPLETED BY SYSTEM OWNER: <specific guidance>]` — never leave a blank field
- If GenAI = Yes, add a GenAI subsection in both Architecture (§2) and Data Flows (§4)
- The System Classification table (Q1–Q4) must always be present — the `/ssp-review` skill reads it
- Never use generic filler like "standard security practices apply" — be specific or use a placeholder
- Do not assess compliance — that belongs to `/ssp-review`

## Structure

1. Write `SSP.md` to repo root (or `security/SSP.md` if `security/` exists)
2. Sections in order: System Overview → System Classification table → Architecture → Technology Stack → Data Flows → Control Implementation Summary → Residual Risks → Review & Approval
3. Commit: `git add SSP.md && git commit -m "Add IM8 SSP (shift-left draft)" && git push -u origin <branch>`
4. Wrap-up one-liner: placeholder count, file path, and prompt to run `/ssp-review`
