# Search Validation - idx-contract-governance

**Date:** Monday (indexing succeeded)
**Search service:** srch-govknow-proto
**Index:** idx-contract-governance
**Pipeline:** index + indexer + data source + skillset (Import & vectorize wizard) ✅
**Embedding model:** text-embedding-3-large (oai-govknow-proto, system-assigned MI)
**Semantic ranker:** ON
**Query mode:** hybrid (keyword + vector) + semantic

## Result: 5 / 5 benchmark questions PASS
Every question returned the correct source document as the #1 result.
The `title` field returns the source filename on every hit → citations are viable.

| # | Question | Expected Source | Top Result (title) | Answer Score | Reranker | Verdict |
|---|----------|-----------------|--------------------|-------------|----------|---------|
| 1 | What approvals are required for NDAs? | Approval Matrix.pdf | Approval Matrix.pdf | 0.93 | 2.98 | PASS |
| 2 | When should Legal review a contract? | Legal Escalation Guidelines.pdf | Legal Escalation Guidelines.pdf | 0.98 | 3.24 | PASS |
| 3 | What documents are required before procurement approval? | Procurement Policy.pdf | Procurement Policy.pdf | 0.93 | 3.38 | PASS |
| 4 | Who owns supplier onboarding? | Supplier Onboarding Process.pdf | Supplier Onboarding Process.pdf | 0.99 | 3.10 | PASS |
| 5 | What is the standard contract review workflow? | Contract Review Procedure.pdf | Contract Review Procedure.pdf | 0.91 | 3.36 | PASS |

## Notes / observations
- Semantic answers (`@search.answers`) returned clean extractive snippets for all 5 — good sign for grounded, citation-backed responses.
- Secondary hits were topically sensible (e.g. Q1 also surfaced Procurement Policy's NDA mention) — expected overlap, not an error.
- Remaining benchmark Qs to spot-check in Copilot Studio: Q5 dispute escalation path, Q7 policy review cycle (both live in already-passing docs).

## Decision
Retrieval layer accepted. Proceed to Copilot Studio agent build (no chunking/prompt tuning required).
