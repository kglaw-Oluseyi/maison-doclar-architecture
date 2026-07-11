# Maison Doclar — Knowledge Preservation Matrix
**Deliverable 06A of the Strategic Architecture Program (mandatory validation phase)**
Status: Draft for principal review · Date: 11 July 2026
Rule in force: no architectural decision may cite this program unless it is traceable to evidence in this matrix. Evidence citations are file paths, model names, and migration names observed in the cloned repositories on 11 July 2026. Verdicts here concern **knowledge** (preserve exactly / evolve / discard); repository *fates* are reserved for Deliverable 06B. There is no "merge" category anywhere in this program.

Legend — **PE**: preserve exactly (the knowledge is correct and hard-won; re-derivation would be loss). **EV**: evolve (the knowledge is real but its current form should be generalised or corrected). **DI**: discard (implementation detail with no knowledge value).

---

## Tier 1 — Dense knowledge carriers

### 1. maison-doclar-aperture — the deepest knowledge asset in the estate
**Knowledge.** The operational theory of running a high-protocol guest list end to end, hardened on a live 700+ guest investiture. Domain inventory readable directly from `lib/`: `guests` (import engine), `rsvp`, `seating`, `messaging`, `campaigns`, `checkin`, `event-day`, `cards`, `readiness`, `accessibility`, `guest-trust`, `journey`, `focus-check`, `reports`, `device`, `host`, `portal`, `audit`.
**Algorithms (PE).** Seating allocator suite — `lib/seating/allocator-engine.service.ts`, `allocator-run.service.ts`, `allocator-suggestion.service.ts`, `allocator-rule-order.ts`, `allocator-audience-mode.ts`, each with co-located tests; migration lineage shows three generations of hardening (`prompt16_seating_allocator` → `prompt58_allocator_rebuild` → `allocator_run_history`, `seating_action_stack`, `table_excluded_from_allocator`). Phone normalisation and audience dedup — `lib/guests/phone-normalize.ts`, `lib/messaging/phone-audience-dedup.ts` (the within-event dedup key made real). Import engine with column mapping, preview, exact-duplicate merge, duplicate review, auto-sync worker, and anomaly detection (`import_sync_anomaly`, `guest_list_import_lock` migrations). Headcount reconciliation — `lib/rsvp/reconcile-headcount.ts`, `headcount-figures.ts`, `headcount-drill-down.ts` (the corrected expected-attendance mathematics). Go-live readiness computation — `lib/readiness/compute.ts`, `go-live.ts`, `venue-capacity.test.ts`.
**Workflows (PE).** RSVP token lifecycle including rotation and resend-alternate (`token-lifecycle.ts`, `rotate-resend`, `resend-alternate.ts`); regrets-only flows (`regrets-only.test.ts`); the three-state RSVP model with the deliberate retirement of "no response" as a state (`retire_rsvp_no_response` migration — a decision, recorded in schema history); allocator review with undo/redo action stack and revertible suggestions; host review → change request → operator resolution; import → preview → sync-review-batch → merge; Focus Check monitoring as a separately deployable watchdog (`Dockerfile.focus-check`, `railway.focus-check.toml`).
**Invariants (PE).** Event-scoping enforced in code (`verify-allocator-suggestion-event-scope.ts` — a script that exists because cross-event contamination happened and was killed); import lock during allocation push; consent gating on print dispatch (`print_consent_report_dispatch`).
**Reports (PE as templates, EV as engine).** Event reports with templates and generation audit (`EventReport`, `ReportTemplate`, `EventReportGenerationAudit`); merged messaging report (`lib/messaging/merged-report.ts`); RSVP insights and visualisations; care-notes PDF for accessibility (`lib/accessibility/care-notes-pdf.ts`) — host-facing dignity encoded as software.
**UX patterns (EV).** Operator/Host/Planner surface separation with feature-access exceptions (`HostCredential`, `HostFeatureAccessException`); drag-and-drop seating with narrated welcome; import-sync review UI.
**Protocol knowledge (PE).** Guest-trust checklist (`lib/guest-trust/`), relationship tag presets, tier handling embedded across seating rules.
**Discard (DI).** App-router plumbing, theme presets, clipboard/util shims — commodity.

