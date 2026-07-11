# Maison Doclar — Platform & Company Architecture
**Deliverable 05 of the Strategic Architecture Program**
Status: Draft for principal review · Date: 11 July 2026
Baseline: Deliverables 01–04 accepted; all nine ICAA amendments incorporated. Planes renamed **Execution Plane** and **Intelligence Plane**. MPS ratified as a versioned constitutional asset. Nexus/Vendor-Intelligence contract ratified. **MKC** (Maison Knowledge Constitution) and **Decision Intelligence** introduced. Experience is both consumer and producer of knowledge. **Maison owns capabilities; Platform, Signature, Nexus, Studios, Plavent and future systems realize them.** Capabilities are peers — none may own another.

---

## 1. Evaluation criteria (fixed before any option was drawn)

Per instruction, topology is derived from principles, not repositories, and never from microservice fashion. Every option below is scored against five criteria, in priority order:

1. **Execution correctness** — a 700-guest investiture cannot experience a distributed-systems failure mode at check-in. Truth must be simple to reason about.
2. **Operational simplicity** — the realistic operator for the next 18 months is one principal plus Cursor plus a small future team. History is evidence: the Gen-2 federation of ~20 services was architecturally clean and operationally unaffordable; Gen-3's monolith gravity was the estate voting for simplicity.
3. **Knowledge preservation** — the topology must make the Intelligence Plane and MKC natural, not bolted on.
4. **Reversibility** — every boundary should be movable later without rewrite; decisions should be two-way doors wherever possible.
5. **Long-term evolution** — modules must be able to become services, and products must be able to compose capabilities, without re-architecture.

## 2. The realization landscape

Maison owns twenty-three capabilities (C1–C21 plus Event Intelligence and Vendor Intelligence, with Decision Intelligence reserved as C24). Systems realize them: **Maison Platform** realizes the event-execution and event-intelligence capabilities; **Maison Signature** realizes an earlier generation of several of the same capabilities for its clients, under managed divergence; **Nexus** realizes Revenue Operations, Vendor & Compliance records, and Planning records; **Studios** realizes Media Production; **Plavent** realizes marketplace capabilities entirely outside the family; **Group functions** realize MPS stewardship, MXS, MKC, AI Governance, Corporate & Decision Intelligence. No system owns a capability; realization is a contract that can, in principle, move — which is precisely why (per amendment 8) Studios could one day realize Media Production against the same capability definition with entirely different software.

## 3. Topology options for Maison Platform

**Option A — Pure modular monolith** *(the candidate blueprint: one repository, one Next.js application, one PostgreSQL, one Redis; packages as bounded contexts).*
Correctness: excellent — single transaction domain for execution truth. Simplicity: excellent — one deploy, one log stream, one on-call surface. Knowledge preservation: **weak as specified** — the blueprint predates the two-plane ratification; folding the Intelligence Plane into the same application and database blurs the advisory boundary the moment a developer can join a dossier to an engagement, and it forces MKC's stricter access, retention, and sensitivity rules onto the same database role model as execution. Reversibility: good for execution modules; poor for intelligence once entangled. Evolution: intelligence workloads (graph traversal, pipeline batch, model inference, simulation) scale and fail differently from request/response execution; co-hosting couples their release cadence and their outages.

**Option B — Federated services** *(the Gen-2 pattern: each context its own deployed service).*
Correctness: weakest — check-in truth spread across network boundaries; the estate has already produced cross-service contamination bugs. Simplicity: disqualifying at current scale; this is the pattern the company already abandoned by revealed preference. Knowledge preservation: neutral. Reversibility: poor (merging services is harder than splitting modules). Evolution: nominally flexible, practically frozen by operational load. **Rejected on evidence, not fashion.**

