# GOV-009 — Handover Inventory: The Definitive Estate Map
**Prepared by the programme's architecture steward at the Claude→Cursor implementation handover · 12 July 2026**
All heads live-verified via GitHub API at preparation time. Nothing summarised; unverifiable items marked ◻ per GOV-008 discipline.

---

## PART 1 — Repositories created by this programme (complete detail)

### 1. maison-doclar-architecture — THE CONSTITUTION
Purpose: the constitutional repository — MPS/MXS/MKC charters, capability catalogue (C1–C25 + reserved), decision register (DI-001–DI-054), assumption registry (A-001–A-008), specifications (STD-001–STD-012 living, STD-003/004/005 frozen), ARCH-001–ARCH-005, AMM-001, governance (GOV-001–GOV-008), contracts (CON-001 v1.2), knowledge registers (fossils, negative knowledge, human knowledge), as-delivered deliverables 01–07.
Status: **Active (governance cadence — slowing by design, DI-045 growth signal)** · Class: **Constitutional** · Branch: main · Commits by me: 29 · Last commit: `bdcb2a40111b` (GOV-008 + DI-054).
Relationship: governs everything; precedence root (DI-020); Decision Intelligence seed corpus; every implementation artifact must cite it by stable ID.

### 2. maison-doclar-knowledge-extraction — THE HISTORICAL ARCHIVE + PIPELINE
Purpose: raw→extracted→review→promoted pipeline; Historical Design Corpus Batch 1 (11 documents, verbatim, provenance-manifested); MPS v1.0 four-source extraction; seating fork divergence report; extraction outcome records.
Status: **Active until corpus extraction completes, then Reference** · Class: **Historical** · Branch: main · Commits by me: 12 of 13 (one is the principal's Batch-1 intake `9a645cc9`) · Last: `f22d55448d34`.
Relationship: feeds the constitution through governed promotion (DI-024); MPS v1.0 draft sits in `/review` awaiting founder disposition.

### 3. maison-platform — THE EXECUTIVE INTELLIGENCE (primary implementation repository)
Purpose: the realization monorepo — Execution Plane service, seven substrate packages (constitution, contracts, mkc, decisions, identity, observability, ai-runtime) + mission-runtime + guest-domain + persistence; migrations (four schemas); four vertical slices, 16 tests; Dockerfile + railway.toml.
Status: **Active — the centre of all future activity** · Class: **Implementation** · Branch: main · Commits by me: 7 pushed + **1 local unpushed** (CI workflow — blocked on PAT Workflows scope) · Last pushed: `a6240006af89`.
Relationship: realizes the constitution; every service imports the substrate packages; the organism's body and first memory.

## PART 2 — Constitutional estate
`maison-doclar-architecture` (sole constitutional repository). Constitutional *content* also exists as promoted frozen specs inside it (STD-003/004/005) and as the MPS v1.0 draft in the extraction repo's `/review` (constitutional-pending, not yet promoted).

## PART 3 — Implementation estate (executable code)
**Programme-created:** `maison-platform` (the only implementation repo this programme created).
**Pre-existing Living systems (28, per GOV-002, all executable, all outside the Platform):** maison-doclar-aperture (Signature flagship — pushed 2026-07-12, actively developed), maison-doclar-relay, maison-event-nexus, maison-doclar-events, md-attend, facegate, md-remote-view, maison-doclar-pwa, maison-doclar-guestbook, maisondoclar-photo-wall, maison-doclar-lookbook, maison-doclar-giftpack, maison-doclar-microsite, maison-embeds, maison-doclar-live, maison-doclar-badges, maison-doclar-assets, md-video-utility, voxlier, md-creative-studio, maisondoclar-djos, md-macs, md-scribe, cleo, maison-doclar-orion, maison-doclar-shared, maison-doclar-gateway, plavent.

## PART 4 — Historical archive
`maison-doclar-knowledge-extraction` (dedicated). Knowledge-donor repos pending read-only transition on named instruction: **nucleus, ESLA, maison-doclar-music, maison-doclar-intelligence** (donated knowledge banked; four-step transition pipeline ratified, never executed by default).

## PART 5 — Operational systems informing the Platform
All 28 Living systems above remain production-operational and outside the Platform. The knowledge lineages that matter to implementation: Aperture → STD-001/002 acceptance corpora + Signature divergence register; Relay → STD-006 engine + MPS title corpus; md-events/facegate/live → STD-007; remote-view → tenancy reference; Assets → STD-009 commission chain; Nexus → simulation/Venari (Intelligence Plane donors); Orion → C24 decision-log donor.

## PART 6 — Current implementation focus
**`maison-platform`, singularly.** Next milestone inside it: Railway deployment + Neon cutover, then API authentication, then Situation synthesis (DI-053 order).

## PART 7 — Outstanding work (complete, per repository)

**maison-platform**
- *Blockers:* CI workflow push (PAT needs **Workflows: Read and write** — one local commit waiting); Railway project creation (principal action); Neon `DATABASE_URL` (principal supplies; boot migrates automatically); ◻ Neon project identity unconfirmed.
- *Unfinished:* **HTTP endpoints have no authentication** — must land before any public deployment exposure (the service currently trusts `actorId` in the request body; C17 identity service is the designed answer, a minimal token check is the interim); Situation synthesis (DI-053 step 3); planner-consuming-situations (step 5); comms/seating/event-day departments; C16 registry service; MPS/MXS as real versioned packages (currently interface + draft catalog).
- *Technical debt (honest):* guest-domain has dual in-memory/Pg paths not yet unified behind one repository interface; `server.ts` bridges durable↔memory runtime per-wave (scaffold — the runtime should be natively durable); executor registry is a hardcoded `noop`; Dockerfile runs `tsx` on source rather than a compiled artifact; `EchoModel` is the only Model implementation (by design, but the Claude adapter + GOV-001 key issuance is unbuilt); `allowBuilds` workaround in pnpm-workspace.yaml.
- *Next milestone:* deployed `/health` green on Railway against Neon.

**maison-doclar-architecture**
- *Blockers on humans:* MPS v1.0 founder disposition (gates STD-001/002/006 Ready-for-Build — the oldest open item); CTR-I1 ruling (gates Phase C fact flow); ICAA/principal dispositions on STD-002/006/007/008/009/010/011/012 and ARCH-003 v1.2; MXS constitution authoring (design authority = founder).
- *Unfinished:* Architecture Traceability Matrix (DI-039 — committed as the final pre-implementation artifact; not yet produced); Architecture Freeze v1.1 (marks spec completion; after dispositions); Decision Intelligence derived views (`/decisions/derived` is a stub).
- *Debt:* none structural; growth-signal watch applies.
- *Next milestone:* founder session → MPS v1.0 promotion → readiness tracker flips three rows.

**maison-doclar-knowledge-extraction**
- *Unfinished:* extraction passes for 4 of 11 Batch-1 documents (event-os-architecture-spec, platform-portfolio-assessment, frontend-experience-surface-specification, event-data-acquisition-canonical-spec — expected outcomes: largely Superseded-with-confirmation, MXS seed, and STD-002 CommunicationContact shape (STD-006 O-2)); founder review queue empty pending session; future corpus batches if more historical material exists.
- *Debt:* none.
- *Next milestone:* complete Batch-1 passes; promote MPS v1.0 on disposition.

**Estate-wide outstanding (owned by principal):** admin PAT rotation post-stabilisation (carries estate-wide admin + all secrets exposure history in this conversation); read-only transitions for the four donor repos when you name them; Plavent invest-or-shelve (DI-003 clock); Alakija close-out (DI-018 wall lifts after 23 July).

## PART 8 — Six-month commit expectations
**High activity:** `maison-platform` (daily — the centre). **Moderate then declining:** `maison-doclar-knowledge-extraction` (until Batch-1 completes + founder dispositions land, then near-zero). **Low and event-driven:** `maison-doclar-architecture` (dispositions, AMM promotions, phase-gate entries, genuine amendments only — if it outpaces maison-platform, invoke the DI-045 growth signal). **Continuing on its own track (not this programme):** `maison-doclar-aperture` (Signature, divergence-registered quarterly) and the Living estate under normal operations. **Stable/read-only trajectory:** nucleus, ESLA, music, intelligence (on named instruction), gateway (Legacy trajectory), all as-delivered historical content.

## PART 9 — Final recommendation: the daily map
**Open daily:** `maison-platform` (implement), `maison-doclar-architecture/decisions/decision-register.md` + `governance/implementation-readiness.md` (steer), and — until its passes finish — `maison-doclar-knowledge-extraction`.
**Consult, don't edit:** specifications (STD-*), ARCH-005 (the lens), frozen specs, corpus.
**Effectively read-only now (formal transition on your instruction):** nucleus, ESLA, maison-doclar-music, maison-doclar-intelligence, deliverables 01–07, the frozen specifications, and the Historical Design Corpus.
**The one-question filter for every Cursor session (DI-053, as amended by DI-056):** *does this change increase the Executive Intelligence's ability to **safely** operate the Maison Doclar estate while remaining constitutionally governed?* — "safely" carrying reliability, observability, rollback, security, and governance, not just capability.
