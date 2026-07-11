---
artifact_id: ADVISORY-CONTRACT
version: 1.1
status: Ratified
effective_date: 2026-07-11
supersedes: none
depends_on: [MPS, MKC]
decision_refs: [DI-010, DI-015, DI-019]
evidence_refs: [architecture/deliverables/05-platform-and-company-architecture.md]
---
# Advisory Contract v1.0 (ratified)
The only interface between the Execution Plane and the Intelligence Plane. **Execution owns operational truth; Intelligence owns advice.** Execution never queries the Knowledge Store; it requests advice with event-scoped operational context only.

## Envelope
```
Recommendation {
    recommendation
    confidence
    reasoning
    provenance
    mpsVersion
    mkcVersion
    intelligenceVersion
    generatedAt
    expiresAt
}
```
Execution **accepts, modifies, or rejects** — and always records why. That record is a Decision Intelligence fact and flows one way into the Knowledge Store.

## Rules
1. Advice degrades to absence, never to failure: no event-day surface may depend on Intelligence availability.
2. Knowledge-plane identifiers never appear in Execution records; advice attaches to event-scoped records with provenance.
3. **Signature access (ratified):** Maison Signature may submit advice requests under this same contract. Signature never writes to and never reads the Knowledge Store; its accepted/rejected advice flows one way into Decision Intelligence. Knowledge admission from Signature operations occurs only through deliberate human review.
4. Every recommendation pins MPS, MKC, and Intelligence versions.
5. **Self-contained requests (DI-019):** every advisory request carries its full context — event context, version pins, request ID, execution timestamp. The advisory service never performs discovery against the requesting system (Signature or Platform Execution). Coupling through future feature growth is prevented by construction.
