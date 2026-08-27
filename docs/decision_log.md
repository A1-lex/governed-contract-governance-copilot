# Decision Log — Governed Enterprise Knowledge and Action Agent

A running record of non-obvious decisions, why they were made, alternatives
considered, and their consequences. Kept for governance, auditability, and to
inform Project 2 (Contract Intelligence Copilot).

---

## Decision 001 — MVP domain
**Decision:** Use *Contract Governance Knowledge* as the initial domain.
**Reason:** Directly aligns with the future Contract Intelligence Copilot, so the
knowledge, retrieval, and governance patterns are reusable rather than throwaway.
**Alternatives considered:** HR policies, IT knowledge base, compliance procedures.
**Consequences:** Slightly smaller demo audience, but maximum strategic reuse for
Project 2.

---

## Decision 002 — Curated documents over enterprise-wide indexing
**Decision:** Ground the agent on a small set of curated, approved PDFs.
**Reason:** Improves answer quality and trust, avoids stale/conflicting content,
and reduces permission complexity for the prototype.
**Alternatives considered:** Index all enterprise content.
**Consequences:** Smaller knowledge base; controlled, high-quality answers.

---

## Decision 003 — Require citations in every answer
**Decision:** Every grounded answer must reference its source document.
**Reason:** Builds user trust and supports auditability.
**Consequences:** Some answers are shorter or explicitly decline; the same citation
behaviour is required for contract review in Project 2.

---

## Decision 004 — One escalation action only
**Decision:** Implement a single action — notify the content owner.
**Reason:** Demonstrates human-in-the-loop action without over-automation or scope
creep.
**Consequences:** Limited workflow automation in the prototype; the pattern is
reusable for contract-exception routing later.

---

## Decision 005 — Accept the Azure AI Search retrieval layer as-is
**Decision:** Accept the retrieval layer without chunking changes.
**Reason:** 5/5 benchmark questions returned the correct source as the top hit with
strong reranker scores (Search Explorer validation).
**Consequences:** Moved straight to the agent build; tuning budget preserved for
after the demo.

---

## Decision 006 — File-upload knowledge for the demo (not Azure AI Search)
**Decision:** For the prototype demo, attach the 5 governance PDFs as Copilot Studio
file-upload knowledge instead of the Azure AI Search index.
**Reason:** The Default (personal) environment does not expose Azure AI Search as a
*Knowledge* source (only as a Tool/connector). File-upload knowledge still delivers
grounded, citation-backed answers and meets the success criteria.
**Consequences:** The Azure AI Search index (`idx-contract-governance`, validated
5/5) is retained as the reusable retrieval layer for Project 2 / production, to be
promoted in a managed (Dataverse) environment.

---

## Decision 007 — Hard-lock the escalation recipient
**Decision:** The "Notify content owner" email recipient is fixed and pre-configured;
the agent must never request or accept a user-supplied recipient.
**Reason:** Observed that the agent asked the user for a recipient and then sent to
whatever address was typed — a governance risk (internal knowledge-gap notices could
be redirected to arbitrary addresses).
**Fix:** Instruction guardrail ("never ask for or use a user-provided email") + tool
input `To` = Value (literal `content-owner@example.com`), no ask-if-empty.
**Status:** Applied and re-validated.
**Project 2 note:** Enforce per-topic owner routing via configuration, never user input.

---

## Decision 008 — Enforce strict grounding (no general-knowledge leak)
**Decision:** Add a strict grounding rule to instructions and set Moderation to High
so the agent answers ONLY from connected knowledge and refuses out-of-domain
questions (general knowledge, dates/times, geography, law, definitions).
**Reason:** Observed a grounding leak — with GPT-5.6 Reasoning orchestration the model
answered general-knowledge questions (e.g. "children's rights", "capital of Africa",
current time) that are not in the knowledge base. This build exposes no "use general
knowledge" toggle, so grounding is enforced via instructions + moderation.
**Residual risk:** Instruction-based grounding is strong but not a hard guarantee on a
reasoning model. Project 2 to enforce via knowledge-only orchestration in a managed
environment.
**Status:** Applied and re-validated with off-domain tests.

---

## Decision 009 — Tool carve-out so grounding doesn't disable escalation
**Decision:** Explicitly state that the strict grounding rule applies ONLY to
answering questions and does NOT restrict tools; the agent is always allowed to call
the "Notify content owner" (Send an email) tool.
**Reason:** After tightening grounding, the agent began reporting "the notification
tool is unavailable" — it over-applied the strict rule and treated the email tool as
disallowed.
**Status:** Applied; escalation email sends again after the carve-out.

---

