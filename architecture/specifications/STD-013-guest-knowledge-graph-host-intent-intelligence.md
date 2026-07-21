---
artifact_id: STD-013
name: Guest Knowledge Graph & Host Intent Intelligence Specification
version: "1.1"
status: Ratified (founder sign-off, 21 July 2026)
effective_date: 2026-07-21
realizes: C2 (Guest Intelligence — Intelligence Plane half)
depends_on: [STD-002, STD-008, CON-001, MKC-001, MPS-001, STD-001/003/004 (Seating)]
provides_to: [STD-014 (Decision Simulation Engine) — as an input dependency]
decision_refs: [DI-009 (two-plane architecture), DI-033 (STD-002's explicit deferral of this capability to "Phase 4"), DI-125 (this specification's ratification)]
evidence_refs: [founder-supplied research documents on host-intent inference and constraint extraction; two rounds of external architecture review; real operational incident — Aperture allocator, ~500-guest event, split a couple and produced a rigid, unsatisfactory result requiring full manual rework]
---

# STD-013 — Guest Knowledge Graph & Host Intent Intelligence

**This is an Intelligence Plane specification.** It does not modify STD-002 (Guest) or STD-001/003/004 (Seating). Both remain exactly as built. This specification defines a new capability that sits *between* raw human input (RSVP text, host conversations, planner notes) and Guest's existing relationship data — making the step of *filling that data in correctly* reliable, evidence-based, and auditable, instead of manual and error-prone.

---

## 0. Position in the Architecture — Read This First

```
Host conversations, RSVP replies, planner notes, imported lists
                         │
                         ▼
        ┌────────────────────────────────────┐
        │   STD-013 — Guest Knowledge Graph    │   ← THIS SPEC
        │   (Intelligence Plane, C2 realized)  │      Infers, proposes, explains.
        │                                      │      Never decides. Never writes
        │  - Inference pipeline (§5)           │      execution truth directly.
        │  - Relationship Graph (§4.1)         │
        │  - Intent Graph (§4.2, separate       │
        │    object, separate lifecycle)        │
        │  - Host Intent Profile (§4.3)        │
        │  - Event Type Ontology (§7)          │
        └────────────────────────────────────┘
                         │
                AdvisoryEnvelope (STD-008/CON-001)
                         │
                         ▼
        ┌────────────────────────────────────┐
        │   Tiered human confirmation (§6)     │   ← Mandatory gate. No exceptions.
        │   Tier A: blocking · Tier B: passive │      Nothing bypasses this at any
        │   Tier C: never operational          │      confidence level.
        └────────────────────────────────────┘
                         │
                writes via STD-002's own path
                         │
                         ▼
        ┌────────────────────────────────────┐
        │   STD-002 — Guest (Execution Plane)  │   ← UNCHANGED. Already built.
        │   GuestRelationship, household, etc. │      Owns operational truth.
        └────────────────────────────────────┘
                         │
                read exactly as today
                         │
                         ▼
        ┌────────────────────────────────────┐
        │  STD-001/003/004 — Seating (Solver)  │   ← UNCHANGED. Already built,
        │  H1–H10 hard/soft constraints        │      already hardened.
        └────────────────────────────────────┘
```

**The load-bearing design decision:** this capability never writes directly into Guest's operational relationship data, and Seating never talks to it directly. Every inference is advisory (STD-008's AdvisoryEnvelope), every advisory requires a human confirmation appropriate to its tier before it becomes real, and confirmed data enters Guest through Guest's own existing write path — the same one a planner typing into a form uses today. **Seating requires zero changes.**

This is a direct rejection of one failure mode named in the founder's source material: an AI system that infers a seating decision and asserts it as truth. Nothing here ever does that. AI infers meaning; a human confirms it; only then does it become something Seating can rely on.

**Downstream relationship:** STD-014 (Decision Simulation Engine, ratified) depends on this specification's data model for scenario input vocabulary. That dependency runs one way — STD-014 consumes STD-013's structures; nothing in this document depends on Simulation.

---

## 1. Problem Statement

