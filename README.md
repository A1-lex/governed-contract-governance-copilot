# Governed Enterprise Knowledge and Action Agent

**Contract Governance Copilot** — a governed, citation-grounded knowledge agent for
contract, procurement, NDA, supplier-onboarding and legal-review governance, delivered
in Microsoft Teams + Microsoft 365.

This is **Project 1** of a two-project roadmap. It deliberately establishes the reusable
foundation (secure knowledge, retrieval + citations, feedback, human escalation,
governance) for **Project 2 — Contract Intelligence Copilot**.

---

## What it does

- Answers governance questions **only** from approved documents.
- **Cites** the source document in every answer.
- **Refuses** out-of-domain questions instead of hallucinating.
- **Escalates** knowledge gaps to a human by emailing the content owner.
- Captures **feedback** (👍/👎) on every response.
- Runs in **Microsoft Teams + Microsoft 365**.

## Status

✅ Prototype complete — all MVP success criteria met and validated. Published to
Teams + Microsoft 365.

---

## Architecture (frozen)

```
Curated PDFs (ai_data/)
        │
Azure Blob Storage  ──►  Azure AI Search  ──►  Azure OpenAI (text-embedding-3-large)
        │                     (idx-contract-governance, 5/5 validated)
        │
Copilot Studio agent  ──►  Microsoft Teams / M365
        │
Office 365 Outlook (Send email) = "Notify content owner" escalation
```

> Demo note: because the build environment is a Default (personal) environment,
> the agent's demo knowledge is attached as **file-upload knowledge**. The Azure AI
> Search index (`idx-contract-governance`, validated 5/5) is retained as the reusable
> retrieval layer to promote for Project 2 in a managed environment. See
> `docs/decision_log.md` (Decision 006).

---

## Repository map

| Path | Purpose |
|------|---------|
| `ai_data/` | The 5 curated contract-governance PDFs (knowledge base) |
| `docs/agent_configuration.md` | Full agent build record (instructions, tools, channels) |
| `docs/validation_report_v1.md` | Retrieval + agent validation results & scorecard |
| `docs/demo_evidence.md` | Screenshots proving each success criterion |
| `docs/images/` | Evidence screenshots |
| `docs/decision_log.md` | Why non-obvious decisions were made |
| `docs/whys.md`, `docs/challenges.md`, etc. | Planning & governance docs |

---

## Knowledge base

| Document | Governs |
|----------|---------|
| Approval Matrix.pdf | Contract & NDA approval thresholds |
| Procurement Policy.pdf | Required documentation, review cadence |
| Legal Escalation Guidelines.pdf | Mandatory legal review, dispute escalation |
| Supplier Onboarding Process.pdf | Onboarding roles & steps |
| Contract Review Procedure.pdf | Standard contract review workflow |

---

## Success criteria — all met

| Criterion | Status |
|-----------|--------|
| Grounded answers from approved documents | ✅ |
| Citations to source document | ✅ |
| Correct refusal on out-of-domain questions | ✅ |
| Human escalation action (email owner) | ✅ (live email proven) |
| Feedback capture (👍/👎) | ✅ |
| Delivered in Teams | ✅ |

See `docs/validation_report_v1.md` for the evidence.

---

## Next: Project 2 — Contract Intelligence Copilot

Reuses this foundation and adds document extraction, clause playbook comparison,
risk detection, and human-approved workflow actions. Production-hardening follow-ups
(managed environment + Azure AI Search knowledge, per-user security trimming, durable
feedback logging) are listed in `docs/validation_report_v1.md`.