## Decision 010 — Mandatory "Source:" line to restore citations
**Decision:** Require every grounded answer to end with a line in the exact format
`Source: <document name>`.
**Reason:** With strict grounding, the reasoning model heavily reformatted answers
(headings/tables) and the citation *chip* often failed to attach. An in-text
`Source:` line makes citations reliable and screenshot-able regardless of whether the
chip renders.
**Status:** Applied and re-validated (e.g. NDA answer cited Approval Matrix.pdf,
Procurement Policy.pdf, Supplier Onboarding Process.pdf).
**Project 2 note:** Prefer native citation cards once on Azure AI Search knowledge in
a managed environment; keep the `Source:` line as a fallback.

## Decision 011 — Replace manual file-upload knowledge with automated ingestion + Foundry IQ
**Supersedes:** Decision 006 (file-upload knowledge for the demo).
**Decision:** Move the knowledge layer to Azure Blob Storage + a scheduled Azure AI
Search indexer, surfaced to the Copilot Studio agent via a Foundry IQ knowledge base.
**Reason:** Supervisor review identified that manual file-upload knowledge is static and
does not scale — 1,000 documents would require 1,000 manual uploads, defeating the
purpose of an enterprise knowledge agent.
**Clarification:** This is not "Foundry instead of Copilot Studio". Copilot Studio remains
the agent/experience layer; Foundry IQ (built on Azure AI Search) becomes the managed
knowledge layer. The two are complementary.
**Storage choice:** Azure Blob Storage (unstructured documents). Azure Cosmos DB is
deferred to Project 2, where structured contract metadata is actually required.
**MCP:** Deferred. MCP-server knowledge sources are a future/Project 2 item; connecting
the Foundry IQ knowledge base achieves the scalability goal without it.
**Consequences:** New documents are ingested automatically on a schedule; the knowledge
base becomes reusable across agents (including Project 2); permission-aware retrieval
becomes possible. Some Foundry IQ capabilities are in preview — the validated Azure AI
Search index is retained as the fallback.
**Status:** In progress.

## Decision 012 — SharePoint connector as the knowledge source (dynamic ingestion)

**Supersedes:** Decision 006 and the demo portion of Decision 011.

**Context:** Supervisor review identified that manually uploading files into Copilot
Studio is static and does not scale ("if you had a thousand files, adding them manually
beats the purpose").

**Options considered:**

| Option | Effort | Outcome | Verdict |
|--------|--------|---------|---------|
| A. SharePoint connector as knowledge source | ~20 min | Automatic, permission-aware ingestion; keeps Teams deployment, escalation tool and all governance controls intact | **Chosen** |
| B. Provision a managed (Dataverse) environment and rebuild the agent to use Foundry IQ | 1–2 hrs | Full target architecture | Deferred to Project 2 |
| C. Leave Copilot Studio static and present the Foundry agent separately | 0 | Does not fix the Teams agent | Rejected as the sole fix |

**Decision:** Connect a SharePoint document library ("Contract governance") as the
agent's knowledge source, and remove all manually uploaded PDFs from Copilot Studio.

**Reasons:**
1. **Dynamic by design.** The SharePoint connector queries the library at question time
   rather than holding a copy inside the agent, so newly added documents become
   answerable without re-uploading or republishing the agent.
2. **Permission-aware retrieval.** The connector authenticates as the signed-in user's
   Entra ID, so users only retrieve content they are authorised to see. This delivers
   the "permission-aware access" objective from the original proposal, which the
   prototype had previously deferred as production hardening.
3. **No rebuild risk.** Keeps the published Teams channel, the recipient-locked
   "Notify content owner" email tool, the feedback loop, and Decisions 007–010 intact.
4. **Aligns with the proposal.** The proposal named SharePoint or Blob Storage as the
   source repository; this implements the SharePoint path.

**Known limits (documented, accepted):**
- File size for SharePoint search grounding is limited (approximately 7 MB per file
  without Tenant Graph Grounding with Semantic Search). Governance PDFs are far below
  this, so it is not a constraint today.
- Newly added documents depend on SharePoint's own indexing and are therefore automatic
  but not instantaneous.

**Retained assets:** The Azure AI Search index `idx-contract-governance` and the Foundry
IQ knowledge base `kb-contract-governance` remain in place, with the Blob indexer on an
hourly incremental schedule (`PT1H`). These were validated independently and are the
intended retrieval layer for Project 2 in a managed environment.

**Evidence:** Agent in Microsoft 365 Copilot answers across all six governance documents,
citing `Contract Renewal and Termination Policy.pdf` — a document that was never
manually uploaded to the agent.

**Status:** Applied and validated.
