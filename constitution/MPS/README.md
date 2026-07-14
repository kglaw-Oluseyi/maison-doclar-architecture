---
artifact_id: MPS
version: "1.0"
status: Ratified
effective_date: 2026-07-13
supersedes: "0.1 (assembly charter)"
depends_on: [MKC]
decision_refs: [DI-011, DI-075, DI-076, DI-085]
evidence_refs: [knowledge/preservation-matrix.md#A8, maison-doclar-knowledge-extraction/extracted/mps/protocol-doctrine-research.md, maison-doclar-knowledge-extraction/extracted/mps/mps-corpus-normalized.md]
---
# Maison Protocol System (MPS) — v1.0

MPS is Maison Doclar's primary intellectual property: the encoded understanding of Nigerian and diaspora high-protocol event culture. It is versioned like source code; every event, recommendation, and protocol decision records the MPS version under which it was made. Lessons never edit history — they produce the next version.

## Ratification disposition (DI-085)
v1.0's originally-stated bar (per the extraction repo's `review/MPS-v1.0-draft.md`) required, before ratification: resolution of the §6 attendance-semantics contradiction, and GAP-01 (title precedence) either resolved or explicitly marked "unencoded — Judgement-class." Both are met, the second exceeded:

- **RSVP / attendance-semantics contradiction — resolved.** The five-part decomposition (response state, attendance mode, allocation state, engagement state, attendance record) is promoted doctrine: DI-075 (the governing declaration/disposition/truth principle), DI-076 (the model itself), realized in `STD-002` §G-4 and implemented in `maison-platform`'s guest domain.
- **GAP-01, title precedence — resolved, not merely marked unencoded.** Founder-endorsed via Directive-adopted disposition (DI-077), synthesized from research and confirmed by direct founder review rather than elicited from scratch. Promoted below as §1.

**Ahead of the original v1.0 scope** (originally deferred to v1.1), also Directive-adopted (DI-077) and promoted here because corroborating implementation already exists (`maison-platform` Slice 17, the Entourage domain, migration `0008_entourage_domain.sql`):
- **GAP-02, entourage/ADC/security-detail conventions — resolved.** Promoted below as §2.
- **GAP-03, invitation authority (who may invite on whose behalf) — resolved.** Promoted below as §3.
- **GAP-05, decline and representation for senior dignitaries — resolved.** Promoted below as §4, extending the RSVP response-state model.

**Genuinely still open, honestly recorded rather than papered over:**
- **GAP-04, seating-politics rules beyond allocator mechanics — not resolved.** Never in v1.0's scope; deferred to the Seating Reconciliation Audit (unscheduled). No doctrine exists for this yet in any source, staged or promoted.

## 1 · Title precedence (resolves GAP-01)
Precedence among titled guests is context-dependent Judgement-class knowledge, not a fixed total order — no source corpus (Relay, Nucleus, Aperture, MACS) contains ordering rules, and none should be invented. MPS encodes the **vocabulary** of titles (constitution-wide reference, `titleRef`) and the **method**: precedence disputes are resolved as Decision Intelligence judgement examples, captured founder-side, and promoted to declarative rules only where the founder states a durable rule (e.g., "a reigning monarch always precedes a chieftaincy title, regardless of seniority of the chieftaincy"). Full synthesis: `maison-doclar-knowledge-extraction/extracted/mps/protocol-doctrine-research.md` §1.

## 2 · Entourage, ADC, and security-detail conventions (resolves GAP-02)
Dignitaries above a founder-set tier travel with a retinue — aides-de-camp, security details, personal staff — who require seating, access, and communication handling distinct from both the principal and ordinary guests. Realized as the Entourage domain (`maison-platform`, Slice 17): retinue members are linked to a principal, carry their own invitation/representation state, and are excluded from principal-guest side-channels by construction. Full synthesis: extraction repo §3.

## 3 · Invitation authority (resolves GAP-03)
Who may extend an invitation on whose behalf — a principal, a designated household representative, a corporate office — is a declared authority relationship, not assumed from context. Full synthesis: extraction repo §4.

## 4 · Decline and representation for senior dignitaries (resolves GAP-05)
Extends the ratified RSVP response-state model (§DI-076): a senior dignitary's decline may be accompanied by a designated representative attending in their stead, modeled as a distinct representation record linked to, not overwriting, the original decline. Full synthesis: extraction repo §5.

## Open items carried into v1.1
GAP-04 (seating-politics rules beyond allocator mechanics) remains unresolved and unscoped — the honest state of the art as of this ratification.
