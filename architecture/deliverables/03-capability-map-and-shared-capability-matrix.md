# Maison Doclar — Capability Map & Shared Capability Matrix
**Deliverable 03 of the Strategic Architecture Program**
Status: Draft for principal review · Date: 11 July 2026
Baseline: Deliverables 01–02 (accepted, with ICAA amendments incorporated: Nucleus deprecated not archived; Intelligence re-scoped to AI governance; premature merges deferred; Maison Signature / Maison Platform naming; Maison Experience System elevated to first-class capability).
Governing constraint for this document: **capabilities are derived from the enduring business, not from repositories.** Repositories appear only as current implementations.

---

## 1. Method

A capability is something Maison Doclar must be able to do in 2036 regardless of which software exists then. The test applied to every candidate: *if every repository were deleted tonight, would the company still need this ability tomorrow, and would rebuilding it require knowledge that cannot be bought?* Capabilities that pass the second half of that test are **distinctive** — they are the moat. Capabilities that pass only the first half are **core** (essential, buildable) or **supporting** (essential, boring). Capabilities that any competent company acquires off the shelf are **commodity**, and the strategic rule for them is inverted: never build twice, never differentiate, never let them consume moat-grade attention.

Products come and go; this map is intended to survive multiple generations of software, including both the Signature and Platform lineages. Products **compose** capabilities; no product owns one exclusively unless the map says so.

## 2. The Maison Doclar Capability Map

### Tier 1 — Distinctive (the moat; never sold, never outsourced, always in-house talent)

**C1 · Protocol Intelligence.** The encoded understanding of Nigerian and diaspora high-society structure: titles and precedence (royal, chieftaincy, military, religious, political), household composition as the unit of invitation, VIP tiering, entourage/ADC/security-detail conventions, gifting and aso-ebi custom, host psychology. Why enduring: it is the reason clients choose Maison over a competent generic planner. Owner: Maison Doclar Events (the house). Every other capability consumes C1; none may redefine it. Current implementations are fragments — enum taxonomies and models scattered across the estate — and the reconciliation in §5 makes C1 a single versioned artifact for the first time.

**C2 · Guest Intelligence.** Knowing who is coming, who they are to each other, and what they will actually do: event-scoped identity, deduplication, enrichment, relationship graphs, RSVP behaviour, attendance prediction, data-quality maturity (the G0–G5 framework). Why enduring: every event's success is decided here before the venue opens. Owner: Events Platform. Distinct from C1: C1 is *what the culture requires*; C2 is *what these particular people will do*.

**C3 · Seating Intelligence.** Constraint modelling, allocation optimisation, explainability, and host-review workflow for the politics of the room. Why enduring: it is the single hardest recurring problem in the business and the most defensible algorithmic asset (the frozen mathematical formulation, eleven neighbourhoods, annealing strategy). Owner: Events Platform. Currently the estate's worst duplication (a diverged fork); reconciliation plan in §5.

**C4 · Maison Experience System (MXS).** The experience language itself: typography, colour, motion, photography and video grammar, sound and voice, copy tone, interaction feel, brand behaviour under stress (what "luxury" means when a check-in queue forms). Why enduring: guests remember how an event *felt*; MXS is that feeling made reproducible across every surface — operator apps included, because staff confidence is part of the guest's experience. Owner: a group-level design authority, not any product team. MXS is consumed by everything (both lineages, Studios, Plavent's brand-permitting subset, marketing, microsites) and owned by none of them. Current implementation is embryonic: a thin UI kit in `maison-doclar-shared`, design tokens, and conventions living in the founder's head — the largest gap between strategic importance and current maturity anywhere on this map.

**C5 · Event-Day Orchestration.** Running the room live: check-in ledgers, biometric re-entry, floor operations, incident handling, MC/room/display coordination, device fleets, offline resilience, snapshot-based truth. Why enduring: it is where the company's reputation is won or lost in real time, and its knowledge (what fails at hour three of a 700-guest investiture) is earned only by operating.

### Tier 2 — Core (essential; built in-house; excellence expected but not secrecy)

**C6 · Communications.** Multi-channel, protocol-aware guest messaging: WhatsApp-first, template governance, suppression and consent, inbound disambiguation, campaign orchestration. Consumes C1 (titles, address forms) and C2 (audiences). Never sold standalone (per Deliverable 02), but built to core-engineering standard.

**C7 · Guest Experience Delivery.** The digital surfaces guests touch: personal portals, memory capture, lookbooks, giftpacks, microsites, embeds, live participation, remote attendance. Composes MXS + C2 + C6. This is the upsell menu of the house.