A ~500-guest event run through the legacy Aperture allocator produced a result the host found unusable: a couple was seated apart, and household movement was rigid (effectively all-or-nothing), forcing the host to redo the seating manually.

**Diagnosis, per STD-005's own failure-analysis playbook (§14, already ratified):** most complaints of this shape are *data* problems, not *algorithm* problems — the constraint was never correctly captured as structured data before the solver ran. STD-003/004 (the new Seating engine, already built and hardened) already close the two specific mechanical gaps this incident exposed: H7/H6 hard constraints prevent silent splitting of correctly-encoded couples/MUST groups (independently verified in code, DI-103), and household togetherness above that floor is a soft, chunk-flexible preference, not rigid all-or-nothing behavior.

**What remains unsolved is the capture step itself.** Nothing in the platform today turns "I'm coming with my wife and two kids," a five-minute call with the host, or a planner's handwritten note into a correctly-filled `GuestRelationship` record. That gap is what this specification closes — and it is exactly the gap STD-002 named and deliberately deferred at its own drafting (DI-033): *"Guest Knowledge Graph explicitly out of scope — it is Intelligence Plane material (Phase 4)."*

**The standard this must meet, per the founder's directive:** every seated guest, at every scale up to 2,000, should reflect a thorough, thoughtful exercise — not a blind spot waiting to surface at the event.

---

## 2. Scope

**In scope (Phase 1, this authorization):**
- A rule-first, progressively-uncertain inference pipeline (§5) extracting structured, evidence-tiered relationship and intent signals from RSVP text, conversational input, and imports
- Two distinct graph objects with distinct lifecycles: the Relationship Graph (§4.1, persists within the event, factual) and the Intent Graph (§4.2, expires with the event, strategic)
- A Host Intent Profile (§4.3) capturing per-event priorities, elicited via the tiered question set (§6.4)
- An Event Type Ontology (§7) — broad classification from day one, narrow specialized intelligence only where evidence justifies it
- Tiered human confirmation (§6) — not uniform blocking confirmation on every inference
- Explainability via reasoning trace (§5.4) and calibrated confidence (§5.5), both as first-class, distinct properties
- Continuous re-inference on new input, always proposing, never silently overwriting confirmed facts

