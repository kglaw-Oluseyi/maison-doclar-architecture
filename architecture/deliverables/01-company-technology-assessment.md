# Maison Doclar — Company Technology Assessment
**Deliverable 1 of the Strategic Architecture Program**
Status: Draft for principal review · Date: 11 July 2026
Evidence base: full shallow clones of 32 repositories under `kglaw-Oluseyi`, schema extraction, dependency analysis, system passports, and gateway/OS documentation. `urbanac` excluded per scope.

---

## 1. Executive Summary

Maison Doclar is not yet a collection of software products. It is **one luxury event operating company that has built itself a private, vertically-integrated operating system** — and inside that operating system, three distinct things are trying to be born: an event delivery platform, a media production house, and a marketplace product. The repository evidence supports your instinct that a software company is emerging, but the emergence is earlier and messier than the "collection of products" framing suggests. What exists today is closer to **one product-in-waiting (the event platform), one genuine external product candidate (Plavent), one internal creative-production vertical, and a corporate nervous system (Orion/CLEO/Scribe/Intelligence/Gateway) built for a company of one principal.**

The single most consequential finding is that **the ecosystem contains three overlapping generations of the same platform**, all live, all divergent:

1. **Generation 1 — Nexus** (`maison-event-nexus`, 63 models, 73K LOC): the business-operations platform — clients, vendors, contracts, finance, compliance, run-of-show, planner tasks. This is the *event production company* platform.
2. **Generation 2 — The Federated OS** (Nucleus + Gateway + ~20 satellite services): the microservice era — canonical guest registry in Nucleus, identity resolution in Gateway, specialist satellites (Relay, Assets, ESLA/Salle, md-events, PWA, facegate, attend, remote-view, giftpack, guestbook, photo-wall, lookbook, microsite, embeds, badges, macs).
3. **Generation 3 — Aperture** (89 models, 165K LOC): a single-tenant guest-operations monolith that has quietly **re-absorbed the domains of at least five satellites** — seating (from ESLA/Salle, with near-identical model names proving direct lineage), messaging/campaigns (overlapping Relay), event-day runtime (overlapping md-events), access cards (overlapping Badges/Assets), and guest import/dedup (overlapping Nucleus).

Generation 3 exists because Generation 2's federation cost more than it returned for a sole operator running live events. That is not a failure of discipline; it is evidence about what architecture this company can actually operate. Any future architecture — including Event OS — must be judged against this observed reality, not against a textbook.

## 2. What business does Maison Doclar actually operate?

Reading the schemas rather than the READMEs, four business lines are visible:

