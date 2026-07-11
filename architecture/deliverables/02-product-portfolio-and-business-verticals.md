# Maison Doclar — Product Portfolio Assessment & Business Vertical Identification
**Deliverable 02 of the Strategic Architecture Program**
Status: Draft for principal review · Date: 11 July 2026
Baseline: Deliverable 01 (accepted). Correction incorporated: Aperture is **not** frozen — a knowledge baseline is documented; Aperture continues as the actively developed Signature edition; Aperture V2 / Event OS is a new lineage created from that baseline.

---

## 1. Executive Summary

Designed from first principles, Maison Doclar is **one house with two revenue engines, one product bet, and a shared nervous system**. The two revenue engines are luxury event delivery and media/creative production — both sell to the same client on the same event, but they are different businesses with different economics, different talent, and different software needs. The product bet is Plavent, a two-sided marketplace that shares almost nothing operationally with the house and should be governed at arm's length. The nervous system — corporate intelligence, identity, communications, infrastructure — should remain internal forever.

The thirty-two repositories are not thirty-two products. They resolve into **eleven business verticals**, of which four are core, four are supporting, and three are optionality (things that could become products or companies but must not be allowed to tax the core). The single most important strategic conclusion of this document: **the company's defensible asset is protocol-aware event intelligence** — the encoded knowledge of how Nigerian high-society events actually work (titles, households, tiers, entourage, aso-ebi, seating politics, WhatsApp-first communication). Every investment decision should be tested against whether it deepens or dilutes that asset.

The most difficult recommendations, stated plainly and argued below: Nucleus should be retired as a live service; the Intelligence service's AI-key-ownership mandate should be revoked rather than enforced; the four AI media apps should consolidate to two; Plavent must receive either a real commitment or a deliberate shelving — a half-attended marketplace is worse than none; and Nexus should be recognised as the *company* operating system, permanently internal, and never merged into Event OS.

## 2. Maison Doclar Business Map

Ignoring the repositories and designing the company as it should exist in 2036:

```
MAISON DOCLAR GROUP
│
├── MAISON DOCLAR EVENTS  ................. the house (core revenue)
│     ├── Signature Edition (Aperture lineage — current clients)
│     └── Premium Platform (Event OS lineage — next decade)
│
├── MAISON DOCLAR STUDIOS ................. media & creative production (second revenue engine)
│     ├── Audio (voice, music, DJ intelligence)
│     └── Visual (cinematography, personalised video, event media)
│
├── PLAVENT ............................... marketplace (arm's-length product company)
│
└── GROUP SERVICES ........................ internal forever
      ├── Corporate Intelligence (Orion / CLEO / Scribe)
      ├── Business Operations (Nexus: clients, vendors, finance, compliance)
      ├── Platform Engineering (infrastructure, shared kernel, standards)
      └── Identity & Communications (staff/host identity, protocol comms engine)
```

The hierarchy is a **business** hierarchy: each node has its own customer, its own revenue logic, and its own definition of quality. Software architecture (Deliverables 07–10) will be derived from this map, not the reverse.

## 3. Business Vertical Assessment

**V1 — Luxury Event Operations (core, mature).** The house itself. Customers: HNW Nigerian families, chieftaincy and traditional institutions, corporates, and diaspora clients in the UK/US/Canada. Revenue: planning and management fees per engagement, with premium add-ons. Strategic importance: existential — it funds everything and generates the proprietary data and protocol knowledge every other vertical feeds on. This vertical *is* the moat; software exists to scale it beyond one principal.

**V2 — Event Platform Technology (core, adolescent).** The Aperture → Event OS lineage: guest data acquisition, RSVP intelligence, seating optimisation, event-day runtime, host reporting. Customer today: Maison Doclar itself. Customer in year 5–10: potentially other premium planners (SaaS), but only after multi-tenancy, and only if it does not leak protocol knowledge to competitors of the house. Maturity: functionally deep, architecturally adolescent (89-model monolith, single-tenant). This is the **future flagship** and should absorb the majority of engineering investment for the next 24 months.

**V3 — Guest Experience Technology (core-adjacent, mature in parts).** PWA, guestbook, photo wall, lookbook, giftpack, microsites, embeds, live participation, remote attendance. Customers: the event's guests; the buyer is the host. Revenue: per-event premium add-ons — this is the natural **menu of upsells** for V1 and should be packaged and priced as such. Remote View deserves special note: it is the only asset in the estate with real multi-organisation tenancy already built, making it the most product-ready guest-experience asset.

