# Contract Governance Copilot

A governed, citation-grounded assistant that answers contract and procurement
policy questions in Microsoft Teams, using only the organisation's approved
governance documents.

This is the first of two related builds. The second, the **Contract
Intelligence Copilot**, applies these same governance documents to supplier
agreements: where this assistant answers what the policy says, the second
assesses whether a given contract complies with it.

**Full project documentation:**
[`docs/Contract Governance Copilot - Project Documentation.pdf`](docs/)

---

## What it does

- Answers governance questions **only** from approved documents
- **Cites** the source document in every answer
- **Refuses** out-of-domain questions rather than answering from general
  knowledge
- **Escalates** knowledge gaps by emailing a fixed document owner
- Runs in **Microsoft Teams** and **Microsoft 365 Copilot**

## Status

Built, validated and published.

---

## Architecture

```
Approved documents (ai_data/)
        │
Azure Blob Storage
        │
        ▼
Azure AI Search  ──►  hourly indexing + vectorisation
        │
        ▼
Microsoft Foundry knowledge base
        │              agentic retrieval: query planning,
        │              parallel search, semantic reranking
        ▼
Power Automate workflow
        │
        ▼
Copilot Studio agent  ──►  Microsoft Teams / M365
        │
Office 365 Outlook  ──►  "Notify content owner" escalation
```

Adding a policy means placing a file in Blob Storage. It becomes answerable
after the next hourly ingestion, with no change to the assistant.

---

## Knowledge base

| Document | Governs |
|----------|---------|
| Approval Matrix | Contract and NDA approval thresholds |
| Procurement Policy | Required documentation, review cadence |
| Legal Escalation Guidelines | Mandatory legal review, dispute escalation |
| Supplier Onboarding Process | Onboarding roles and steps |
| Contract Review Procedure | Standard contract review workflow |
| Contract Renewal and Termination Policy | Notice windows, auto-renewal, termination |
| Conflict of Interest and Gifts Policy | Declarations, gift limits, breach handling |

---

## Behaviour

| Behaviour | What it means |
|-----------|---------------|
| Grounded | Answers come only from the approved document set |
| Cited | Every answer names the source document |
| Honest about gaps | States when an answer is not in the approved documents |
| Actionable | An unanswered question becomes an email to the document owner |

---

## Validation

| Scenario | Required behaviour |
|----------|-------------------|
| In-scope question | Grounded answer ending with a named source document |
| Out-of-domain question | Declines, with no answer from general knowledge |
| In-scope topic, absent from documents | States the gap, offers to notify |
| Notification confirmed | Email sent to the fixed recipient |
| Newly added document | Answerable after the next scheduled ingestion |

Evidence for each is in the project documentation.

---

## Repository map

| Path | Purpose |
|------|---------|
| `ai_data/` | The approved governance documents |
| `docs/Contract Governance Copilot - Project Documentation.pdf` | Full project documentation |
| `docs/agent_configuration.md` | Agent build record: instructions, tools, channels |
| `docs/decision_log.md` | Non-obvious decisions and their rationale |
| `docs/validation_report_v1.md` | Validation results |
| `docs/demo_evidence.md` | Evidence screenshots |
| `docs/architecture.md` | Architecture notes |
| `docs/whys.md`, `docs/challenges.md` | Planning and governance notes |

---

## Known limitations

- Answer quality is bounded by document quality. Outdated policy produces
  outdated answers, so document ownership and review dates are part of the
  operating model.
- Grounding is enforced by instruction rather than by architecture. It holds
  consistently in testing but should be re-tested after any model or
  instruction change.
- All reviewers see the same document set. Per-user permission trimming is a
  production-hardening item.
- Agentic retrieval is called through a preview API. The underlying index is
  generally available and provides a stable fallback.

---

## Roadmap

- Permission-aware retrieval, restricting answers by reviewer entitlement
- Knowledge-gap reporting, aggregating unanswered questions into a content
  roadmap
- Usage and confidence signals
- Extension to adjacent policy domains using the same ingestion and citation
  pattern