**Option C — Two-plane hybrid: modular-monolith Execution core + separately deployed Intelligence Plane + shared worker tier.**
One repository (a monorepo of packages) but **two runtime systems**: the Execution application (modular monolith — one Next.js app, one execution PostgreSQL, Redis, exactly as the candidate blueprint prescribes, minus intelligence); and the Intelligence system (its own service(s) and its own database, initially one small Hono-style service plus pipeline workers), speaking to Execution only through the ratified advisory contract and the declared pipelines. Correctness: equal to A for execution — the transaction domain stays whole. Simplicity: one repo, two deployables plus workers — marginal overhead over A, far below B, and within proven operational capacity (the estate already runs Relay's worker pattern successfully). Knowledge preservation: **strongest** — the plane boundary is physical, so MKC's governance (sensitivity classes, narrower access, retention, audit) is enforced by infrastructure rather than discipline; the advisory contract is a real network interface that cannot be quietly bypassed with a SQL join. Reversibility: strongest — intelligence can later split into per-graph services along package seams; equally, if the second deployable proves burdensome it can collapse back into the monolith without schema surgery because the databases were never joined. Evolution: intelligence scales, versions, and fails independently; AI/agent workloads land on the correct side of the boundary by construction.

**Option D — Event-driven distributed platform** *(event bus as backbone, contexts as consumers).*
Correctness: event-sourcing gives auditability but makes "what is true right now at the door" a projection problem — the wrong trade for event-day operations. Simplicity: the worst of the four for a small team. Preservation: attractive in theory (events as knowledge), but MKC needs curated knowledge with provenance and approval, not a raw event firehose. Reversibility: low. **Rejected for now**; its one genuinely valuable element — an append-only fact log feeding Intelligence pipelines — is adopted *inside* Option C as the pipeline transport, without making it the platform's backbone.

## 4. Recommendation

**Option C.** The candidate Event OS blueprint is upheld in its essentials — modular monolith, one repository, packages as bounded contexts, business domains over deployment boundaries — and **amended in one clause**: not one application and one database, but one *execution* application and database, with the Intelligence Plane as a separately deployed, separately stored sibling in the same monorepo. The amendment exists because the blueprint was drawn before the two-plane architecture was ratified; the plane boundary is the one place where a deployment boundary *is* the architecture. Storage note: the Intelligence store begins as PostgreSQL with graph-shaped relational modelling (edges as first-class rows with confidence and provenance columns per MKC); a dedicated graph database is a later, evidence-driven decision, not a day-one purchase.

```
                    MAISON DOCLAR (owns capabilities)
                    MPS · MXS · MKC · AI Governance      ← group constitutional assets
                                │ versioned artifacts
     ┌──────────────────────────┼──────────────────────────────┐
     │        MAISON PLATFORM (one monorepo, two runtimes)     │
     │                                                         │
     │  EXECUTION PLANE                 INTELLIGENCE PLANE     │
     │  one Next.js app, one Postgres   advisory service +     │
     │  modules: guest-data · seating   pipeline workers,      │
     │  comms · access · event-day      own Postgres           │
     │  guest-experience · reporting    graphs: guest · event  │
     │        │            ▲            vendor · venue         │
     │  declared fact      │ advice: recommendation,           │
     │  pipelines ────────►│ confidence, reasoning,            │
     │  (append-only)      │ MPS version, provenance           │
     │                     └── Execution decides; owns truth   │
     └─────────────────────────────────────────────────────────┘
   Signature (managed divergence) · Nexus (facts by contract)
   Studios (commissions) · Plavent (fully outside)
```

## 5. The learning loop (drawn, per amendment 7)

Experience → Observations → Knowledge → Intelligence → Capability → Experience. Concretely: a check-in bottleneck at hour one (Experience) is captured as a structured lesson by the runtime's capture obligation (Observation); MKC governs its admission — provenance, confidence, approval (Knowledge); pipelines fold it into the Venue and Event graphs (Intelligence); the advisory service surfaces "open the second lane at 6:40 for this venue-population shape" at the next event's planning (Capability); the next guest never queues (Experience). The loop closes only if every stage exists — which is why capture is a workflow obligation, why MKC exists, and why advice carries reasoning a planner will actually trust.

## 6. Maison Knowledge Constitution (outline; full charter as its own deliverable)

MKC governs every knowledge asset on the Intelligence Plane and the constitutional assets above it. Its articles, in outline: **Provenance** (every fact names its source, capture context, and MPS/MXS version); **Confidence** (scored, never binary; contradictions coexist with weights rather than overwriting); **Authorship & approval** (who may create, who may verify, AI-generated marked distinctly from human-verified until reviewed); **Sensitivity** (field-level classes; special-category material — affiliations, risk, health/accessibility — under the narrowest access, with legal basis recorded; obligations verified against current NDPA/GDPR advice, not assumed); **Lifecycle** (expiration, retention, deletion, legal hold); **Contradiction handling** (newer does not mean truer; source trust weighting); **Human override** (always available, always recorded — an override is itself a Decision Intelligence fact); **Audit** (every read of sensitive knowledge is loggable). The MKC is versioned like MPS; pipelines and advisory services declare which MKC version they comply with.

## 7. Decision Intelligence (C24, reserved and seeded now)

Every consequential decision — a guest moved, a vendor chosen, a protocol overridden, an AI recommendation accepted or rejected, an architecture ratified — is recorded with rationale, alternatives, the advice it accepted or ignored, and later, outcome. Realization begins embarrassingly simple: a decision log with structure, seeded immediately with **this program's own decision register** (every ratification and amendment from Deliverables 01–05 onward), so the capability is born already useful. On the Platform, decision capture attaches to the surfaces where decisions happen — the seating review cycle's accept/reject of allocator suggestions is the first and richest source, and it requires no new UI: the action stack already records the *what*; Decision Intelligence adds the *why* and the advice context.

## 8. Company architecture around the Platform

Signature continues as its own deployed estate, pinning MPS/MXS versions where adopted and recording divergence quarterly. Nexus exchanges vendor and engagement facts with the Intelligence Plane over the ratified contract — a declared pipeline, not an API entanglement. Studios interacts through media commissions carried as execution-plane records. Plavent touches nothing but public contracts and the brand-permitted MXS subset. Gateway's long-term role shrinks to the migration seam already assigned; the advisory contract and declared pipelines are the Platform's real interfaces. AI Governance registers every model call site on both planes but sits in no request path.

## 9. Risks and open questions

The two-deployable overhead is real and accepted knowingly; the mitigation is that collapse back to one deployable is an explicitly preserved two-way door (§3C). The advisory contract must be designed so that event-day surfaces never *depend* on Intelligence availability — advice degrades to absence, never to failure. The Intelligence store's graph-in-Postgres approach will meet limits if graphs grow past memory-comfortable traversal; the seam for a graph engine is preserved behind the advisory service. And one open question is flagged for you rather than decided: whether Signature should ever consume the Intelligence Plane's advice (tempting — it improves live client outcomes now; risky — it couples the lineages you chose to let diverge). Deliverable 06 will carry both options with trade-offs.

---
*Next deliverable on approval: **06 — Repository & Shared Infrastructure Strategy** (monorepo design for the Platform, the architecture/knowledge repository, Signature's repo posture, shared kernel packaging of MPS/MXS/MKC artifacts, and the deferred-merge register). Ratification requested on: (a) Option C as Platform topology, (b) MKC outline as the basis for its full charter, (c) seeding Decision Intelligence with this program's decision register.*