**Explicitly out of scope (Phase 1):**
- Any direct write from this capability into STD-002's `GuestRelationship` or any Seating input — the confirmation gate has no bypass at any confidence level or tier
- Fully specialized intelligence modules for every Event Type Ontology entry — only the four immediate ones (§7.3) get real specialized handling in Phase 1; the rest are classification labels with a shared default profile
- Cross-event learning beyond MKC's segment-aggregate rule (P-5) — no individually-linked cross-event inference
- Automatic re-optimization triggering — proposing updated constraints is in scope; automatically re-running Seating is a Seating/Event-Day scheduling decision, out of this spec's authority
- New "social situation" grouping mechanisms (bride's family, press table, delegation) — the data model is kept extensible for this (§4.1, edge-type note), but nothing new is built for it now
- Simulation/scenario capability of any kind — that is STD-014, a separate, ratified specification that consumes this one's outputs, not built here
- Any P-4-prohibited data class, under any framing — see §5.3

---

## 3. Invariants

**GI-1 — Advisory only, absolutely.** No component of this capability ever writes to STD-002's `GuestRelationship`, household data, or any Seating input directly. Every output is an `AdvisoryEnvelope` (STD-008). This is a §1-class hard invariant, not a soft preference, revisited by nothing that follows.

**GI-2 — Confirmation is tiered, not uniform, but never absent.** Every inferred fact that becomes real Guest data does so through a human act appropriate to its tier (§6): blocking confirmation for Tier A, visible-and-reversible passive review for Tier B, and Tier C never becomes operational truth at all. "The host didn't object" is not, by itself, confirmation — Tier B's passive-review window and explicit reversibility are what make silence a legitimate signal there; outside that designed mechanism, silence is never treated as consent.

**GI-3 — Confidence is calibrated, not decorative, and is distinct from evidence tier.** A stated confidence of 90% must be empirically checked against outcomes over time. Confidence (§5.5, a continuous 0–1 estimate) and evidence tier (§5.6, a discrete verified/observed/inferred/speculative classification) are separate axes — a fact can be highly confident but still merely inferred, and that distinction must remain visible downstream, not collapsed into one number.

**GI-4 — Conflict/SEPARATE-class data enters only via host or operator, never free inference from arbitrary guest text.** This is STD-008 P-4, restated here because it is the single most consequential guardrail for this specific capability. A guest's own RSVP reply expressing a preference about another guest is treated as host/operator-channel evidence only when collected through a channel the host explicitly designated for this purpose (§5.7) — never silently scraped from open-ended communications.

**GI-5 — Every inference is explainable via a reasoning trace, not just a confidence score.** Per STD-008 G-4, extended: every advisory carries not only a host-readable reason but the actual chain from evidence to conclusion (§5.4) — this is what lets a host productively dispute a recommendation instead of just accepting or rejecting it blind.

**GI-6 — Every prohibited data class in MKC P-4 is a hard rejection at the extraction boundary**, not a downstream filter. Health beyond self-declared accessibility/dietary, income/wealth signals, **political affiliation**, non-consented photographs, precise home addresses, biometric data, and anything about minors beyond count/dietary/accessibility are rejected at the point of extraction. **This applies without exception regardless of Event Type classification** — an event classified as "Political/Public Leadership" (§7.2) is a label describing the *event*, never license to tag *attendees* by political affiliation. See also §7.4.

**GI-7 — Event-scoped by default (DI-019), with an explicit lifecycle split.** The Relationship Graph persists *within* the event it was recorded for. The Intent Graph is inherently event-scoped and expires with the event by design — it describes what a host wants for *this* event, not a durable fact about a guest. Neither crosses into a future event as an individually-linked fact; cross-event reuse exists only through MKC's segment-aggregate mechanism (P-5).

**GI-8 — Stability under re-inference.** A new RSVP, a late edit, or a corrected note triggers a fresh proposal, not a silent overwrite. Previously confirmed, human-approved data is never demoted or replaced without going through its tier's confirmation gate again.

**GI-9 — The inference pipeline is ordered by certainty, and cheaper/more-certain stages run first (§5).** The LLM is the last resort for a given fact, not the first call — this reduces cost, latency, and hallucination exposure, and it is a governing principle, not an optimization detail.

---

## 4. Data Model

### 4.1 Relationship Graph Edge (Intelligence Plane, factual, event-persistent)

Describes *who knows whom* — durable social facts, within the scope of one event (GI-7).

```
GraphEdge {
  edgeId
  eventId                    // GI-7: event-scoped
  fromGuestId, toGuestId
  edgeType                   // SPOUSE | HOUSEHOLD | PARENT_CHILD | FRIEND | BUSINESS |
                              // ALUMNI | MENTOR | INVESTOR_FOUNDER | NEIGHBOUR |
                              // CONFLICT | FORMER_PARTNER | COMPETITOR | UNKNOWN
                              // Extensible list, not exhaustive; new types require a
                              // decision-register entry, not silent addition. The model
                              // is deliberately kept extensible enough that future,
                              // temporary event-specific groupings (e.g. "bride's family,"
                              // "press table," "government delegation") could be
                              // represented without abusing these edge semantics — but
                              // no such mechanism is built in this revision.
  strength                    // continuous, not just present/absent
  confidence                  // 0–1, calibrated (GI-3, §5.5)
  evidenceTier                 // VERIFIED | OBSERVED | INFERRED | SPECULATIVE (GI-3, §5.6)
                              // — a distinct axis from confidence, not a restatement of it
  evidenceSource               // RSVP_TEXT | HOST_CONVERSATION | PLANNER_NOTE |
                              // IMPORTED_LIST | GUEST_SELF_REPORT (via designated channel, §5.7)
  evidenceText                 // the actual source snippet, retained for audit (MKC retention rules apply)
  reasoningTrace                // ordered chain: evidence → detected pattern → mapped type →
                              // matched guest → proposed edge (GI-5, §5.4)
  extractedAt, expiresAt
  status                       // PROPOSED | CONFIRMED | MODIFIED | REJECTED
  confirmationTier             // TIER_A | TIER_B | TIER_C (§6)
  confirmedBy, confirmedAt      // GI-2
}
```

### 4.2 Intent Graph Entry (Intelligence Plane, strategic, event-scoped and expiring)

**Deliberately a separate object from the Relationship Graph, with a separate lifecycle (GI-7).** Describes *what the host wants to happen* — event strategy, not social fact. "Don't cluster my cousins" and "spread the executives" don't reduce to guest-pair edges; collapsing them into the Relationship Graph would misrepresent both.

```
IntentEntry {
  intentId
  eventId                    // always event-scoped; never persists past the event (GI-7)
  intentType                  // AVOID_CLUSTERING | ENCOURAGE_MIXING | PROTECT_PROMINENCE |
                              // ENCOURAGE_INTRODUCTION | MAINTAIN_DISCRETION | OTHER
                              // (extensible, decision-gated, same discipline as edge types)
  targetScope                  // a guest, a group, or a criterion (e.g., "all cousins")
                              // — never assumed to reduce to explicit pairs
  description                  // host-stated or inferred intent, host-readable
  confidence, evidenceTier, evidenceSource, evidenceText, reasoningTrace   // identical structure to GraphEdge, §4.1
  status, confirmationTier, confirmedBy, confirmedAt                        // identical structure to GraphEdge, §4.1
  expiresAt                    // mandatory; defaults to event end; never null
}
```

### 4.3 Host Intent Profile (per event)

```
HostIntentProfile {
  eventId
  eventTypeId                 // reference into the Event Type Ontology, §7
  priorities: {
    protocolWeight, networkingWeight, familyTogethernessWeight,
    businessMixingWeight, comfortWeight   // each 0–1, elicited not assumed
  }
  elicitedVia                 // reference to the specific question-set version used (§6.4)
  confirmedBy, confirmedAt
}
```

**How this reaches Seating without changing Seating:** STD-004's term weights are already per-run configuration, not hardcoded — a confirmed `HostIntentProfile` is translated into that existing configuration surface by the operator's confirmation action, not injected automatically or silently.

---

## 5. Inference Pipeline

Structured as a `PipelineDeclaration` per STD-008 §2F — registered, sensitivity-ceilinged, purpose-bound, before any fact flows.

### 5.1 Ordered stages (GI-9, formalized)

```
Deterministic Facts
  (same household on the invitation, same surname, shared address,
   shared emergency contact, invitation submitted jointly — no inference needed)
   │
   ▼
Existing Knowledge
  (already-confirmed data from this event or a prior confirmed record within scope)
   │
   ▼
Statistical Inference
  (patterns from structured, non-textual signals — e.g. import-file company/department fields)
   │
   ▼
LLM Semantic Extraction
  (free text — RSVP replies, conversation notes — genuinely ambiguous, last resort)
   │
   ▼
Human Confirmation (§6, tiered)
   │
   ▼
Operational Truth (STD-002, via its own existing write path)
```

**Governing principle: every stage is progressively more uncertain.** A fact resolved deterministically never touches the LLM.

### 5.2 What the LLM does and does not do

The LLM extracts structured, evidence-tiered candidates. It never proposes a seat, a table, or any Seating-facing output directly — that boundary is absolute and identical to STD-014's DS-5 (AI never performs optimization), for consistency across both capabilities.

### 5.3 MKC boundary (GI-6)

P-4 classes are rejected mechanically at extraction, before storage — health beyond self-declared accessibility/dietary, income/wealth, **political affiliation**, non-consented photographs, precise addresses, biometrics, and anything about minors beyond count/dietary/accessibility.

### 5.4 Reasoning Trace (GI-5)

Every `GraphEdge`/`IntentEntry` carries an explicit, ordered chain, not just a confidence number:

```
Evidence: "I'm attending with my wife."
   ↓
Detected: spouse-indicating phrase
   ↓
Mapped to: edgeType = SPOUSE
   ↓
Matched: existing guest record (name/contact correlation)
   ↓
Proposed: SPOUSE edge, confidence 0.92, evidenceTier = INFERRED
```

### 5.5 Confidence (continuous, calibrated)

0–1, checked against actual confirmation/rejection outcomes over time (§9 telemetry requirement). Wide intervals and "prior-only, no observed evidence" are surfaced honestly, never smoothed over, per STD-008's existing rule.

### 5.6 Evidence Tier (discrete, distinct from confidence)

`VERIFIED` (e.g., an imported marriage certificate or authoritative record) → `OBSERVED` (a deterministic-stage match, e.g. shared household on the invitation) → `INFERRED` (LLM-extracted from text, evidence-backed) → `SPECULATIVE` (low-signal, pattern-only, no direct evidence). A fact's tier governs what it's even eligible to become without further evidence.

### 5.7 Designated Guest-Input Channels (GI-4 compliance)

Guest-originated preference/conflict signals only count as host/operator-channel evidence when collected through a channel the host explicitly set up for this purpose — never inferred from open-ended guest messages without that explicit designation.

---

## 6. Confirmation (tiered, per GI-2)

### 6.1 Tiers

- **Tier A — blocking confirmation required.** High-stakes or irreversible-feeling facts: SPOUSE, HOUSEHOLD structure changes, CONFLICT/SEPARATE edges, anything touching accessibility or a sensitive circumstance.
- **Tier B — passive review, visible and reversible.** Lower-stakes facts with a clear evidentiary basis: surfaced for review, auto-applied after a defined window unless the host/operator intervenes, but always visible and always reversible.
- **Tier C — never becomes operational truth in Phase 1.** Speculative, low-confidence, or pattern-only inferences remain visible as candidates, never written into STD-002.

### 6.2 Tier assignment

Determined by edge/intent type (some types are always Tier A — CONFLICT, SEPARATE, accessibility-adjacent facts, regardless of confidence) and evidence tier + confidence for everything else.

### 6.3 Why this exists

At 2,000 guests, requiring blocking confirmation on every inferred edge would recreate the manual-burden problem this capability exists to remove.

### 6.4 Elicitation Question Set (Layer 1/2/3 structure)

**Layer 1 — Mandatory Event Intelligence Capture:**
1. Who must be seated together? 2. Who must not be seated together? 3. Are there family, household, or organizational structures not visible from the guest list? 4. Are there sensitivities, conflicts, bereavements, or circumstances requiring discretion? 5. Which outcomes matter most for this event: protocol correctness, relationship-building, comfort, visibility, or discretion?

**Layer 2 — Intent and Experience Design:**
6. Which relationships should be strengthened through seating? 7. Which guests should be introduced to each other? 8. Who should receive prominence or recognition — and who should not be highlighted? 9. Who requires additional hosting attention? 10. Are there guests whose experience should be intentionally quiet or private?

**Layer 3 — Event-Specific Modules** (only when the Event Type Ontology indicates relevance):
- *Chieftaincy/Investiture:* titles/traditional authorities requiring confirmation; precedence rules; ceremonial roles.
- *Wedding:* family structures spanning both sides; cultural traditions; couple priorities.
- *Corporate:* executive hierarchy; client relationships; strategic introductions.
- *Funeral/Memorial:* deferred to founder input at implementation (O-6).

This set is versioned and extensible via decision, not open-ended prompting.

---

## 7. Event Type Ontology

### 7.1 Principle

**Broad recognition, narrow intelligence.** Classification is cheap and valuable from day one; specialized intelligence is expensive and only built where evidence justifies it.

### 7.2 Phase 1 taxonomy (16 categories)

1. Chieftaincy / Investiture Ceremony · 2. Wedding · 3. Funeral / Memorial · 4. Corporate Dinner · 5. Executive Retreat · 6. Board / Leadership Meeting · 7. Product Launch / Brand Event · 8. Awards Ceremony · 9. Fundraiser / Charity Gala · 10. Political / Public Leadership Event · 11. Religious / Faith-Based Ceremony · 12. Private Celebration · 13. Networking / Relationship-Building Event · 14. Conference / Summit / Forum · 15. Cultural / Traditional Ceremony (other than #1 — kept explicitly distinct to avoid classification ambiguity in the Nigerian context this platform serves) · 16. Other / Unknown

### 7.3 Immediate specialized profiles (Phase 1)

Only four get real, dedicated question modules and protocol-handling logic in Phase 1: **Chieftaincy/Investiture** (the flagship category), **Wedding**, **Funeral/Memorial**, **Corporate Executive Events**. All other categories start as classification labels with a shared, generic default profile — usable, not a placeholder, just not deeply specialized yet.

### 7.4 Mandatory guardrails on two categories (GI-6)

- **"Political/Public Leadership Event" (#10)** is an event-level classification only — never license to collect, infer, or tag any individual guest's political affiliation (MKC P-4, no exception).
- **"Religious/Faith-Based Ceremony" (#11)** may capture religious protocol facts already covered by MPS; may not profile an individual guest's personal religious affiliation (STD-008 CTR-I1 resolution).

### 7.5 Structure

```
EventType {
  eventTypeId
  canonicalName, aliases
  specializedProfile: boolean        // true only for the four in §7.3
  applicableQuestionModules
  protocolRequirements
  seatingObjectiveDefaults           // maps to HostIntentProfile priority defaults
}
```

---

## 8. Surfaces

Proposed as **S13 — Relationship Graph Review**, following the Executive Interaction Hierarchy and Analysis Rule already binding on every EIX surface. Formal addition to EIX-002 requires its own small ratification step, not assumed here.

---

## 9. Scale Requirement (2,000 Guests)

Extraction and graph construction are inherently parallelizable per guest/household and do not share Seating's combinatorial-search bottleneck class. The binding constraint at 2,000 guests is host/operator review throughput, addressed directly by tiering (§6).

**Calibration telemetry (GI-3, operationalized):** track, over time, whether inferences at a stated confidence band are actually confirmed at that rate. New instrumentation, needs a home — likely C24 Decision Intelligence (§11, O-4).

---

## 10. Dependency Matrix

| Depends on | Consumed through | Provides to |
|---|---|---|
| STD-008 / CON-001 | AdvisoryEnvelope, DecisionRecord, PipelineDeclaration | — |
| MPS v1.0 | protocol/relationship vocabulary, title precedence | correctly-typed relationship edges |
| MKC | P-4 boundary enforcement, sensitivity ceiling | Knowledge Store governance |
| STD-002 (read-only reference) | existing `GuestRelationship`/household schema | confirmed edges, written via STD-002's own path |
| C16 AI Governance | producer registration, model routing, budget | telemetry |
| EIX-001 | six-questions / Analysis Rule discipline | host/operator trust |
| **Provides to** | confirmed `GraphEdge` → STD-002 write | STD-001/003/004 Seating (unchanged) |
| **Provides to** | confirmed `HostIntentProfile` → STD-004 run configuration | Seating term-weight configuration |
| **Provides to** | confirmed data model, evidence-tier/pipeline discipline | STD-014 Decision Simulation Engine |
| **Provides to** | `DecisionRecord` stream | C24 Decision Intelligence |

---

## 11. Open Items

- **O-3** — S13 surface's formal EIX-002 ratification.
- **O-4** — Calibration telemetry infrastructure (likely C24, not yet built).
- **O-5** — Designated-channel mechanics (§5.7), precise enumeration against STD-002's actual RSVP schema.
- **O-6** — Funeral/Memorial Layer 3 question module, deferred for direct founder input.
- **O-7** — Specialization-promotion criteria for the 12 non-specialized §7.2 categories.

**Closed this revision:** O-1 (elicitation question set) and O-2 (event-type taxonomy) — both resolved by founder sign-off.

---

## 12. What This Does Not Change

STD-001, STD-002, STD-003, STD-004 require zero code changes as a result of this specification.

---

**Status:** Ratified 21 July 2026 (founder sign-off). Not yet built — implementation not yet authorized as a Cursor prompt.