### 2. maison-doclar-relay — communications engine knowledge
**Knowledge.** How to run protocol-correct multi-channel campaigns at Nigerian-event scale. The Drizzle schema's `title` enum is the most complete encoding of MPS address forms anywhere (royal: HRH/Oba/Obi/Igwe/Emir/Tor/Ooni; chieftaincy: Chief/Otunba/Alhaji/Alhaja; military ranks; clerical: Pst/Bp/Abp/Sheikh/Imam; political: Senator/Hon/Rt_Hon/Ambassador) — **PE, and the seed corpus for the MPS versioned library**. Suppression taxonomy (`suppression_reason`: opted_out_all/channel, admin_suppressed, duplicate, invalid_contact, deceased, do_not_contact) — PE; "deceased" as a first-class suppression reason is protocol knowledge, not plumbing.
**Algorithms (PE/EV).** Eight AI micro-capabilities in `src/modules/ai/`: send-time recommendation, failure-pattern analysis, message personalisation, cleaning, guest history, audience anomaly detection, template effectiveness, inbound intent — each a distilled operational lesson; preserve the *logic and prompts*, evolve the delivery. Channel intelligence: `whatsapp.quality.ts` (template quality/ban-risk handling), `asset-degrade.ts` (graceful media degradation per channel), `dispatch.service.ts` orchestration, opt-out service.
**Workflows (PE).** Campaign → audience → personalised asset render (`asset.render.service.ts`, `overlay.service.ts`) → queued dispatch (BullMQ) → delivery webhook reconciliation → RSVP writeback to Gateway (`gateway.writeback.ts`). Form system with question library and template seeds (`db/seeds/`).
**Discard.** Hono scaffolding, generic auth.

### 3. maison-event-nexus — business-operations knowledge
**Knowledge.** How the *company* runs an event as a project: full lifecycle from client inquiry to post-event report across 63 models.
**Algorithms (PE).** Tabletop simulation engine (`simulation/engine.test.ts`, `SimulationRun`, `TabletopItem`, `DeviationLog`) — rehearsing an event before it happens; timeline benchmarking (`timeline-benchmark.ts`, `TimelineBenchmark`, `FinancialBenchmark`) — expected-vs-actual drift knowledge; **Venari** AI vendor-assessment agent (`services/venari/`: build-input, prompt-builder, agent, schema) — structured vendor evaluation, the strongest existing seed for Vendor Intelligence.
**Workflows (PE).** Change-order discipline with cost and timeline impact; disbursement lifecycle; compliance registers per jurisdiction; accreditation/learning (LearnProgress, AssessmentToken); event archive and signing tokens.
**Invariants (PE).** Money is decimal(15,2) NGN-defaulted with exchange-rate logging; every financial mutation traceable. **Caution flag:** any compliance figures encoded in seed data must be verified against current sources before reuse — regulatory content ages.
**Discard.** Expo app scaffolding, generic dashboard plumbing.

### 4. ESLA (+Salle) — the seating fork's other half
**Knowledge.** The standalone seating lineage Aperture forked from, **plus capabilities Aperture never absorbed**: the Salle conversational design loop (`SalleProposal`, `SalleConversationMessage`, `apps/salle/.../assistant/route.ts`, `conversation/route.ts`) — AI-assisted floor design as dialogue; live canvas operations (table convert/rename/note endpoints, SSE streaming, online-status handling); `GuestIdentityMapping` (its own identity-bridging attempt — evidence for the identity story); service tokens and Salle credentials (machine-to-machine access pattern).
**Verdict.** The fork-reconciliation audit ratified in Deliverable 03 §5 is confirmed as necessary by this evidence: Aperture's allocator is ahead on rules/undo/audience-mode; ESLA is ahead on conversational design and canvas liveness. Both PE; the Platform seating module inherits the union. Salle's HTML manuals (`SALLE_HOST_PLANNER_MANUAL.html`, `ESLA_ADMIN_MANUAL.html`) are knowledge artifacts — PE into the baseline.

