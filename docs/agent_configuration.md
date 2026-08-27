# Agent Configuration — Contract Governance Copilot

**Project:** Governed Enterprise Knowledge and Action Agent (Project 1)
**Purpose:** Foundation prototype for the future Contract Intelligence Copilot (Project 2)
**Environment:** Default (personal) — `Default-65eebeca-8aa8-45d5-91de-20a328ba1bf7`
**Schema name:** `cr7a9_contractgovernance`
**Solution:** Default Solution
**Primary language:** English
**Model:** GPT-5.6 Reasoning
**Status:** Published to Microsoft Teams + Microsoft 365
**Owner:** AlexUser
**Date documented:** 2026-08-13

---

## 1. Agent instructions (system prompt)

> Answer questions about contract, procurement, NDA, supplier onboarding and legal
> review governance using only the connected knowledge. Always cite the source
> document. If the answer isn't in the knowledge, say so and offer to notify the
> content owner.

Design intent: grounded, citation-only answers; explicit refusal on out-of-domain
questions; a single human-in-the-loop escalation action ("notify content owner").

---

## 2. Knowledge sources (grounding)

Attached as Copilot Studio file-upload knowledge (5 curated PDFs):

| # | Document | Governs | Answers benchmark Q# |
|---|----------|---------|----------------------|
| 1 | Approval Matrix.pdf | Contract & NDA approval thresholds | 1, 2 |
| 2 | Procurement Policy.pdf | Required documentation, review cadence | 6, 7 |
| 3 | Legal Escalation Guidelines.pdf | Mandatory legal review, dispute escalation | 3, 5 |
| 4 | Supplier Onboarding Process.pdf | Onboarding roles & steps | 4 |
| 5 | Contract Review Procedure.pdf | Standard review workflow | 8 |

Grounding hygiene:
- "Search all websites" / public web knowledge **removed** — answers are limited to
  the 5 approved documents only.
- Out-of-domain questions are refused (verified with "parental leave policy" test).

---

## 3. Tools / actions

**Tool:** Office 365 Outlook — Send an email (V2)  (connector: `shared_office365`)
**Named:** Notify content owner
**Trigger:** When information is not in the connected knowledge and the user asks to
notify, the agent calls this tool and confirms the owner was emailed.

| Field | How filled | Value |
|-------|-----------|-------|
| To | Value (literal) | content-owner@example.com |
| Subject | Value | Knowledge gap reported by Contract Governance Copilot |
| Body | AI | Model composes a short notice naming the missing topic and asking the owner to add/link the approved source |
| From (Send as) | Value (empty) | defaults to signed-in mailbox (agent-owner) |
| CC / Attachments / Sensitivity / Reply To / Importance | left default | optional, unused |

First-run behaviour: the connector shows a one-time "Permission Required
(shared_office365 → SendEmailV2)" consent card; user clicks **Allow** once per session.

`Note: the actual recipient address is configured in the tool and redacted here for publication.`
---

## 4. Feedback capture

Built-in per-response feedback (👍 / 👎 + copy) is enabled by default under each agent
reply. Satisfies the MVP "capture feedback" metric. Durable feedback logging (writing
ratings to a table) is deferred to Project 2 / production hardening.

---

## 5. Channels

- **Teams + Microsoft 365** — enabled (primary delivery channel for the demo).
- Demo Website / Web app — not used (would require "No authentication"; intentionally
  avoided to keep the agent authenticated/governed).

---

## 6. Known limitations (documented, intentional)

1. **Retrieval layer for the demo = file-upload knowledge**, not the Azure AI Search
   index. Reason: the Default (personal) environment does not expose Azure AI Search
   as a *Knowledge* source (only as a Tool/connector). See Decision 006.
2. The Azure AI Search index `idx-contract-governance` (5/5 validated) is retained as
   the reusable retrieval layer for Project 2 / production, to be promoted in a
   managed (Dataverse) environment.
3. Per-user document-level security trimming not implemented in the prototype (all
   pilot users share the same curated content) — production-hardening item.
