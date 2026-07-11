---
artifact_id: STD-002
version: "1.0-draft"
status: Draft — implementable on MPS v1.0 publication; review pending
effective_date: 2026-07-11
depends_on: [MPS-001, MKC-001, CAP-001, CON-001]
decision_refs: [DI-033]
evidence_refs: [KNW-001 §1 §6, KNW-003, maison-doclar-knowledge-extraction/extracted/mps/mps-corpus-normalized.md]
---
# Guest Domain Specification (STD-002) — Execution Plane guest-data module
Derived, not designed. Sources: the ratified taxonomy (Nucleus donor), Aperture's validated operational machinery (KNW-001 §1), the MPS corpus extraction, and the fossils register. The Guest Knowledge Graph is **out of scope** — it is Intelligence Plane material (Phase 4); this module owns operational guest truth only.

## 1. Scope
Owns: households, guests, engagements, RSVP lifecycle, imports and deduplication, guest data quality, guest-trust protocol state, relationship declarations (operational — used by seating), audience declaration for downstream consumers. Does not own: protocol definitions (imports MPS by pinned version), cross-event knowledge (Intelligence Plane, via declared pipelines only), credentials (C9), message state (C6).

## 2. Invariants
G-1 **Event-scoped absolutely**: every aggregate carries eventId; no query joins across events (fossil: event-scope verification; Negative Knowledge #2). G-2 **Household is the invitation unit; engagement is the attendance unit** (ratified taxonomy). G-3 **Phone is the within-event dedup key**, normalized per the validated normalizer lineage (`phone-normalize.ts` donor); fuzzy name-matching only ever *proposes*, never auto-merges. G-4 **Three-state RSVP** (confirmed / declined / pending) with orthogonal flags; "no response" is never a state (fossil `retire_rsvp_no_response`); NON_RESPONDER is an escalation flag. Attendance *mode* (in-person/remote) is a separate dimension — final shape pins to the founder's §6 corpus ruling. G-5 **RSVP never gates** any downstream system; it informs eligibility only. G-6 **Respondent ≠ attendee**: the person who answers is modelled distinctly from those attending (ratified data-acquisition principle). G-7 **Imports never race allocation** (fossil `guest_list_import_lock`): import lock is a module-level primitive exposed to seating. G-8 **Every guest mutation is revisioned** with actor and source (Nucleus pending-change/revision donor pattern); host/planner-originated changes enter a review state before commit. G-9 **Duplicate handling is a ledger, not an event**: proposals, dismissals, merges, and couple-split reviews are first-class records (Nucleus + Aperture donors). G-10 **Data quality is graded G0–G5** per guest and per event, computed not asserted, and consumable by readiness (the go-live computation donor). G-11 Every record pins the MPS version in force at creation; taxonomy fields (titles, tiers, household types, group types) are MPS references, never local enums (Negative Knowledge #5). G-12 Tenancy is present in the schema from day one (A-006) even while single-tenant in operation.

## 3. Aggregates
**Household** (type, primary/member roles, staff-inclusive support) → **Guest** (person within event: identity fields, MPS title reference, tier, accessibility needs under MKC sensitivity class, contact set with per-channel validity) → **Engagement** (attendance record: mode, plus-ones with host-allocated allowance, event-day linkage) → **RsvpThread** (token lifecycle with rotation/resend-alternate, submissions, three-state status + flags, respondent identity) → **ImportBatch** (source, column mapping, preview, sync-review, anomaly detection, lock acquisition) → **DedupLedger** (proposals with match basis, dismissals, merges with survivorship record, couple-split reviews) → **GuestRevision** (append-only) → **TrustChecklist** (per-event guest-trust ladder state, MPS §8) → **AudienceDeclaration** (named, criteria-based guest sets published to comms/seating — consumers receive membership snapshots, never query guest tables).

## 4. Contracts
Downstream consumers (seating, comms, access, event-day, reporting) read through published module interfaces: audience snapshots, guest summaries scoped to declared fields (Gateway's permission-filtering knowledge, donated), import-lock signalling, and change events on the append-only fact log (pipeline transport per DI-012). No module joins guest tables directly. Intelligence Plane receives facts exclusively via declared pipelines; advice returns per CON-001 attached to event-scoped ids.

## 5. Acceptance corpus
Aperture's guests/rsvp test suites (import preview, parse, exact-duplicate merge, headcount reconciliation, regrets-only, token lifecycle) adopt as behavioural specifications. Edge cases carried: multi-phone guests, shared family names, host-allocated-unconfirmed plus-ones, alternate-contact resend, couple splits, import re-sync after manual edits.

## 6. Open items
O-1 Final RSVP flag/mode shape — founder ruling on corpus §6. O-2 MPS v1.0 publication (G-11 dependency). O-3 Relationship-declaration richness (strength scales) — reconcile with STD-001's consumption during seating implementation.
