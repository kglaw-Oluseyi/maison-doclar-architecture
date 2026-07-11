# Maison Doclar — Platform Boundary Definition & Domain Model
**Deliverable 04 of the Strategic Architecture Program**
Status: Draft for principal review · Date: 11 July 2026
Baseline: Deliverables 01–03 accepted with ICAA amendments, all incorporated here: the asset hierarchy is **Knowledge → Capability → Service → Experience**; products compose capabilities and never own them; Protocol Intelligence is elevated to the **Maison Protocol System (MPS)** as primary IP; Guest Intelligence expands to a **Guest Knowledge Graph**; **MXS** is an operating philosophy spanning every physical and digital interaction; **Event Intelligence (C22)** and **Vendor Intelligence (C23)** join as first-class capabilities; Communications splits into a never-sold protocol layer and a potentially sellable engine; AI Governance governs and never proxies; and the architecture must remain valid if every repository is replaced within the decade.

---

## 1. The governing inversion

The old estate was built Repo → API → App. The Platform is built **Knowledge → Capability → Service → Experience**:

- **Knowledge** is what Maison has learned and encodes: protocol, guests, events, vendors, venues, taste. It is the only layer that appreciates.
- **Capabilities** are the stable abilities that operationalise knowledge (Deliverable 03's map, as amended).
- **Services** are the current mechanical carriers of capabilities — replaceable, expected to be replaced.
- **Experiences** are what a human touches: an operator console, a host report, a guest's WhatsApp message, a badge, a seat, the feeling of check-in. Experiences are governed by MXS whether pixel or fabric.

Maison is not building an ERP that stores facts. It is building **the operating system for high-protocol events** — a system that coordinates intelligence. Every boundary below was tested against that distinction: a context earns its place by coordinating knowledge, not by owning tables.

## 2. The Platform boundary

**Maison Platform** is the capability platform. It owns capabilities; it is not itself a product. Products — Maison Signature, the Premium product built on the Platform, Remote View, Studios' offerings, Plavent, future concierge/API/agent products — are **compositions** that consume Platform capabilities through defined contracts.

Inside the boundary (Platform-owned capabilities): MPS, Guest Knowledge, Seating Intelligence, Communications (both layers), Attendance & Access, Event-Day Runtime, Guest Experience Runtime, Reporting & Host Intelligence, Event Intelligence, Venue Intelligence, Vendor Intelligence (knowledge layer), and the commodity substrate (identity, storage, delivery, telemetry).

Outside the boundary, consuming by contract: **Maison Signature** (Aperture lineage — consumes MPS and MXS as versioned libraries where practical, otherwise tracked divergence; never reaches into Platform data), **Studios** (interacts through media commissions only), **Nexus** (owns vendor/client/financial *records*; exchanges with Vendor Intelligence through a defined contract, never shared tables), **Plavent** (consumes at most MXS-brand-subset and public contracts; never Platform data; separable at all times), **Group functions** (AI Governance, MXS authority, Corporate Intelligence) which sit above the Platform and govern it.

The Platform test for any future proposal: *if this thing were deleted, do products lose an ability (capability → inside) or lose a feature (composition → outside)?*

## 3. The two-plane resolution (requires ratification)

The Guest Knowledge Graph and the standing event-scoping principle conflict on the surface. Resolution: the Platform runs two planes with different physics.

**The Operational Plane** is event-scoped, exactly as ratified. Every aggregate belongs to one event; guest identity is issued fresh per event; phone remains the within-event dedup key; nothing operational joins across events. This plane runs events. Its virtues are blast-radius containment, data hygiene, and the privacy posture that event scoping already gives you.

**The Knowledge Plane** is where compounding lives: the Guest Knowledge Graph, Event Intelligence, Venue Intelligence, Vendor Intelligence. It is fed **only** through the controlled analytical pipelines the standing principle already names — now given real architecture: pipelines are declared, versioned, consent-aware, and auditable; linkage across events happens here under explicit matching rules (not a master ID leaking back into operations); and the knowledge plane can **advise** the operational plane (a seating suggestion, an attendance prior, a vendor recommendation) but never *identifies into* it — advice crosses the boundary as scored suggestions attached to event-scoped records, with provenance.

This preserves the letter and intent of the standing principle while unlocking the graph. It also localises the estate's most sensitive material in one governed place.

**A necessary caution, stated plainly.** The graph as envisioned includes political, religious, and business affiliations, influence and risk scores, and behavioural prediction. Under Nigeria's NDPA and the GDPR (relevant for UK/EU-resident guests), several of those are special-category or high-risk data whose lawful handling depends on consent basis, purpose limitation, and security controls — and the exact obligations should be verified against current legal advice rather than asserted here as fixed. Architecturally, the two-plane design is the strongest available posture: the knowledge plane gets field-level sensitivity classification, purpose-bound pipeline declarations, retention rules, and access that is narrower than operator access. This is a design constraint of the plane, not an afterthought — and it is also a commercial asset: hosts of this calibre will ask how their guests' data is treated, and the answer is part of the luxury.

## 4. Bounded contexts and domain model

**MPS — Maison Protocol System** *(shared kernel; the company's primary IP)*. Owns: title taxonomy and address forms, precedence rules, household composition grammar, tier definitions, entourage/ADC conventions, invitation culture, communication etiquette, diplomatic-handling rules, seating-politics rules, protocol exceptions register. Form: a versioned, tested library plus a governed rule store — protected like source code of record, with change control and provenance. Consumed by every context; defined by none of them. Aggregate style: MPS has no per-event aggregates; it is reference knowledge with versions pinned per event (an event records which MPS version governed it — essential for Event Intelligence to learn from protocol exceptions).

**Guest Knowledge** *(knowledge plane)*. Aggregate root: GuestDossier (graph node), linked to Household, Affiliation, RelationshipEdge (with confidence), AttendanceHistory, PreferenceProfile, AccessibilityNeeds, CommunicationStyle. Invariants: every fact carries provenance, consent basis, and sensitivity class; edges carry confidence, never certainty; nothing here is directly addressable from operational surfaces.

**Guest Data (operational)** *(operational plane)*. Aggregate roots: Household (invitation unit) and Engagement (attendance unit) per the ratified taxonomy; Guest, RSVP lifecycle (three-state, respondent-vs-attendee), ImportBatch with dedup ledger, DataQuality (G0–G5). Invariant: one event, always.

**Seating Intelligence** *(operational, advised by knowledge plane)*. Aggregates: SeatingProblem (rules, fixtures, zones, tables, seats), AllocatorRun (deterministic, explainable, reproducible — the frozen formulation is normative), ReviewCycle (host review, annotations, change requests, action stack). Invariant: every placement must be explainable in host-facing language; determinism is a feature of luxury (the host can ask *why* and receive an answer).

**Communications** *(two layers, split per amendment)*. **Protocol Messaging layer** (never sold): address-form resolution, etiquette rules, template governance under MPS, tone control under MXS. **Channel Engine** (potentially commercial one day): campaign orchestration, queueing, suppression/consent, delivery, inbound routing, disambiguation. Aggregates: Campaign, Audience (declared from operational guest data), MessageJob, SuppressionRecord, InboundThread. The engine may one day serve external customers *only* stripped of the protocol layer.

**Attendance & Access** *(operational)*. Aggregates: Credential (QR/card/biometric enrolment with consent record), AccessGrant, CheckInLedger (append-only truth), ReEntryVerification. Event-scoped credentials die with the event.

**Event-Day Runtime** *(operational)*. Aggregates: EventDaySnapshot (the distributed truth device fleets acknowledge), DeviceSession, IncidentLog, FloorAssignment, ShowControl (MC/room/display orchestration). Invariant: degraded-network operation is a first-class requirement — the runtime must be correct offline and reconcile on return.

**Guest Experience Runtime** *(operational, MXS-governed)*. Aggregates: GuestSurfaceSession, MemoryCapture (the unified guestbook/photo-wall capability), ContentExperience (lookbook/giftpack), PublishedSite (microsite/embeds), RemoteAttendance. All surfaces render through MXS; none defines its own visual language.

**Reporting & Host Intelligence** *(reads both planes through governed queries)*. Aggregates: ReportCommission, NarrativeReport, CoverageRecord, MomentRegister. Reports are MXS artifacts — a host report is an experience, not a printout.

**Event Intelligence (C22)** *(knowledge plane; new)*. Aggregate root: EventLesson — captured observations (timeline drift, bottlenecks, acoustics, vendor punctuality, power, weather, crowd density, protocol exceptions, best camera/stage/valet decisions) each tagged to venue, vendor, MC, guest-population shape, and MPS version. Pipelines distil lessons into: VenueProfile updates (feeding Venue Intelligence), VendorPerformance updates (feeding Vendor Intelligence), PlanningPriors (feeding the next event's timeline and staffing), and AllocatorPriors (feeding seating). Invariant: capture is an event-day *workflow obligation*, not an optional habit — the runtime prompts for structured lessons at defined moments, because knowledge that isn't captured at hour three is gone by midnight.

**Vendor Intelligence (C23)** *(knowledge plane; new)*. Owns knowledge — which photographer works with which family, venue, MC, budget, culture, protocol level — as scored, provenance-carrying edges. Boundary contract with Nexus: **Nexus owns vendor records and money; Vendor Intelligence owns vendor knowledge.** Nexus emits engagement facts (vendor X served event Y in role Z); Event Intelligence emits performance observations; Vendor Intelligence composes them into recommendations that planning surfaces consume. No shared tables; facts cross by contract.

**Venue Intelligence** *(knowledge plane; reserved, now fed)*. VenueProfile compounds from Event Intelligence. Thin today by design; the slot exists so lessons have somewhere to accrue.

**Identity (staff/host/planner)** *(commodity service)*. One provider, all operator/host surfaces, role and feature-access model generalised from Signature's credential work. Guests never enter this system — guest identity remains event-scoped in Attendance & Access.

**AI Governance** *(group function)*. Policy, prompt registry, model registry, budgets, routing policy, evaluation, observability, provider compliance. Never in the execution path.

**MXS** *(group authority, consumed as artifacts)*. Constitution (the philosophy: how luxury behaves under load, in error states, in silence), token and motion packages, voice/sound direction, photography/video grammar, print and physical standards (cards, badges, uniforms, stage), surface guidelines per register (operator, host, planner, guest, staff). Every context above consumes MXS; the deck a host sees and the dashboard an usher sees are the same brand at different registers.

## 5. Dependency rules

Allowed: everything may depend on MPS and MXS (versioned artifacts); operational contexts may depend on the commodity substrate; knowledge-plane contexts consume operational facts only through declared pipelines; advice flows knowledge→operational as scored suggestions with provenance. Forbidden: operational joins across events; any context redefining MPS concepts locally; products reaching into Platform tables; Nexus↔Platform shared schemas; Plavent depending on any Platform data; knowledge-plane identifiers appearing in operational records; AI Governance in any request path. Signature's divergence is managed by contract: it pins MPS/MXS versions where adopted, documents where it diverges, and the divergence register is reviewed each quarter — divergence is legitimate; *unrecorded* divergence is not.

## 6. What Deliverable 04 defers

Topology (modular monolith vs distributed — Deliverable on Platform Architecture, where the candidate twelve-module monolith is evaluated against these contexts), repository strategy, schema design, pipeline technology, and sequencing. The claim of this document is narrower and prior: **these contexts, these two planes, and these dependency rules are the shape of the operating system** — valid regardless of which repositories carry them, this decade or next.

---
*Next deliverable on approval: **05 — Platform & Company Architecture** (topology decision for Maison Platform against the candidate modular-monolith blueprint; service realisation of the two planes; Signature divergence mechanics). Ratification requested on: (a) the two-plane resolution in §3, (b) the Nexus/Vendor-Intelligence boundary contract, (c) MPS as the working name for the elevated protocol capability.*
