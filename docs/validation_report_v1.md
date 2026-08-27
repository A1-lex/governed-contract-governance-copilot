# Validation Report v1 — Contract Governance Copilot

**Project:** Governed Enterprise Knowledge and Action Agent (Project 1)
**Date:** 2026-08-13
**Tester:** AlexUser
**Deadline:** Thursday 5pm — met with buffer

---

## 1. Retrieval validation (Azure AI Search — Search Explorer)

**Index:** `idx-contract-governance`
**Search service:** `srch-govknow-proto`
**Pipeline:** index + indexer + data source + skillset (Import & vectorize wizard)
**Embedding model:** text-embedding-3-large (`oai-govknow-proto`, system-assigned MI)
**Query mode:** hybrid (keyword + vector) + semantic ranker ON

### Result: 5 / 5 benchmark questions PASS
Every question returned the correct source document as the #1 result. The `title`
field returns the source filename on every hit, confirming citations are viable.

| # | Question | Expected Source | Top Result (title) | Answer Score | Reranker | Verdict |
|---|----------|-----------------|--------------------|-------------|----------|---------|
| 1 | What approvals are required for NDAs? | Approval Matrix.pdf | Approval Matrix.pdf | 0.93 | 2.98 | PASS |
| 2 | When should Legal review a contract? | Legal Escalation Guidelines.pdf | Legal Escalation Guidelines.pdf | 0.98 | 3.24 | PASS |
| 3 | What documents are required before procurement approval? | Procurement Policy.pdf | Procurement Policy.pdf | 0.93 | 3.38 | PASS |
| 4 | Who owns supplier onboarding? | Supplier Onboarding Process.pdf | Supplier Onboarding Process.pdf | 0.99 | 3.10 | PASS |
| 5 | What is the standard contract review workflow? | Contract Review Procedure.pdf | Contract Review Procedure.pdf | 0.91 | 3.36 | PASS |

---

## 2. Agent validation (Copilot Studio Preview)

| Test | Expected behaviour | Result |
|------|--------------------|--------|
| Grounded answer + citation | Answer from approved doc with citation card | PASS — e.g. review workflow answer cited *Contract Review Procedure.pdf* |
| Citation accuracy | Citation points to correct source PDF | PASS (all spot-checks) |
| Out-of-domain refusal | Declines "parental leave policy", names in-scope topics, offers to escalate | PASS |
| Escalation action | Calls "Notify content owner" and sends email | PASS — email received at content-owner@example.com |
| Feedback capture | 👍/👎 shown per response | PASS (built-in) |

### Escalation evidence
- Email subject: **"Knowledge gap reported by Contract Governance Copilot"**
- From: agent-owner@example.com
- Body correctly named the missing topic (parental leave policy) and requested the
  owner add/link the approved source.
- One-time connector consent ("Permission Required: shared_office365 → SendEmailV2")
  approved via **Allow** — expected security behaviour, not a defect.

---

## 3. Success criteria scorecard

| MVP success criterion | Status |
|-----------------------|--------|
| Grounded answers from approved documents | ✅ Met |
| Citations to source document | ✅ Met |
| Correct refusal on out-of-domain questions | ✅ Met |
| Human escalation action (notify content owner) | ✅ Met (live email proven) |
| Feedback capture (👍/👎) | ✅ Met (built-in) |
| Delivered in Teams | ✅ Published to Teams + Microsoft 365 |

**Overall: prototype meets all MVP success criteria.**

---

## 4. Foundation established for Project 2 (Contract Intelligence Copilot)

Reusable patterns proven this sprint:
- Curated, metadata-tagged contract-governance knowledge base
- Retrieval + citation pattern (Azure AI Search index validated 5/5)
- Grounded, citation-only answering behaviour
- Human-in-the-loop escalation/action pattern (email owner)
- Feedback loop
- Teams delivery experience

---

## 5. Follow-ups (post-demo / production hardening)

1. Promote to a managed (Dataverse) environment and attach the **Azure AI Search
   index** as a first-class Knowledge source (replaces file-upload knowledge).
2. Add per-user document-level security trimming.
3. Durable feedback logging (ratings → table) and a knowledge-gap dashboard.
4. Expand escalation routing (per-topic content owners).