### 5. maison-doclar-events (md-events) — event-day distributed truth
**Knowledge.** How event-day truth survives bad networks. Snapshot pipeline: ingestion → publication → delivery (`modules/snapshot/*.service.ts`) with device acknowledgement (Aperture's `DeviceSnapshotAck` corroborates the pattern from the consuming side). **The Hub** (`hub-provision.service.ts`, `hub-bridge.service.ts`, `hub-mode.ts`): an on-venue provisioning/bridge layer — the estate's answer to "the venue's internet died." Fanout services (seating, check-in, card delivery) — how truth propagates to device fleets. Device surfaces: Director/Concierge/Scanner/Kiosk role separation.
**Verdict.** PE wholesale as the Event-Day Runtime module's design ancestor; this repo is the strongest single donor outside Aperture and Relay.

### 6. nucleus — taxonomy and registry knowledge
**Knowledge.** The ratified guest taxonomy in schema form: Household/GuestGroup/six group types, ChildProfile with event-level audience gating, Interpreter, Engagement/EngagementPersonnel (vendor personnel at events), five VIP tiers, CoupleSplitReview and DuplicateGuestFlag (dedup edge cases encoded as models), GuestRevision/PendingGuestChange (change-review discipline), EventLedgerEntry. `lib/readiness/guest-health.ts` (guest-health RAG), `lib/canonical-ids.ts` (identity issuance), access-grants with hashed resolution.
**Verdict.** PE the taxonomy, the guest-health rubric, the pending-change review pattern, and the couple-split/duplicate-flag edge-case knowledge — these are the normative inputs to the Platform guest-data module. The service itself is deprecated read-only legacy per ratified decision; nothing operational is preserved.

### 7. facegate — biometric access knowledge
**Knowledge.** Consent-first biometrics for luxury events: **`purge-biometrics` as a first-class admin endpoint** (privacy invariant — biometric data is deletable by design, PE); EDR connection to the event-day runtime (`edr-connection/route.ts`) — access decisions flow to the runtime, not the reverse; guest sync scoped per event slug; attempt/metrics auditing. 14 models of enrolment/verification lifecycle.
**Verdict.** PE the consent/purge posture, the event-scoped credential lifecycle, and the EDR integration contract. EV the matching pipeline as vendors change.

### 8. md-remote-view — remote attendance knowledge (and the estate's only true multi-tenancy)
**Knowledge.** `Organisation`/`OrganisationMember`/`OperatorInvitation` — a working multi-org model, PE as the tenancy reference for the Platform's day-one tenancy decision (Deliverable 02 §14). Consent records, OTP attempts, token/session discipline for high-trust remote guests; audio request/session flows (remote guests speaking into the room); notification queue with delivery logs; ticker messaging; `DomainEvent` and `EventMetricsSnapshot` (an event-sourcing-lite pattern, corroborating Option C's append-only pipeline transport).
**Verdict.** PE tenancy, consent, and audio-participation workflows. This repo also remains a living product candidate — but that is 06B's ruling.

### 9. md-macs — attire and merchandise coordination
**Knowledge.** The aso-ebi domain: collection types (`lib/types/collection.ts`), order status pipeline (`lib/orders/status-pipeline.ts` — the lifecycle of a fabric order from commitment to delivery), supplier manifests (`lib/supplier/manifest-utils.ts`, `lib/manifest/generate.ts`), partner-vs-guest auth split (two-sided coordination). This is protocol-adjacent commerce no off-the-shelf product models.
**Verdict.** PE the status pipeline, manifest logic, and partner/guest role split.

