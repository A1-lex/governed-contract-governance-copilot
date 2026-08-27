Mission:

Build a governed enterprise knowledge foundation
that can later power contract review,
risk identification,
workflow automation,
and future enterprise AI solutions.

Every technical decision must increase
trust,
governance,
reusability,
or delivery speed.


Decision 001: Start with one knowledge domain
- Reason: Reduces scope, improves answer quality, and aligns with MVP guidance.
- Trade-off: Does not yet prove enterprise-wide knowledge retrieval.
- Future impact: The same ingestion and metadata pattern can be expanded later.

Decision 002: Use curated SharePoint/Blob documents instead of all enterprise content
- Reason: Improves trust, avoids outdated content, and reduces permission complexity.
- Trade-off: Smaller knowledge base.
- Future impact: Enables controlled expansion after validation.

Decision 003: Require citations in every answer
- Reason: Builds user trust and supports auditability.
- Trade-off: Some answers may be shorter or say “I don’t know”.
- Future impact: Same citation behaviour is required for contract review.

Decision 004: Add one escalation action only
- Reason: Demonstrates action capability without over-automation.
- Trade-off: Limited workflow automation in prototype.
- Future impact: Pattern can be reused for contract exception routing.

NOT building:

- Multi-agent systems
- Autonomous decision making
- Enterprise-wide indexing
- Broad HR support
- ERP integrations
- CRM integrations
- Voice interfaces
- Contract extraction
- Clause comparison
- Contract risk scoring
Reason:
Every item above increases complexity while adding little value
to the primary objective:

'Prove governed enterprise retrieval with citations.'
