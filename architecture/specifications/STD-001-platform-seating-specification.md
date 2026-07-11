---
artifact_id: STD-001
version: "1.0"
status: Ratified — BLOCKER-S1 cleared and CTR-S1/S2 ratified (DI-034)
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

## 5. Canonical formulation & algorithm (normative by reference — STD-003/004/005)
STD-003 (Mathematical Specification, incl. Problem Definition), STD-004 (Algorithm Design — optimization neighbourhoods, simulated annealing), and STD-005 (Validation & Benchmark) are normative for the Platform engine. **CTR-S1 ratified:** the validated Aperture allocator is the initial constructor and degraded-mode implementation; the frozen model defines the constitutional target. **CTR-S2 ratified:** ESLA contributes the interaction/proposal workflow, Aperture the validated engine; the Advisory Contract (CON-001) governs suggestion production; the frozen model governs correctness. Lineage: Salle → ESLA → Aperture → Frozen Mathematical Specification → Platform.

## 6. Acceptance corpus & edge cases
Aperture's 3,184-line seating test corpus is adopted as the seed acceptance corpus (migrated as behavioural specifications, not code). Edge cases carried from evidence: unseated guests as first-class suggestion state; override with reason; host-allocated-unconfirmed guests in remainder pass; kids-zone eligibility; accessibility-compatible scarcity; anchor guests (host proximity) with rank ties.

## 7. Migration notes
Signature keeps its engine untouched (managed divergence). ESLA becomes Read-only after this spec's ratification per GOV-002 (named instruction still required). The Platform seating package implements this spec only after: BLOCKER-S1 cleared, CTR-S1/S2 ruled, MPS v1.0 published (I-10 dependency).

## 8. Capability dependency matrix (DI-037 artifact)
| Depends on | Consumed through | Provides to |
|---|---|---|
| STD-003/004/005 Frozen specs | normative reference | engine correctness |
| STD-002 Guest Domain | audience snapshots, relationship declarations, import-lock signal | — |
| MPS | versioned package (tiers, protocol) | — |
| CON-001 | suggestion-production contract (CTR-S2) | operator/host surfaces |
| C17 Identity | kernel client | attributable review actions |
| Kernel workers | DI-027 harness | allocator runs |
| Provides to | — | STD-007 (published allocations), C10 Reporting (explainable placements), C24 (accept/reject decisions) |

## 9. Objective interface (ARCH-002 lens)
The capability's objective form: *continually optimise seating as event knowledge changes, preserving protocol and explaining every recommendation.* Composes: allocator runs, advisory priors, review cycles, action stack — under existing gates (host approval, CON-001, MPS pinning). No new primitives.