**V4 — Communications & Protocol Intelligence (core, mature).** Relay plus Aperture's campaign machinery plus the title/protocol taxonomy. No external customer, ever — this is the crown-jewel internal capability. A WhatsApp-first, title-correct, suppression-aware, culturally fluent messaging engine is worth more inside the platform than sold; commoditising it arms competitors. Classification: **must never be sold** as a standalone product; it is sold only as a property of V1/V2.

**V5 — Media & Creative Production, "Studios" (supporting → second engine, early).** Voxlier, Creative Studio, Music, DJOS, Video Utility, and the production half of Assets. Customers: initially the house's own events; realistically also external clients (event media, personalised video, AI voice/music production for the Nigerian market). Revenue: production services per commission. Strategic importance: high optionality — it is the one vertical that can grow revenue without scaling the founder's event calendar. Maturity: fragmented (four overlapping AI apps). Requires consolidation before investment.

**V6 — Marketplace, Plavent (optionality, early).** A genuine two-sided product with a complete transaction stack. Customers: Nigeria's professional events economy — venues/vendors on the supply side, planners on the demand side. Revenue: take-rate on bookings, listing/subscription fees. Strategic importance: potentially the largest business on this map *and* the one most likely to die of neglect; marketplaces demand liquidity operations, trust/safety, payments compliance — full-time attention. Recommendation in §9.

**V7 — Identity & Access Technology (supporting, early).** Facegate (biometric re-entry), Badges, access cards, staff/host credentialing. Customer: event-day operations. Facegate is a plausible long-term niche product (biometric access for premium events), but only after V2 stabilises. Internally, the estate has a *guest* identity story (Gateway) and no staff/host identity story — that gap is addressed in the Identity Strategy deliverable.

**V8 — Corporate Intelligence (supporting, internal forever).** Orion, CLEO, Scribe, Intelligence. Customer: the principal. This vertical encodes how a one-person executive runs a thirty-service company — valuable, unsellable, and permanently internal. It should be cheap to run and boring to maintain.

**V9 — Business Operations (supporting, internal forever).** Nexus: clients, vendors, contracts, invoices, disbursements, compliance, accreditation. This is the **company OS**, not the event OS. It handles money and legal exposure; it must remain independent of guest-facing systems and conservative in its change velocity.

**V10 — Venue Intelligence (emerging, thin).** Venue templates in Aperture, Venue/VenueLesson in Nexus, fixtures in ESLA. Today a data model, not a business. Long-term: venue knowledge (layouts, acoustics, protocol constraints, vendor performance per venue) compounds into a real asset. No investment now; ensure Event OS's domain model leaves room for it.

**V11 — Platform Engineering & Infrastructure (supporting, internal forever).** Railway/Neon/B2 substrate, shared kernel, passports, engineering standards. Its customer is every other vertical. Deliverable 01 found this quietly successful; the strategy is to formalise it, not expand it.

## 4. Product Portfolio Assessment

Customer-facing products (someone outside the company touches them): the guest-experience surfaces (PWA, guestbook, photo wall, lookbook, giftpack, microsites, embeds, live, remote view), RSVP/forms surfaces from Relay/Aperture, Plavent, and host/planner portals in Aperture. Operator-only products: Aperture's operator surface, ESLA/Salle, md-attend, facegate consoles, md-events runtime, md-macs, Badges, the Studios tools, Nexus, Orion, CLEO, Scribe. Invisible infrastructure: Gateway, Intelligence, Shared, Assets pipelines, Video Utility.