### 10. maison-doclar-assets — media production pipeline knowledge
**Knowledge.** Worker-registry pattern with typed processors (`lib/processors/clip-processor.ts`, `card-processor.ts`, `voice-processor.ts`); personalised-video pipeline with job service and Relay notification hook (`lib/personalised-video/`) — the render→notify→deliver chain; B2 integration hardened; nucleus client (cross-service identity consumption pattern).
**Verdict.** PE the processor/worker architecture and the personalised-video chain as the Studios⇄Platform commission pattern's ancestor.

## Tier 2 — Solid knowledge, narrower scope

### 11. maison-doclar-pwa
Guest-portal knowledge: ICS calendar generation, push notifications, host-auth for guest surfaces, CSV self-service. PE the guest-facing auth/session posture and push/ICS flows; EV surfaces into the Platform guest runtime. 17 docs indicate mature operational runbooks — PE into baseline.

### 12. maison-doclar-guestbook & maisondoclar-photo-wall
One capability in two repos (memory capture). Guestbook: capsule delivery (`lib/capsule/deliver.ts` — post-event memory delivery), tenant resolution, QR delivery, gateway webhook verify pattern, four-way auth (admin/host/guest/api-key). Photo-wall: live moderation and display flows (14 docs). PE the capsule-delivery concept, moderation workflow, and QR patterns; the unification happens in Platform, not by repo surgery.

### 13. maison-doclar-lookbook & maison-doclar-giftpack
Premium content experiences: chaptered AI-composed lookbooks; giftpack's 24-model digital-souvenir lifecycle. PE the content-composition workflows and gifting lifecycle; EV rendering into MXS-governed Platform surfaces.

### 14. maison-doclar-microsite & maison-embeds
Publishing knowledge: commission→review→publish→export pipeline for luxury microsites (12 docs); embeddable widget platform with Studio operator UI. PE the publish/export pipeline and widget embedding contract.

