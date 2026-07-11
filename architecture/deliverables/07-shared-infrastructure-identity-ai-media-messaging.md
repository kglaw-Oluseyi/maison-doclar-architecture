# Maison Doclar — Shared Infrastructure, Identity, AI Governance, Media & Messaging Realization
**Deliverable 07 of the Strategic Architecture Program**
Status: Draft for principal review · Date: 11 July 2026
Scope per ICAA: resolve four realization questions before roadmap and migration planning. All proposals cite the Preservation Matrix (KNW-001), the Negative Knowledge register (KNW-002), and assumptions (GOV-004). Advisory protocol CON-001, capability catalogue CAP-001, and MKC-001 are binding context.

---

## 1. Shared Infrastructure

**The boundary rule.** Shared infrastructure contains no domain concepts. The admission test: *would two unrelated domains use this identically, with zero domain vocabulary in its interface?* If a title, guest, seat, campaign, or vendor appears in the API, it is a domain package, not infrastructure. This single rule prevents the shared kernel from becoming a second monolith.

**What is shared (kernel packages in the Platform monorepo, versioned for external consumers):**
- **Identity client** (consuming the C17 service, §2) — sessions, roles, feature-access checks; no domain roles baked in (roles are data).
- **Storage adapter** — B2 behind an owned interface (Negative Knowledge #6 is law: no provider SDK calls outside the adapter). Presigned URL issuance, content-addressed keys, retention hooks for MKC lifecycle rules.
- **Delivery adapters** — Twilio/Termii/Resend behind owned interfaces; the adapters know channels, never campaigns (campaigns are C6 domain).
- **Background execution harness** — queue/worker runtime (BullMQ today, behind an adapter) with the worker-registry Principle donated by Assets: typed processors, registration, health, retry policy. Domain packages register processors; the harness knows none of their semantics.
- **Observability** — structured logging, trace/request-ID propagation (including the advisory request ID from CON-001), health/ready conventions (already estate-wide practice), and the usage-event emitter AI Governance requires (§3).
- **Secrets & config** — the Railway shared-variables pattern formalized: every deployable declares required config; startup fails loudly on absence (env-health, donated by Assets).
- **Scheduling** — one cron harness (the nucleus-nightly-cron lineage generalized); scheduled jobs are registered domain processors, the scheduler owns only time.

**What is explicitly not shared:** guest/household/engagement models (C2 domain), protocol content (MPS is a *constitutional* dependency, consumed as a versioned package — above infrastructure, not part of it), MXS artifacts (same), seating/rules/campaign/commission logic, and anything with an event ID in its core interface. Redis is shared runtime; its keyspaces are domain-owned and prefixed.

**Realization form.** Packages first, services only where a runtime must exist (identity, AI governance registry). Signature consumes kernel packages opportunistically under the divergence register; satellites are never retrofitted (they die by absorption per GOV-002 trajectories).

## 2. Identity — C17 resolved: **build the minimal internal provider**

**The decision and its decisive argument.** Build a minimal internal identity service in the Platform, rather than adopting a managed provider (Auth0/Clerk/WorkOS class). The decisive argument is **event-day sovereignty**: staff, usher, and host logins must succeed at a venue with degraded internet (A-003, Validated — the Hub exists because this happens). A managed IdP places a third-party availability dependency directly in the check-in path. The estate's own architecture already rejects that class of dependency — advice degrades to absence; login cannot.

**Supporting arguments.** The estate has already built and validated the hard parts as donors: session discipline and OTP flows (remote-view), host credentialing with per-feature access exceptions (Aperture's `HostCredential`/`HostFeatureAccessException` — a model no managed provider expresses natively), org tenancy (remote-view's `Organisation` — Reference implementation, A-006 makes tenancy a day-one schema fact), service identities with one-time reveal (Gateway knowledge asset #4, ESLA service tokens). Per-MAU pricing fits SaaS user bases, not an operator estate of dozens of staff and hundreds of episodic hosts/planners. Data residency stays under Maison control.

**Honest trade-offs.** Building identity means owning its security burden. Mitigations are scope discipline: phone/email + OTP and magic links as primary flows (matching how hosts and staff actually authenticate today — no password-reset surface to defend); HS256 JWT per estate standard; WebAuthn/passkeys as a later additive; **no federation/SSO in v1** — if an enterprise client ever demands OIDC/SAML, that is an additive module, and the decision records assumption **A-008: no enterprise SSO requirement within 24 months** (now registered). If A-008 expires, the build-vs-buy question legitimately reopens — that is what the assumption registry is for.

**Shape.** One small identity service (Execution-side, its own schema in the execution database), kernel client package, role-and-feature-access as data (generalizing Aperture's exception model), org/tenant model from day one, service-token issuance for machine identities. **Guests never enter this system** — guest identity remains event-scoped in C9, constitutionally. Migration: new services adopt at birth; the standing rule from Deliverable 03 activates now — *no new service ships its own User/Session model*; Living systems adopt opportunistically; Signature only if cheap, divergence-registered.

## 3. AI Governance — from charter (GOV-001) to executable infrastructure

**Form.** One small registry service (its own schema; group-owned, not Platform-owned) plus a kernel AI client. The service is **never in any request path** — enforcement is by policy, observation, and revocation, not interception.

**The registry owns:** model registry (approved models, versions, provider policies), prompt registry (versioned prompts with stable IDs, MKC sensitivity class, evaluation references; prompts are constitutional-adjacent artifacts — changes flow through review like code), call-site registration (every AI caller across the estate registers: system, purpose, models, prompts, budget), budget ledger, routing policy documents (fallback chains as *configuration consumed by clients*, not runtime proxying).

**Runtime enforcement without interception:** keys are issued per call site through the registry (one-time reveal, Gateway lineage) and are revocable — revocation is the enforcement teeth; the kernel AI client emits usage events asynchronously to observability (spend, latency, model, prompt ID, MKC class), reconciled against provider usage APIs; budget breaches alert and can trigger key rotation, never block a live event operation. Evaluation harness lives in the extraction workspace `/scripts` initially, maturing into registry-linked evaluation records; approval workflow is the constitutional PR flow.

**Realization decision:** build the registry fresh, seeded by the donated app-registry concept from `maison-doclar-intelligence` (DI-006); the old proxy is not rebuilt. Migration is registration: the ~15 existing call sites register profiles and swap to issued keys opportunistically; no rewiring (already ratified in Deliverable 03 §5).

## 4. Media & Messaging realization — without reintroducing coupling

**Messaging.** The Platform comms module (C6) lives on the Execution Plane and owns the protocol layer (MPS-consuming address forms, template governance, etiquette) and campaign/audience/suppression/consent state in the execution database. The **Channel Engine** is realized as workers in the Platform worker tier using Relay's donated architecture (dispatch, quality management, degradation, webhook reconciliation) over the kernel delivery adapters. It is one deployable tier of the Platform, not a separate product service — the "sellable engine" option from DI-004 remains a *future extraction along the package seam*, preserved but not pre-built. Inbound routing and disambiguation are comms-module domain logic. Nothing in messaging knows Studios exists.

**Media & commissions.** The **Commission** is an Execution Plane aggregate (an event orders media). The Platform publishes commissions over a contract (queue/webhook, carrying event-scoped context, MXS version pin, and delivery requirements); Studios systems consume, render, and return **artifact references** (B2 keys with provenance and MXS version), never rows. Studios never reads Platform data; the Platform never reaches into Studios pipelines. This is the personalised-video chain (Assets donor) elevated to the formal Platform⇄Studios boundary.

**The one deliberate exception, resolving the badges/assets ambiguity:** *credential rendering* (access cards, QR cards, badges needed for event operations) is C9 — an operational access concern, not creative production — and therefore runs in the **Platform worker tier** using the donated card-processor lineage. Creative media (clips, voice, personalised video, cinematography, music) is C8 and belongs to **Studios**. The dividing question: *does the artifact grant or verify access?* If yes, Platform; if it delights, Studios. Both render under MXS.

## 5. Consequences carried into the roadmap
The roadmap's earliest Platform work is now determined: shared kernel (storage/delivery/worker/observability/config packages) and the identity service are prerequisites for every module; the AI Governance registry can proceed independently and immediately (registration-only migration); the comms module inherits Relay's architecture with the protocol layer built against MPS v1.0 — making the MPS assembly workstream a hard dependency of comms, which is why it already runs in parallel.

---
*Ratifications requested: (a) the shared-infrastructure boundary rule and package set; (b) **C17: build the minimal internal identity provider**, with assumption A-008 registered; (c) AI Governance realization as registry + revocable keys + async usage telemetry, never intercepting; (d) the Commission contract as the sole Platform⇄Studios interface, with credential rendering assigned to the Platform worker tier under the access-vs-delight rule.*