**C8 · Media Production.** Voice, music, cinematography, personalised video, credential/asset rendering, transcode pipelines. Owner: Studios. Interfaces with Events through commissions (an event orders media), never through shared tables.

**C9 · Access & Guest Identity.** Event-scoped credentialing: QR, access cards, biometric enrolment/verification, token lifecycles, consent. Event-scoped by standing principle — no cross-event master identity; controlled analytical pipelines are the only cross-event path.

**C10 · Reporting & Host Intelligence.** Turning operational truth into host-facing narrative: attendance stories, coverage, moments, post-event reports fit for a Prince and a Doctor. Composes C2 + C5 + MXS.

### Tier 3 — Supporting (essential and deliberately boring)

**C11 · Revenue Operations** — invoicing, disbursements, change orders, contracts (the Nexus estate; money and legal exposure; conservative change velocity). **C12 · Vendor & Compliance Management** — vendor registry, compliance registers, accreditation. **C13 · Planning & Scheduling** — timelines, run-of-show, task taxonomies, milestones. **C14 · Corporate & Knowledge Intelligence** — the Orion function: ecosystem awareness, decision logs, passports, the knowledge baseline itself; this capability is the institutional memory that mitigates the founder bus factor. **C15 · Venue Intelligence** — venue knowledge compounding across events (emerging; hold space, invest later). **C16 · AI Governance** *(re-scoped per ICAA)* — prompt registry, model registry, budget management, fallback routing, evaluation, cost analytics, PII controls, safety policy. Govern, don't proxy: products call providers directly under registered configurations; governance observes, budgets, and can revoke.

### Tier 4 — Commodity (buy or standardise once; never differentiate)

**C17 · Staff/Host/Planner Authentication & Authorization** — one identity provider for humans-who-operate, built or adopted once, consumed everywhere; currently re-implemented ~30 times, the estate's most wasteful duplication. **C18 · Storage & Media Infrastructure** — B2, presigned URLs, CDN. **C19 · Payments** — Paystack/Stripe adapters. **C20 · Delivery Plumbing** — Twilio/Termii/Resend adapters beneath C6. **C21 · Telemetry & Analytics plumbing** — collection and dashboards beneath C10/C14.

## 3. Shared Capability Matrix

| Capability | Tier | Business owner | Consumed by | Current implementations (evidence) | Target home | Duplication status |
|---|---|---|---|---|---|---|
| C1 Protocol Intelligence | Distinctive | The house | All | Enums/models in Relay, Nucleus, Aperture, macs | **Versioned protocol library** (shared kernel) | Fragmented — high priority |
| C2 Guest Intelligence | Distinctive | Events Platform | C3, C6, C7, C9, C10 | Aperture, Nucleus, Relay, PWA, facegate, ESLA, remote-view, giftpack | Maison Platform guest-data module; Signature keeps its own | 8-way — highest priority |
| C3 Seating Intelligence | Distinctive | Events Platform | Operators, hosts | Aperture seating domain; ESLA/Salle fork | Maison Platform seating module | Diverged fork — high priority |
| C4 MXS | Distinctive | Group design authority | Everything | shared/ui (thin), per-app conventions | **MXS package suite** (tokens, motion, voice, sound, copy) | Embryonic — strategic gap |
| C5 Event-Day Orchestration | Distinctive | Events Platform | Operators, staff | Aperture event-day domain, md-events, md-attend, live, facegate | Maison Platform event-day module | 4-way overlap — medium |
| C6 Communications | Core | Events Platform | C7, operators | Relay; Aperture campaigns; (MACS legacy) | Maison Platform comms module (Relay as donor engine) | 2-stack — high priority |
| C7 Guest Experience | Core | Events Platform | Guests, hosts | pwa, guestbook, photo-wall, lookbook, giftpack, microsite, embeds, live, remote-view | Maison Platform guest runtime + Remote View standalone | Surface sprawl — absorb per roadmap |
| C8 Media Production | Core | Studios | Events (via commissions) | voxlier, creative-studio, music, djos, video-utility, assets | Studios Audio + Studios Visual | 6-app sprawl — consolidate in Studios track |
| C9 Access & Guest Identity | Core | Events Platform | Event-day, guests | facegate, badges, assets(cards), gateway identity map | Platform access module; facegate stays standalone surface | Moderate |
| C10 Reporting & Host Intelligence | Core | Events Platform | Hosts, principal | Aperture reports, macs reports, nexus post-event | Platform reporting module | Moderate |
| C11–C13 Revenue/Vendor/Planning | Supporting | Business Ops | Principal, staff | Nexus | **Nexus, permanently** | None — correctly isolated |
| C14 Corporate & Knowledge Intel | Supporting | Group Services | Principal | orion, cleo, scribe, passports | Orion-centred; baseline repo | Low |
| C15 Venue Intelligence | Supporting | Events Platform | C3, planning | Venue models in Aperture/Nexus/ESLA | Platform domain slot (reserved) | Thin |
| C16 AI Governance | Supporting | Group Services | All AI callers (15 sites) | intelligence (mis-scoped) | Re-scoped Intelligence service | Mandate broken — re-charter |
| C17 Staff Auth | Commodity | Platform Eng | All operator apps | ~30 local implementations | One identity provider | 30-way — worst waste |
| C18–C21 Storage/Payments/Delivery/Telemetry | Commodity | Platform Eng | All | Standardised adapters (mostly consistent) | Shared kernel adapters | Acceptable |

