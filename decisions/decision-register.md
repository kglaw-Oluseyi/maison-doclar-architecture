# Decision Register — Decision Intelligence Seed Corpus (C24)
Format: Situation · Decision · Reasoning · Alternatives considered · Status. Every entry is citable as `DI-xxx`.

**DI-001 · Program constitution.** Independent portfolio review commissioned; no prior architectural decision assumed correct by default, except event-scoping (ratified prior). Status: in force.

**DI-002 · Aperture strategy corrected.** Situation: "freeze Aperture" wording. Decision: knowledge is baselined; the repository and product are never frozen; Aperture continues as the actively developed **Maison Signature** edition; **Maison Platform** (Aperture V2 / Event OS) is a new lineage cut from a documented baseline. Alternatives: operational freeze (rejected — live Signature clients); clone-and-freeze (rejected — severs history, freezes a living product). Status: ratified.

**DI-003 · Business map.** Two revenue engines (Events, Studios), one arm's-length product (Plavent), permanently internal nervous system (Nexus, Corporate Intelligence, Platform Engineering). Nexus is the company ERP and never becomes the Event OS. Status: ratified.

**DI-004 · Never-sell classifications.** MPS and the protocol layer of Communications are never sold; the Channel Engine may be commercial one day only stripped of the protocol layer; the seating optimiser is never licensed standalone. Status: ratified (with ICAA's engine/protocol split).

**DI-005 · Nucleus.** Deprecated read-only legacy (security-only, knowledge source, non-operational) — deprecation, not archival. Alternatives: retirement/archive (rejected — repositories are cheap, knowledge is not). Status: ratified.

**DI-006 · Intelligence proxy mandate revoked.** Reality: 15 direct call sites. Decision: govern, don't proxy — AI Governance owns policy/registries/budgets/routing/evaluation/observability and is never in the execution path. Alternatives: enforce the proxy (rejected — latency, coupling, and the mandate had already lost). Status: ratified.

**DI-007 · Premature merges deferred.** Repository consolidation is not performed where Platform absorption makes it redundant work. Status: ratified. Superseded in part by DI-013 (no merge category exists at all).

**DI-008 · Capability constitution.** Maison owns capabilities; systems realize them. Capabilities are peers; none owns another. Asset hierarchy: Knowledge → Capability → Service → Experience. Status: ratified.

**DI-009 · Two-plane architecture.** Execution Plane (event-scoped, owns truth) and Intelligence Plane (reasons over the governed Knowledge Store, returns advice only), reconciling the Guest Knowledge Graph with the event-scoping principle. Knowledge is passive; intelligence is computation over knowledge; the store is named **Knowledge Store**. Alternatives: master guest ID (rejected — ratified principle, contamination history); no graph (rejected — forfeits compounding). Status: ratified.

**DI-010 · Advisory contract.** Envelope and rules per `/contracts/advisory-contract.md`. Execution accepts/modifies/rejects and always records why. Status: ratified.

**DI-011 · MPS elevated.** Maison Protocol System is the company's primary IP, versioned immutably; every event and recommendation pins its MPS version. Status: ratified.

**DI-012 · Platform topology: Option C.** Modular-monolith Execution core + separately deployed Intelligence Plane + worker tier, one monorepo. The candidate Event OS blueprint upheld except its one-application-one-database clause. Alternatives: pure monolith (weaker knowledge preservation), federation (rejected on operational evidence — see Negative Knowledge #1), event-driven backbone (rejected for event-day truth; its fact-log adopted as pipeline transport). Collapse back to one deployable preserved as a two-way door. Status: ratified.

**DI-013 · Knowledge-first repository strategy.** Mandatory Knowledge Preservation Matrix before repository fates; classification justified exclusively by knowledge economics; no merge category; nine classification states; classifications are versioned constitutional state with rationale, evidence, and DI references. Status: ratified.

**DI-014 · MKC.** Every knowledge artifact is invalid unless MKC accepts it. Confidence classes Canonical/Validated/Observed/Hypothesis/Historical; founder content graded Founder-authored → Founder-verified → Constitutionally ratified ("Canonical-by-founder" struck). Failures preserved as Negative Knowledge. Status: ratified in outline; full charter pending.

**DI-015 · Signature advice access.** Signature consumes Intelligence Plane advice under the strict one-way contract (no Knowledge Store reads/writes; event-scoped context only; accept/reject recorded to Decision Intelligence; knowledge admission from Signature only via deliberate human review). The Intelligence Plane is a shared constitutional service. Status: ratified.

**DI-016 · Gateway ruling.** Knowledge split into four independently preserved assets (identity translation; trust/permission filtering; webhook relay/retry; key management). Runtime: Living system while satellites depend on it; no new capabilities; trajectory Legacy → Read-only on named instruction. Policy-enforcement and partner-API futures assigned to the Platform seam and AI Governance respectively. Status: ratified.

**DI-017 · Constitutional repository.** `maison-doclar-architecture` created as the constitutional (not merely architecture) repository, closing Negative Knowledge #7. Structure: constitution/capabilities/knowledge/decisions/architecture/governance/contracts. Status: approved and executed — pushed to kglaw-Oluseyi/maison-doclar-architecture, remote HEAD 6a394d513788 verified via GitHub API on 11 July 2026.

**DI-018 · Alakija sequencing wall.** No system serving the 23 July 2026 event changes behaviour before close-out; extraction work is non-invasive and proceeds immediately. Status: in force.

**DI-019 · Constitutional machine-discoverability & self-contained advice.** Every constitutional artifact exposes metadata (artifact ID, version, status, effective date, supersedes, depends-on, decision refs, evidence refs) via front-matter and the root `manifest.yaml`. Advisory requests are self-contained (event context, version pins, request ID, timestamp); the advisory service never discovers against requesting systems. Status: ratified and executed.

**DI-020 · Register/Intelligence separation & precedence.** The Decision Register is the immutable append-only legal record; Decision Intelligence derived views live in `/decisions/derived` and evolve freely. Conflicting-statement precedence: Constitution → Governance → Current Knowledge → Architecture Standards → Historical Deliverables. Status: ratified and executed.

**DI-021 · Knowledge Extraction Workspace.** `maison-doclar-knowledge-extraction` created as a private staging repository (/raw /extracted /review /promoted /scripts) separating ETL-style extraction from constitutional governance. Promotion into the constitutional repository is an explicit governed action. Raw evidence is committed as excerpts with path+commit provenance, never as full clones, and never containing guest PII. Status: approved and executed.

**DI-022 · Constitutional schema frozen at v1.0.** The manifest and front-matter schema are frozen; schema changes are constitutional changes requiring register entries. Status: ratified and executed.

**DI-023 · Stable identifiers.** Permanent artifact IDs (MPS-/MXS-/MKC-/CAP-/CON-/GOV-/KNW-/STD-/ARCH-/DI- prefixes) replace filename-primary referencing. Advisory responses and cross-references cite IDs. Status: ratified and executed.

**DI-024 · Promotion exit criteria.** Extraction pipeline stages carry explicit exit criteria (raw: provenance verified · extracted: normalized + source-linked · review: reviewer disposition recorded · promoted: constitutional artifact + register entry). Promotion is reproducible, not judgment-based. Status: ratified; executed in the extraction workspace.

**DI-025 · Advisory envelope is a versioned protocol.** Additive = minor; removal/rename = major; consumers declare supported versions. Status: ratified and executed (CON-001 v1.2).

**DI-026 · Assumption registry.** Decisions reference assumptions by ID; lifecycles tracked in GOV-004 and analysed in derived intelligence. Seeded A-001–A-007. Status: ratified and executed.

**DI-027 · Deliverable 07 ratified.** (a) Shared-infrastructure boundary rule ("no domain vocabulary in the interface") and the seven-package kernel set; (b) **C17: build the minimal internal identity provider** — decisive argument event-day sovereignty (A-003); assumption A-008 registered, reopening build-vs-buy if expired; (c) AI Governance realized as registry + revocable per-call-site keys + asynchronous usage telemetry, never interception; (d) the Commission contract as the sole Platform⇄Studios interface with the access-vs-delight rule assigning credential rendering to the Platform worker tier. Status: ratified.

**DI-028 · Architecture Freeze v1.0.** Deliverables 01–07 are Architecture Baseline v1.0 (ARCH-001). Subsequent work is realization/extraction/specification/implementation/migration only; architectural change requires constitutional amendment. Discipline: derive, specify, preserve. Status: ratified and in force.

**DI-029 · Realization flow constraint.** No architectural decision may originate in the Platform repository. The only allowed flow: Evidence → Knowledge Extraction → Constitutional Amendment (if required) → Capability Specification → Platform Implementation. Never code → architecture. Status: ratified.

**DI-030 · Seating Reconciliation Audit executed (read-only).** Divergence report produced in the extraction workspace; Platform Seating Specification v1 (STD-001) drafted, blocked on BLOCKER-S1 (the frozen Mathematical Formulation / Algorithm Design / Validation & Benchmark specifications exist only in prior session records and must be supplied by the principal for constitutional promotion) and on CTR-S1/S2 rulings. No repository was modified by the audit. Status: executed; ratifications pending.

**DI-031 · Historical Design Corpus established.** Prior-session design documents are constitutional evidence, ingested via Git (never chat transport) into the extraction workspace under `/raw/historical-event-os` — verbatim, filenames and dates preserved, immutable, provenance-manifested, principal-attested. Remit is the full corpus; BLOCKER-S1 resolution is a natural outcome of Phase 1 extraction, not the reading lens. Extraction destinations expanded: mps, mkc, capabilities, negative-knowledge, implementation-guidance, decision-intelligence. Status: ratified; structure executed; awaiting first installment from the principal.

**DI-032 · Corpus intake operations.** Announce-then-verify replaces monitoring: the principal pushes and verifies, then instructs extraction to begin; extraction verifies presence and proceeds. Corpus processed in independent batches (Raw → Extracted → Review → Promoted each). Every extracted item ends in exactly one outcome: Promote · Preserve · Reference · Reject · Superseded. Confirmation of ratified decisions is a valid outcome requiring no new artifact. Status: ratified and executed.

**DI-033 · STD-002 Guest Domain Specification drafted.** Twelve invariants derived from ratified taxonomy, fossils, negative knowledge, and validated Aperture machinery; Guest Knowledge Graph explicitly out of scope (Intelligence Plane, Phase 4). Open on founder RSVP ruling (O-1) and MPS v1.0 publication (O-2). Status: drafted; review pending.

**DI-034 · Frozen seating specifications promoted; BLOCKER-S1 cleared; CTR-S1/S2 ratified.** STD-003/004/005 promoted verbatim (metadata-only prepended) from corpus @ 9a645cc9. CTR-S1: the validated Aperture allocator is the initial constructor and degraded-mode implementation; the frozen model is the constitutional Platform target. CTR-S2: ESLA contributes interaction/proposal workflow, Aperture the validated engine, CON-001 governs suggestion production, the frozen model governs correctness. Lineage ratified: Salle → ESLA → Aperture → Frozen Mathematical Specification → Platform. Governance distinction: **Frozen Specifications** (immutable historical evidence) vs **Living Specifications** (maintained, versioned). STD-001 v1.0 Ratified. Programme phase: executing the architecture; the corpus strengthens and validates implementation rather than redefining it. Status: ratified and executed.

**DI-035 · No new architectural primitives in capability specifications.** A capability specification may derive, compose, specialize, or constrain existing constitutional concepts — never invent a new architectural primitive. A genuinely new primitive enters through the constitutional process (decision → amendment → ratification) before any specification references it. Status: ratified and in force.

**DI-036 · STD-006 Communications Specification drafted.** Derived (DI-035-compliant) from the Herald corpus specification, Relay validated knowledge, MPS §7–9, STD-002 contracts, and ratified plane/adapter decisions. Herald's state machine and edge-case catalogue adopted as normative/acceptance material; Herald's embedded cross-event intelligence layer recorded as **Superseded in part** by the two-plane architecture (DI-009) — reassigned to Intelligence Plane pipelines + CON-001 advice. Status: drafted; review pending.