### 15. maison-doclar-live
Realtime participation: five-service topology (admin/guest/mc/room/socket) — MC control and room display orchestration knowledge, Socket.IO patterns. PE the MC/room show-control model (feeds Event-Day Runtime's ShowControl aggregate); DI the Turborepo shell.

### 16. md-attend
Floor operations: usher/concierge unified surface, gateway-registered (passport carries Gateway System ID). PE floor-assignment workflows; slim but real.

### 17. maison-doclar-badges
Identity design atelier: brief→consult→generate→review workflow for credential design. PE the design-workflow pattern (an MXS-governed production loop); EV rendering tech.

### 18. md-video-utility
WhatsApp video compression with deliverability verification — a distilled lesson (compress until WhatsApp accepts, then verify). PE the compression/verification recipe as a Studios utility function; the rest is thin.

### 19. voxlier & md-creative-studio & maison-doclar-music & maisondoclar-djos
Studios knowledge cluster. Voxlier: TTS/voice-clone timeline DAW with FFmpeg export. Creative Studio: AI-directed scene production (11 docs; external provider integrations — HeyGen/Kling/ImagineArt per standing credentials note). Music: prompt libraries for Nigerian event music (PE the prompt/genre knowledge; the 2.2K-LOC app is thin). DJOS: 22-model AI music cockpit with Socket.IO live operation — the most operationally complete Studios asset. PE across the cluster: prompt libraries, cultural music taxonomies, timeline/export pipelines, live-operation patterns. Consolidation shape is 06B/roadmap business.

### 20. md-scribe, cleo, maison-doclar-orion
Corporate knowledge. Scribe: client intake→document generation→Gateway guest lookup; six canonical document templates lineage. Orion: ecosystem self-awareness — repo-reading, health checks, dependency alerts, decision log, action queue (PE: this is proto–Corporate Intelligence and proto–Decision Intelligence; its decision-log model seeds C24). CLEO: commercial-officer flows (8 models). PE Orion's ecosystem-registry and decision-log patterns; EV the rest.

### 21. maison-doclar-intelligence
13 models of conversation/app-registry design. Knowledge value: the *app registry* and per-app AI identity concept — PE as input to the re-chartered AI Governance registry. The proxy execution path: DI (mandate revoked).

### 22. maison-doclar-gateway
Identity-resolution knowledge: canonical-guest mapping (email/QR/PWA-id → guestId), one-time-reveal API keys, webhook relay with retry, permissions-per-system field filtering, 19 docs of integration/operations runbooks including event-day checklists. **PE all of it** — regardless of Gateway's undecided architectural fate, its identity-mapping rules, permission model, and runbooks are knowledge the Platform's integration seam inherits. (Fate remains **Architecturally Undecided** per ICAA; this entry is knowledge only.)

### 23. maison-doclar-shared
Design tokens (`tokens.css`), gateway/nucleus type contracts. PE tokens into MXS's seed; PE type contracts as interface archaeology.

### 24. plavent
Marketplace knowledge: two-sided verification (OperatorDocument/VerificationStatus), booking/payment/contract/dispute lifecycle, proposal engine with line items, waitlists. PE the transaction-lifecycle design — it is well-modelled and would cost months to re-derive. Entirely outside Platform; preserved for Plavent's own future.

---

## Cross-repository knowledge (owned by no single repo — highest loss risk)
1. **The MPS fragments**: Relay's title enum + Nucleus's tier/household taxonomy + Aperture's guest-trust and seating politics + macs's aso-ebi customs. No repo owns the union; the baseline document must assemble MPS v1.0 from these four sources. **PE, priority zero.**
2. **The event-scoping enforcement pattern**: verify-scripts in Aperture + per-event credential death in facegate + event-slug scoping in satellites — the *practice* of the ratified principle. PE as engineering standard.
3. **The snapshot/ack distributed-truth pattern**: md-events (producer side) + Aperture (consumer side) — only whole when read together. PE as one design document.
4. **The commission pattern**: Assets' render→notify chain + Relay's asset-degrade + Studios tools — the Platform⇄Studios contract ancestor. PE.
5. **Operational runbooks**: Gateway's event-day checklists, PWA's 17 docs, microsite's 12, photo-wall's 14 — institutional memory in markdown. PE into the knowledge baseline verbatim.

## Matrix summary

| Repo | Knowledge density | Algorithms | Invariants | Protocol content | Dominant verdict |
|---|---|---|---|---|---|
| aperture | Very high | Very high | High | High | PE core, DI shell |
| relay | Very high | High | Med | **Very high (MPS seed)** | PE |
| nexus | High | High (simulation, Venari) | High (financial) | Low | PE ops knowledge |
| ESLA/Salle | High | High | Med | Med | PE union w/ aperture |
| md-events | High | High (snapshot/hub) | High | Low | PE wholesale |
| nucleus | High (taxonomy) | Med | High | High | PE taxonomy; service deprecated |
| facegate | Med-high | Med | **High (consent/purge)** | Med | PE posture |
| remote-view | Med-high | Med | High (tenancy/consent) | Low | PE tenancy |
| macs | Med-high | Med | Med | **High (aso-ebi)** | PE pipeline |
| assets | Med-high | High (processors) | Med | Low | PE pipeline |
| pwa/guestbook/photo-wall/lookbook/giftpack/microsite/embeds/live/attend/badges/video-utility | Med | Low-med | Low-med | Low-med | PE selected patterns |
| voxlier/creative-studio/music/djos | Med | Med | Low | Med (cultural music) | PE prompts & pipelines |
| scribe/cleo/orion/intelligence | Med | Low | Low | Low | PE Orion patterns & registry concept |
| gateway | Med-high | Med | Med | Low | PE knowledge; fate undecided |
| shared | Low-med | — | — | — | PE tokens/contracts |
| plavent | High (marketplace) | Med | High | Low | PE for Plavent's own track |

---
*Next deliverable on approval: **06B — Repository Strategy**, assigning each repository one of: Living system · Signature lineage · Platform donor · Knowledge donor · Legacy execution system · Experimental system · Reference implementation · Deprecated · Read-only — every assignment traceable to this matrix. No merge category exists.*
