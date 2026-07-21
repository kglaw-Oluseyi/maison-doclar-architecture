---
artifact_id: STD-014
name: Decision Simulation Engine (Executive Scenario Intelligence)
version: "1.0"
status: Ratified (founder sign-off, 21 July 2026) — new capability, formal charter per §0
classification: Executive Intelligence Capability — Intelligence Plane
depends_on: [STD-001/003/004 (Seating), STD-002 (Guest), STD-008/CON-001, STD-013 (Guest Knowledge Graph & Host Intent), MKC-001, C25 Mission Planning]
decision_refs: [DI-009 (two-plane architecture), DI-051 (C25 charter and its "plug into the Runtime, never invent parallel planning logic" rule), DI-126 (this specification's ratification)]
evidence_refs: [externally reviewed draft; independently checked against current implementation state before revision]
---

# STD-014 — Decision Simulation Engine

---

## 0. Chartering Status

This proposes a **new top-level capability**. Per this programme's precedent (DI-051, which chartered C25 Mission Planning), a new capability charter requires explicit founder ratification with its preconditions stated.

**Preconditions before this can be built (not yet all met):**
1. STD-013 (Guest Knowledge Graph) ratified — met, 21 July 2026 — and at least partially built — **not yet met**.
2. Seating's own performance-hardening arc (DI-121/122) reaching a state where a full optimizer re-run is fast enough for the interactive experience this spec promises — **not yet true**. Confirmed as of this ratification: after four independently-verified fixes (Extensions 1/1b/2, Changes A/B/C), Seating's anneal phase remains far off STD-004 §9's target at every tested scale (200 guests: measured at ~41–77s full-budget wall time against a <1s target). Seating has been deliberately left unfinished at the founder's direction — this is not an oversight, and this precondition is explicitly unmet.
3. Founder ratification of the capability charter itself — met, this entry.

---

## 1. Problem Statement

A seating allocator answers *"what is the best arrangement?"* An executive planner asks *"what happens if I move the Governor? What if I prioritize networking over protocol?"* Today, answering that requires manually making the change, rerunning the algorithm, and mentally comparing outputs. This capability exists to answer *"what happens if..."* before a host commits.

---

## 2. Design Philosophy

A deterministic simulation environment, not an optimizer, not an autonomous agent, not a recommendation engine that decides. It evaluates hypotheticals against the real optimizer, in a sandbox, and reports the difference. It never answers *"should I do this,"* only *"if you do this, here's what changes."* Decision authority stays with the host, always.

---

## 3. What "Clone Snapshot" Actually Means

Seating, Guest, Communications, Event-Day, and Access all currently run as pure in-memory domain modules with no Postgres/Neon persistence layer. "Cloning a snapshot" means constructing a new `SeatingProblem`/`Arrangement` pair from the current one, with the scenario's hypothetical changes applied — the same cheap, structural cloning discipline every domain module in this system already uses. No new snapshot infrastructure needs to be built.

---

## 4. Performance — Phased Fidelity, Stated Honestly

| Operation | Phase 1 (incremental fidelity) | Phase 2 (full re-optimize fidelity) |
|---|---|---|
| Scenario creation | <200ms | <200ms |
| Scenario compilation | <500ms | <500ms |
| Optimization execution | Reuses Seating's incremental delta cost (sub-millisecond per evaluated change) | **Not yet specifiable** — depends on Seating's remaining performance work, which is currently paused, unfinished |
| Impact comparison | <250ms | <250ms |
| Scenario deletion | Immediate | Immediate |

**Phase 1** (buildable once STD-013 lands, independent of Seating's remaining performance work): local, incremental scenario types only — single-guest moves, single-swap, small household relocations — via Seating's already-verified incremental delta machinery, not a full anneal re-run.

**Phase 2** (gated on Seating's performance arc resuming and meeting STD-004 §9's actual targets): full-scenario simulation requiring a genuine full anneal run. Cannot honestly promise interactive latency until that work resumes and completes — which it has not, per §0.

---

## 5. Relationship to C25 Mission Planning

Per DI-051's explicit charter language — *"every future capability plugs into the Runtime rather than inventing its own planning logic"* — Decision Simulation Engine does not compete with or duplicate C25. It produces evidence for C25's existing `Observation` primitive when a host-facing decision is in play. A completed simulation's Executive Impact Report is exposed to Mission Planning as an Observation, never a second, parallel decision-authority object.

---

## 6. Data Governance on Retained Scenarios

The sandbox arrangement itself is always discarded immediately after the Executive Impact Report is generated. The Executive Impact Report may be retained if archived, subject to the same MKC governance and event-scoping rules as every other guest-level record — not a bespoke retention policy. `DISCARDED` scenarios are removed immediately and completely, including their reports.

---

## 7. Scenario Model

```
Scenario {
  scenarioId, eventId, createdBy, createdAt, description
  parentScenario
  baselineRevision                // reference to the SeatingProblem/Arrangement state, not a DB snapshot ID
  changes: ScenarioChange[]       // expressed in STD-002/STD-013's existing vocabulary
  fidelity: "INCREMENTAL" | "FULL_REOPTIMIZE"
  status: "ACTIVE" | "ARCHIVED" | "DISCARDED"
  report: ExecutiveImpactReport | null   // retained only if ARCHIVED, governed per §6
}
```

## 8. Scenario Vocabulary

Every scenario change type (move guest, swap guests, add table, relax alumni clustering, etc.) is expressed as a hypothetical value in Seating's existing `SeatingProblem`/`Arrangement` input shape and Guest's existing `GuestRelationship`/household schema — never a new, parallel vocabulary.

## 9. Simulation Pipeline

```
Current in-memory SeatingProblem + Arrangement (constructed fresh per request)
   │ Clone (cheap, structural)
   │ Apply scenario changes (existing STD-002/003/004 vocabulary)
   │ Evaluate: Phase 1 → incremental delta machinery; Phase 2 → full construct + anneal re-run
   │ Compare against baseline
   │ Generate Executive Impact Report (delta-first)
   │ Discard sandbox arrangement (always) → retain report only if archived
```

## 10. Executive Impact Report, Delta Engine, Explainability, Comparison, Recommendation, Conversation, Trade-offs

Adopted as originally drafted: reports differences, not full replacement plans; every consequence traceable through a chain; multiple scenarios compared side-by-side; recommendations advisory only, never automatic; conversational exploration is a UI pattern over this same pipeline; trade-offs surfaced explicitly, never optimized silently.

**AI-assisted scenario construction** must route through the same evidence-tier and confidence-calibration discipline STD-013 establishes — one AI-governance discipline across both capabilities, not two.

---

## Architectural Invariants

DS-1 — Simulation is always read-only.
DS-2 — No simulation may mutate operational truth.
DS-3 — Every scenario is isolated and reproducible.
DS-4 — Every recommendation is explainable.
DS-5 — AI never performs optimization.
DS-6 — Decision authority always remains with the human.
DS-7 — Simulation reports differences, not complete replacement plans.
DS-8 — Every scenario is attributable, auditable, and versioned.
DS-9 — The Decision Simulation Engine owns scenario modelling only; it never reimplements or forks the Seating Optimization Engine.
DS-10 — The Simulation Engine is a consumer of optimization capabilities, never their owner.
DS-11 — Scenario changes are expressed exclusively in Guest's and Seating's existing data vocabulary; this capability never invents a parallel constraint or relationship model.
DS-12 — Archived Executive Impact Reports are governed by the same MKC/event-scoping rules as the guest data they describe.
DS-13 — A completed simulation is exposed to Mission Planning (C25) as an Observation, never an independent, parallel decision-authority object.
DS-14 — Simulation fidelity is stated honestly per request; a Phase 1 build never implies full-reoptimization-grade confidence it hasn't earned.

---

**Status:** Ratified. Build sequencing depends on STD-013 landing and, for full Phase 2 fidelity, Seating's paused performance arc resuming and being independently verified against STD-004 §9's actual targets.
