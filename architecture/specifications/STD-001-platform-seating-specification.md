---
artifact_id: STD-001
version: "1.0-draft"
status: Draft — blocked on BLOCKER-S1 (frozen formulation promotion) and CTR-S1/S2 rulings
effective_date: 2026-07-11
depends_on: [MPS-001, MKC-001, CON-001, CAP-001]
decision_refs: [DI-030]
evidence_refs: [maison-doclar-knowledge-extraction/extracted/seating/fork-divergence-report.md]
---
# Platform Seating Specification v1 (STD-001)
Derived, not designed: every element below traces to the divergence report, the fossils register (KNW-003), or the frozen formulation (by reference). No implementation is authorized by this document.

## 1. Invariants (non-negotiable; sources cited)
I-1 Event-scoped everything; no cross-event reads (fossil: event-scope verify script). I-2 Every placement suggestion carries a machine-stored, host-readable **reason**; unexplainable placements are invalid (both forks' `reason` field; constitutional explainability). I-3 Runs are **reproducible**: config captured at run time; restore and unallocate are first-class (Aperture run lifecycle). I-4 Imports and allocation never race (fossil: `guest_list_import_lock`). I-5 RSVP never gates downstream systems; it informs **eligibility** through audience modes only. I-6 Locked seats/tables are inviolable when respect is enabled; accessibility is a hard constraint when enforced and a proximity bonus always. I-7 Separate-relationships are hard constraints, never soft penalties. I-8 All mutating actions enter the action stack (undo/redo with before/after state). I-9 The engine **advises**; the surface (operator/host) decides — suggestions flow through the accepted-contract shape (CTR-S2 resolution, per CON-001 philosophy). I-10 Every run pins MPS version (tier/protocol semantics in force).

## 2. Rule taxonomy (accepted from Aperture, Validated)
Hard constraints: capacity · locked · accessibility(enforced) · separate-relationships · zone/segment eligibility. Soft rules, **ordered priority translated to weights** (the priority-without-numbers principle is preserved as UX/knowledge design): household_togetherness → relationship_strength → vip_proximity (VIP-zone ×2, VIP-neighbour, entrance-proximity ×0.5 shape preserved as reference behaviour) → tag_cluster → host_relationship_proximity (anchor + closeness rank). Additional accepted behaviours: accessibility-proximity bonus; row-preference bands; household seed placement.

## 3. Accepted from ESLA/Salle (Observed → to be Validated on Platform)
Speculative assignments with versioning (stage-before-commit) · canvas design physics (snap, safe zone, rotation, z-index, heatmap, relationship lines, kids zones) · structured validation (code/severity/entity) · capability-grained authorization · the conversational proposal loop (design-as-dialogue) as the host/AI review channel · the imported-suggestion contract shape as the producer/consumer boundary.

## 4. Rejected / not carried forward
Two divergent suggestion keys (importKey vs internal id) — Platform uses one event-scoped guest identity per C2. In-process-only allocation (Aperture) — replaced by the producer contract (I-9). Numeric-weight UI exposure — priority ordering remains the interface.

## 5. Canonical formulation & algorithm (normative by reference — BLOCKED)
The frozen Mathematical Formulation, Algorithm Design Specification (eleven optimization neighbourhoods, simulated-annealing strategy), and Validation & Benchmark Specification are normative for the Platform engine. **They are not yet in the constitutional repository (BLOCKER-S1).** Proposed CTR-S1 resolution for ratification: the frozen formulation is the target; the Validated greedy pipeline is retained as the initial-solution constructor and as the degraded-mode engine (advice degrades to a simpler engine before degrading to absence).

## 6. Acceptance corpus & edge cases
Aperture's 3,184-line seating test corpus is adopted as the seed acceptance corpus (migrated as behavioural specifications, not code). Edge cases carried from evidence: unseated guests as first-class suggestion state; override with reason; host-allocated-unconfirmed guests in remainder pass; kids-zone eligibility; accessibility-compatible scarcity; anchor guests (host proximity) with rank ties.

## 7. Migration notes
Signature keeps its engine untouched (managed divergence). ESLA becomes Read-only after this spec's ratification per GOV-002 (named instruction still required). The Platform seating package implements this spec only after: BLOCKER-S1 cleared, CTR-S1/S2 ruled, MPS v1.0 published (I-10 dependency).
