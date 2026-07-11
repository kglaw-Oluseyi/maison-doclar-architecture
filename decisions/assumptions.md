---
artifact_id: GOV-004
version: living
status: Ratified
effective_date: 2026-07-11
decision_refs: [DI-026]
---
# Assumption Registry
Many reversals happen because assumptions changed, not because decisions were unsound. Decisions reference assumptions by ID; assumption lifecycle (Active / Validated / Expired / Invalidated) is tracked here and analysed in /decisions/derived.

| ID | Assumption | Referenced by | Status |
|---|---|---|---|
| A-001 | The realistic operating team for the next 18 months is one principal + Cursor + a small future team. | DI-012 (topology) | Active |
| A-002 | Signature clients will be served by the Aperture lineage indefinitely. | DI-002 | Active |
| A-003 | Event-day venues frequently have degraded/unreliable internet. | DI-012, Event-Day Runtime design | Validated (Hub evidence) |
| A-004 | Graph traversal in Postgres will suffice for Knowledge Store scale for ≥2 years. | DI-012 | Active |
| A-005 | Anthropic remains the primary AI provider across the estate. | DI-006 | Active |
| A-006 | Multi-tenancy will eventually be required (year-5 SaaS option). | Deliverable 02 §14 | Active |
| A-007 | The Alakija event (23 Jul 2026) proceeds on the current Signature stack. | DI-018 | Active |