Commercialisation ladder (earliest sellable first): **Remote View** (multi-tenant today) → **guest-experience add-on suite** (sold as packaged upsells with V1 events) → **Facegate** (niche access product) → **Event OS SaaS** (year 5+, deliberate decision required) → **Plavent** (parallel track, own governance). Never sold: Communications/Protocol engine (V4), Nexus, Corporate Intelligence, and the seating optimiser as a standalone (it is the differentiating heart of V2 — licensing it separately would commoditise the flagship's core advantage).

## 5. Repository Classification Matrix

| Repository | Current purpose | Long-term purpose | Lifecycle |
|---|---|---|---|
| maison-doclar-aperture | Live guest-ops platform | **Signature edition**; knowledge donor to Event OS | Keep · Strategic investment · Knowledge donor |
| *(new)* Event OS / Aperture V2 | — | Premium flagship platform | Future flagship |
| maison-event-nexus | Business back-office | Company OS (V9), permanently internal | Keep · standalone forever |
| nucleus | Canonical registry (superseded in practice) | Taxonomy/household knowledge donor | **Retire service** · Knowledge donor · Archive after extraction |
| maison-doclar-gateway | Identity routing between satellites | Migration-era seam; absorbed into Event OS integration module | Keep (maintenance-only) → Merge long-term |
| maison-doclar-relay | Multi-channel comms engine | Core of Event OS Communications module (V4) | Keep · Strategic investment · Extract into module |
| ESLA (+Salle) | Standalone seating/canvas | Seating knowledge donor (reconcile fork with Aperture first) | Knowledge donor → Archive after reconciliation |
| maison-doclar-events | Event-day runtime | Event-day module of Event OS | Merge into Event OS · Knowledge donor |
| md-attend | Floor ops (usher/concierge) | Event-day module surface | Merge into Event OS |
| facegate | Biometric re-entry | Access module; long-term product candidate | Keep standalone · Experimental product track |
| md-remote-view | Remote attendance concierge | First commercial product candidate (multi-tenant) | Keep standalone · Strategic investment |
| maison-doclar-pwa | Guest portal | Guest runtime of Event OS | Merge into Event OS |
| maison-doclar-guestbook / maisondoclar-photo-wall | Memory capture (two apps) | One guest-memory capability | **Merge with each other** → Event OS module |
| maison-doclar-lookbook / maison-doclar-giftpack | Premium guest content | Guest-experience add-on suite | Merge into Event OS guest-experience module |
| maison-doclar-microsite / maison-embeds | Event web presence | Publishing module | Merge into Event OS |
| maison-doclar-live | MC/room realtime | Event-day realtime capability | Extract socket/orchestration knowledge → merge; retire monorepo |
| maison-doclar-badges / maison-doclar-assets / md-video-utility | Credential + media pipelines (three apps) | One media/credential production pipeline in Studios+Event OS boundary | **Merge (3→1)** |
| voxlier / md-creative-studio | AI audio DAW / AI cinematography | Studios: Audio + Visual pillars | Keep as the two Studios platforms |
| maison-doclar-music / maisondoclar-djos | AI music tools (two apps) | One DJ/music intelligence product inside Studios-Audio | **Merge music → djos**; retire music repo |
| md-macs | Aso-ebi & merch coordination | Distinctive V1 capability; Event OS-adjacent module | Keep · Knowledge-rich |
| md-scribe / cleo / maison-doclar-orion | Docs / commercial / COO intelligence | Corporate Intelligence (consider CLEO→Orion merge at 20-engineer scale) | Keep · maintenance-only |
| maison-doclar-intelligence | AI proxy (mandate broken) | Revoke proxy mandate; keep as prompt/config registry or retire | **Retire mandate** · re-scope or archive |
| maison-doclar-shared | Thin shared kernel | Grow into the real shared kernel (or be superseded by Event OS packages) | Keep · fold forward |
| plavent | Marketplace | Independent product company (or deliberate shelf) | Standalone · own-company track |

## 6. Platform Classification

Applications (humans log in): Aperture, Event OS, Nexus, Studios tools, Plavent, Orion/CLEO/Scribe, guest surfaces. Modules (bounded contexts inside Event OS): Guest Data, Communications, Seating Intelligence, Attendance & Access, Event-Day Runtime, Guest Experience, Reporting/Analytics, Publishing. Libraries/shared kernel: design tokens, UI kit, types, auth client, storage client, protocol/title taxonomy (this taxonomy should become a versioned library — it is currently duplicated in enums across at least Relay, Nucleus, and Aperture). Infrastructure: Railway, Neon (schema-per-app), B2, Redis, Twilio/Termii/Resend, Anthropic. Services that remain services regardless of topology: Relay's queue workers, media transcode workers, socket server. Developer tooling: passports, Orion's repo-awareness, standards docs. Identity: staff/host/planner identity (to be built once), guest identity (event-scoped, per the standing principle). AI capability: direct provider access with shared config and centralized budget observability — replacing the Intelligence-proxy model.

## 7. Engineering Organisation Recommendations

**At 5 engineers** (first hires): 2 on Event OS core (guest data + seating), 1 on Communications (Relay/Event OS comms), 1 on guest-experience surfaces + event-day, 1 platform/infra + Studios support. The founder remains chief architect; Nexus, Corporate Intelligence, and Plavent receive no dedicated headcount — maintenance only. **At 20**: teams crystallise around the verticals — Event Platform (8: guest data, seating, event-day, reporting), Guest Experience (4), Communications & Identity (3), Studios (3), Platform Engineering (2); Plavent gets headcount only if the §9 decision is "invest," in which case it hires its own 3–5 as a separate unit with its own roadmap. **At 100**: the business map becomes the org chart — Events Platform division (~45), Studios (~15), Plavent as subsidiary (~20), Group Services (~20: platform engineering, corporate systems, security/compliance, data). Ownership boundaries follow module boundaries: one team owns a bounded context end to end, including its schema; no shared tables across team boundaries.

## 8. Future Company Structure

The §2 map, with governance annotations: Maison Doclar Events and Studios operate as divisions of one P&L (they sell to the same client and share the event calendar). Plavent is legally and operationally separable from day one — separate infrastructure account boundaries, separate data, no dependence on Group Services beyond arm's-length agreements — so that it can take outside investment or be shut down without touching the house. Group Services bills its cost to the divisions notionally, which keeps internal tools honest about their worth. Research/Future Products is not a unit yet; it is a budget line inside the Events Platform division (attendance prediction, venue intelligence) until something earns independence.

## 9. Strategic Investment Priorities

1. **Knowledge baseline before anything else.** The documented architectural baseline from which Aperture V2 is cut — decisions, invariants, algorithms, taxonomies — is the program's true deliverable. Cheap, urgent, and it de-risks everything downstream.
2. **Event OS core** (guest data acquisition, seating intelligence, communications) — the flagship bet.
3. **Guest-experience consolidation** — collapse eight satellite surfaces into the Event OS guest runtime; package as priced add-ons for V1.
4. **Studios consolidation (4 apps → 2)** before any new media features.
5. **Plavent: decide, don't drift.** Either commit a dedicated track (own roadmap, own success metrics, liquidity plan for one city and one category) or shelve it explicitly — archive the roadmap, keep the repo, revisit in 12 months. The current state — a well-modelled marketplace with no operational attention — is the only unacceptable option, because it silently consumes maintenance and mindshare while acquiring no liquidity.

## 10–12. Keep / Merge / Archive (consolidated register)

**Keep standalone:** Aperture (Signature), Event OS (new), Nexus, Relay (until module extraction), facegate, md-remote-view, md-macs, voxlier, md-creative-studio, orion, cleo, scribe, shared, plavent. **Merge:** music→djos; guestbook+photo-wall→one memory capability; badges+assets+video-utility→one production pipeline; pwa/lookbook/giftpack/microsite/embeds/live/events/attend→Event OS modules over the migration horizon; cleo→orion at scale. **Archive after knowledge extraction:** nucleus, ESLA (post seating-fork reconciliation), maison-doclar-live's monorepo shell, maison-doclar-music, and Intelligence if the re-scoped registry role isn't adopted. Nothing is deleted; archives remain readable donors. No archive action is taken without your explicit per-repo approval.

## 13. Long-Term Product Opportunities

Attendance Prediction Engine as an internal advantage first, commercial API much later if ever; Venue Intelligence compounding quietly; Remote View as first external SaaS; Facegate as niche access product; Event OS SaaS for premium planners as a deliberate year-5 board decision (weighing moat leakage against market size); Studios as an external production house — the fastest path to non-founder-constrained revenue.

## 14. Risks

Founder bus factor remains the dominant company risk — the baseline documentation and Orion mitigate but do not solve it. Platform gravity: Aperture will keep accruing capability while Event OS is built; without a decided module boundary and a "what lands where" rule, V2 launches already behind its ancestor. Marketplace distraction (§9). Multi-tenancy retrofit: if Event OS is built single-tenant "for now," the SaaS option in §13 becomes a rewrite — tenancy must be a day-one schema decision even if unused. Protocol-knowledge leakage through any premature commercialisation of V4. Concentration risk on Anthropic across fifteen call sites without central budget/failover observability.

## 15. Final Recommendations

Adopt the business map in §2 as the governing structure; derive all subsequent architecture from it top-down. Commission the knowledge baseline as the immediate next act of the program. Treat Aperture as a living Signature product and Event OS as a sibling lineage, planning for permanent divergence management rather than a migration endpoint. Consolidate Studios and the guest-experience estate on the schedule set by the Event OS roadmap, not before. Retire Nucleus's live role and Intelligence's proxy mandate. Force the Plavent decision within one quarter. And test every future proposal — repo, feature, or product — against one question: *does this deepen the protocol-intelligence moat, or dilute it?*

---
*Next deliverable on approval: **03 — Capability Map & Shared Capability Matrix** (bounded contexts, capability ownership, and the reconciliation plan for the three duplicated capability clusters: guest data, seating, communications).*
