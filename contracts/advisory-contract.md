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