## 4. Ownership rules (the constitution of the map)

Every capability has exactly one owner; ownership means owning the definition, the schema, and the roadmap. Products compose capabilities through published interfaces and never reach into another capability's tables. Distinctive capabilities are staffed before core, core before supporting; commodity capabilities are forbidden from custom re-implementation once the shared version exists. C1 is upstream of everything: no product may define a title, tier, or household shape locally — they import the protocol library and may only *propose* changes to it. MXS is upstream of every surface: products consume it; only the design authority changes it. Cross-event data flows exist solely through the controlled analytical pipelines already ratified as standing principle.

## 5. Reconciliation plans for the duplicated clusters

**Guest data (8 implementations).** Do not attempt a live consolidation across satellites — that is exactly the premature-merge trap the ICAA flagged. Instead: (a) extract the *canonical guest domain definition* (from the taxonomy overhaul work plus Aperture's operational refinements: three-state RSVP, respondent-vs-attendee, household/engagement split, G0–G5 quality) into the knowledge baseline as the normative model; (b) Maison Platform implements it once as the guest-data module; (c) satellites are never retrofitted — they are absorbed on the Platform roadmap and their local guest tables die with them; (d) Signature keeps its own copy for its lifetime, tracked as a managed divergence.

**Seating (diverged fork).** Before Platform's seating module is designed, run a one-time **fork reconciliation audit**: diff ESLA/Salle against Aperture's seating domain at the model, rule, and algorithm level; produce a superset capability inventory (Salle's canvas/AI-proposal loop + Aperture's rule sets, action stack, allocator runs, tag-rank/seat-for-me flows); the frozen mathematical formulation is the arbiter where the forks disagree. Output feeds the Platform seating module; afterwards ESLA becomes a deprecated knowledge source like Nucleus.

**Communications (2 stacks).** Relay is the engine of record for channel orchestration; Aperture's campaign machinery is the source of record for *operator workflow* knowledge (audience building, template specs, inbound disambiguation). Platform's comms module takes Relay's engine architecture and Aperture's workflow knowledge. No merging of the two live stacks in the interim; Signature keeps its stack, Relay keeps serving what it serves.

**Staff auth (≈30 implementations).** The one commodity worth acting on ahead of the Platform roadmap, because every new service multiplies it. Decision required (flagged, not assumed): build a minimal internal identity provider inside the Platform shared kernel, or adopt a managed provider. Either way, the rule starts now: no new service ships its own User/Session model.

**AI access (15 call sites).** Per the re-chartered C16: call sites remain direct; each registers its model/prompt/budget profile with the governance service; governance provides keys-by-policy, spend observability, fallback routing configuration, and evaluation hooks. Migration is registration, not rewiring — which is why this one can proceed without waiting for Platform.

## 6. What this map deliberately does not do

It does not assign repositories to fates (Deliverable 02's register stands, as amended). It does not design module interfaces, schemas, or the Platform repository (Deliverables 07–11). It does not sequence work (Roadmap deliverable). Its sole claim: **these twenty-one capabilities are the stable abstractions the next decade of Maison software will compose**, and the four Tier-1 duplications — guest data, seating, protocol fragments, and the MXS vacuum — are where architectural attention buys the most company value per unit of effort.

---
*Next deliverable on approval: **04 — Platform Boundary Definition & Domain Model** (bounded contexts for Maison Platform drawn from this capability map; the Signature/Platform divergence contract; where Studios, Nexus, and the satellites sit relative to the boundary).*