**A. Luxury event delivery (the operating business).** The overwhelming majority of code serves one flow: acquire a guest list → clean and enrich it → communicate (WhatsApp/SMS/email, with a title taxonomy that encodes Nigerian royal, chieftaincy, military, religious, and political protocol directly in Relay's enum definitions) → seat guests under social-constraint optimisation → run the event day (check-in, biometric re-entry, ushering, incidents, MC/room orchestration) → deliver premium digital experiences (PWA, guestbook, photo wall, lookbook, giftpack, remote attendance) → report to hosts. This is a genuine, deep, culturally-specific domain. The protocol knowledge embedded in these schemas (household-as-invitation-unit, five VIP tiers, entourage/ADC/security-detail modelling, aso-ebi coordination in md-macs) is the company's real intellectual property.

**B. Event business operations.** Nexus covers vendor sourcing, contracts, compliance (jurisdictions, LASRERA-style requirement registers), invoicing, disbursements, change orders, post-event reports, and staff accreditation. This is back-office knowledge, distinct from guest-facing delivery, and it is the only place in the portfolio where money, vendors, and legal obligations are modelled.

**C. Creative and media production.** Voxlier (voice/video DAW), Creative Studio (AI cinematography), Music, DJOS (event music intelligence), Video Utility, and Assets form a coherent production vertical: TTS/voice cloning, FFmpeg pipelines, B2 media storage, personalised guest video. These share infrastructure patterns but almost no domain models with the event platform — they connect to it only through `nucleusEventId`/guest identity.

**D. Marketplace (Plavent).** The one repository whose schema (25 models) describes a **multi-party transactional business** — a marketplace for Nigeria's professional events economy. It is the only repo that is unambiguously an external product rather than an internal tool.

Alongside these sits the **corporate nervous system**: Orion (COO intelligence — reads every repo, health-checks the ecosystem), CLEO (commercial officer), Scribe (documents/intake), Intelligence (intended sole owner of the Anthropic key), and Gateway (identity resolution and inter-system routing). These are internal tools that encode how a one-person principal runs a thirty-service company.

## 3. What software company is emerging?

The honest answer: **a vertical platform company for high-protocol events, currently operating in single-tenant mode with itself as the only tenant.** The distinguishing assets are not technical — Next.js/Prisma/Railway/Neon is commodity. The distinguishing assets are:

- The **guest-protocol domain model** (households, titles, tiers, entourage, cultural group types) — no Western event SaaS models this.
- The **seating optimisation lineage** (ESLA → Aperture: constraint rules, allocator runs, eleven optimisation neighbourhoods per the Event OS spec work) — a defensible algorithmic capability.
- The **communications intelligence** around WhatsApp-first, title-correct, protocol-aware messaging.
- The **event-day operational runtime** proven on real 700+ guest events.

That is the shape of a company, and it is exactly the capability set the Event OS candidate architecture already circles. Plavent is a second, separable bet.

## 4. Systemic findings (evidence-backed)

**F1 — Guest identity is duplicated in at least eight schemas.** Nucleus, Aperture, PWA, facegate, ESLA, remote-view, giftpack each define a Prisma `Guest` model; Relay defines its own guest table in Drizzle with its own `rsvp_status` and `guest_source` enums. Gateway exists precisely to make "email, QR token, PWA guest id map to one canonical guestId" — yet each satellite still owns a divergent local guest shape. The stated principle (no cross-event master guest ID, phone as within-event dedup key) is applied inconsistently across these copies.

**F2 — The seating capability exists twice, with shared ancestry.** ESLA/Salle's models (`Layout, VenueFixture, Zone, Table, Seat, AllocatorSuggestion, Assignment, HostReview, HostAnnotation, ChangeRequest, Snapshot, ExportJob, ValidationIssue`) reappear name-for-name inside Aperture. Aperture's copy has evolved (SeatingRuleSet, SeatingActionStack, AllocatorRun, TagRankRequest, SeatForMeRequest); ESLA's has the standalone Salle canvas and its own AI proposal loop (`SalleProposal`, `SalleConversationMessage`). Neither is a superset of the other.

**F3 — Two live messaging stacks.** Relay is a purpose-built Hono/Drizzle/BullMQ multi-channel orchestrator with suppression management, form system, and template/font libraries. Aperture independently carries Campaign/Audience/MessageTemplate/MessageJob/WhatsAppTemplateSpec/InboundMessage plus direct Twilio and Resend dependencies. The MACS Apps Script lineage is a third, historical stack whose reporting knowledge partially migrated into md-macs and Aperture.

**F4 — The AI ownership principle is broken in practice.** `maison-doclar-intelligence` was designed to own the Anthropic key. Fifteen repositories call Anthropic directly (Aperture, Nexus, DJOS, Guestbook, Lookbook, Photo-wall, Plavent, Gateway, Orion, Relay, Events, Embeds, CLEO, ESLA, Intelligence itself).

**F5 — Authentication is re-implemented ~30 times.** Nearly every repo carries `jsonwebtoken`/`jose` and its own User/AdminUser/Session models. Aperture additionally owns Host/Planner credentialing with feature-access exceptions. There is no shared identity provider for staff/host/planner personas, only Gateway's *guest* identity mapping.

**F6 — The Gateway is architecturally sound and operationally bypassed.** Its docs describe a small, safe routing/identity/permissions layer that stores no operational data — the right idea. But Aperture (the centre of gravity) integrates providers directly, and the satellites vary between Gateway-mediated and direct integration. Several passports carry Gateway System IDs (md-attend, PWA, embeds), others do not.

**F7 — Governance-by-documentation is real but fragmenting.** 29 of 32 repos carry a `SYSTEM_PASSPORT.md`; several carry per-repo copies of `MAISON_DOCLAR_OS.md` declaring themselves "the single source of truth." There are now at least three "source of truth" documents (ESLA's copy, Gateway's copy, plus the Event OS specifications that live only in conversation history, not in any repository). **The candidate Event OS architecture is currently not version-controlled anywhere.** That is an immediate, cheap-to-fix risk.

**F8 — Infrastructure standardisation mostly succeeded.** Railway + Neon (schema-per-app: `app_relay`, `app_nexus`, `app_orion`…) + Backblaze B2 + Twilio/Termii/Resend + shared `binaryTargets`/Prisma-6 constraints are consistent across the estate. `maison-doclar-shared` is thin (types for gateway/nucleus, a small UI kit, config) and under-adopted, but the platform substrate is coherent. This is a strength most multi-repo estates never achieve.

## 5. Portfolio classification

| Repository | Classification | Verdict direction (to be argued fully in Deliverables 5 & 11) |
|---|---|---|
| maison-doclar-aperture | **Platform (current flagship)** | Preserve as Signature-tier system; primary knowledge donor to Event OS |
| maison-event-nexus | **Platform (business ops)** | Distinct vertical; candidate to remain independent as the "company OS" for finance/vendors/compliance |
| nucleus | Infrastructure → legacy | Registry role superseded by Aperture in practice; knowledge donor (taxonomy, household model) |
| maison-doclar-gateway | Infrastructure | Correct concept; future depends on Event OS topology decision |
| maison-doclar-relay | Platform module | Communications engine; strongest satellite; donor/absorbee for Event OS comms module |
| ESLA (+Salle) | Platform module (duplicated) | Seating knowledge donor; reconcile with Aperture's fork before Event OS seating module |
| maison-doclar-events, md-attend, facegate, md-remote-view | Platform modules (event-day) | Attendance/access bounded context |
| maison-doclar-pwa, guestbook, photo-wall, lookbook, giftpack, microsite, embeds, live | Platform modules (guest experience) | Guest experience bounded context; heavy per-app duplication of auth/storage |
| maison-doclar-badges, maison-doclar-assets, md-video-utility | Platform modules (media/credential production) | Merge candidates within a media pipeline |
| voxlier, md-creative-studio, maison-doclar-music, maisondoclar-djos | **Creative vertical** | Separate business line; should not enter Event OS |
| md-macs | Platform module (aso-ebi/merch) | Distinctive domain knowledge; keep |
| orion, cleo, md-scribe, maison-doclar-intelligence | Internal corporate tools | Keep independent; Intelligence's key-ownership mandate needs a decision (enforce or retire) |
| maison-doclar-shared | Reusable capability (under-adopted) | Grow or fold into Event OS shared kernel |
| plavent | **External product** | Keep fully independent from Event OS |

## 6. Assessment of two standing beliefs

**"Maison Doclar is evolving into a collection of software products."** Partially supported. The evidence supports *two* products (the event platform, once multi-tenant-capable; Plavent) and *one* internal production vertical — not thirty products. Most repositories are modules of one platform that fragmented for deployment reasons, then began re-consolidating inside Aperture. The correct strategic frame is: **consolidate the platform, separate the verticals, productise deliberately.**

**"Clone Aperture, freeze it, build Event OS around the preserved knowledge."** Directionally right, mechanically wrong, and prematurely timed. Three objections, to be developed fully in the Repository Strategy deliverable:
1. Aperture is live infrastructure for Alakija (23 July 2026). Nothing about it can be frozen before that event closes out; it will also keep serving the Signature tier afterwards per your own Event OS decision — a *frozen* repo cannot serve a live tier. What you actually want is a **knowledge-preservation snapshot** (an annotated tag + extracted domain documentation), not an operational freeze.
2. Cloning into a new repository severs history, blame, and issue context — the cheapest knowledge in the repo. A protected tag/branch (`signature-baseline`) plus a written domain extraction (per the Knowledge Preservation Matrix you already commissioned) preserves more at lower cost.
3. The knowledge worth preserving is not evenly distributed: the seating engine, the import/dedup pipeline, the RSVP three-state model, the event-day snapshot/ledger design, and the reporting layer are high-value; much of the surrounding CRUD is not. Preservation should be targeted, not wholesale.

## 7. Risks surfaced by this assessment

1. **Un-versioned architecture.** The Event OS specifications exist only in chat history. First action of the program should be committing them (and this document series) to a dedicated `maison-doclar-architecture` repository — with your approval, since that is a new repo.
2. **Eight-way guest divergence** is the largest data-integrity threat in the estate; every satellite copy is a chance for Alakija-class data bugs to recur.
3. **Aperture's gravity.** Every week, more capability lands in Aperture because it is where the operator lives. Without a decided boundary, Event OS will be born already competing with a growing monolith.
4. **Single-operator bus factor** is partially mitigated by passports and Orion, but the three conflicting "source of truth" documents dilute that mitigation.

## 8. What this deliverable does *not* yet decide

Platform topology (modular monolith vs distributed), repository strategy, module boundaries, migration sequencing, and the fate of individual satellites are deliberately deferred to Deliverables 5–17, where each will be argued with trade-offs, alternatives, and risks per the engagement standard. Nothing in this document should be read as authorising change to any repository.

---
*Next deliverable on approval: **02 — Product Portfolio Assessment & Business Vertical Identification** (deep per-repo maturity review, beginning with the Aperture / Nexus / Nucleus triangle and the seating duplication).*
