---
artifact_id: STD-006
name: Communications Capability Specification
version: "1.0-draft"
status: Draft — review pending; implementable on MPS v1.0 publication
effective_date: 2026-07-11
depends_on: [MPS-001, MKC-001, CON-001, CAP-001, STD-002]
decision_refs: [DI-004, DI-012, DI-027, DI-035, DI-036]
evidence_refs: [corpus architecture/herald-communications-intelligence-platform-spec.md @ 9a645cc9, KNW-001 §2 (Relay), extracted/mps/mps-corpus-normalized.md §7–8]
---
# Communications Capability Specification (STD-006) — Execution Plane comms module
**DI-035 compliance:** every concept below derives, composes, specializes, or constrains an existing constitutional concept; provenance is cited per section. No new primitives are introduced.

## 1. Scope and the ratified split (derives DI-004)
Two layers, one module. The **Protocol Messaging layer** (never sold): address-form resolution, etiquette, template governance, tone — all MPS-bound, MXS-rendered. The **Channel Engine** (potentially separable later along the package seam): orchestration, queueing, delivery, suppression, inbound routing. Owns: communications, campaigns/stages, preference profiles, suppression state, delivery attempts/events, inbound threads. Does not own: guest data (consumes STD-002 audience snapshots), providers (kernel delivery adapters, DI-027), cross-event intelligence (Intelligence Plane), AI configuration (C16 registry).

## 2. Invariants
CM-1 **Recipient ≠ subject** (derives Herald §4 + STD-002 G-6): every send resolves subject (who it is *about*: guest/household) and recipient (who it *reaches*: guest or CommunicationContact — PA, adult child, caregiver) as separate fields on every record. CM-2 **One Communication, many DeliveryAttempts** (Herald §4): fallbacks and retries are attempts within one Communication; the sibling-job pattern is Negative-Knowledge-adjacent and prohibited. CM-3 **The ledger is the truth** (specializes DI-012's append-only fact log): every transition writes exactly one CommunicationEvent; all state, analytics, and triggers project from the ledger. CM-4 **Lifecycle is the ratified state machine** (Herald §14 adopted verbatim as normative): DRAFT→QUEUED→SENDING→SENT→DELIVERED→READ→CLICKED→RESPONDED, monotone engagement, capability-adjusted per channel; terminals FAILED/SUPPRESSED/EXPIRED/CANCELLED; SENDING is never cancellable, QUEUED is; idempotency collapse returns the existing Communication. CM-5 **Suppression is reasoned and absolute** (MPS §7): Do-Not-Contact overridden only by an audited operator emergency action; every suppression writes SUPPRESSED(reason) — "why didn't she get it" is always answerable. CM-6 **Preferences are projected, not hand-maintained** (Herald §8): PreferenceProfile computes from guest fields, contact links, class grants; precedence = subject's explicit instruction > host instruction > event default; profiles drive both audience counts and routing. CM-7 **Policy attaches hierarchically** (Herald §4): stage > campaign > class > event default, for channel/retry/send-window/throttle policies. CM-8 **Quiet hours hold visibly** (holdUntil on QUEUED). CM-9 **Courtesy discipline** (Herald §15.10–11): no automated "you're uninvited," ever — substitution and removal cancel queued sends with courtesy-flagged, operator-mediated classes. CM-10 **Household dedup** (Herald §15.3): one physical send per address per content class per household for household-level content — the shared-phone rule generalized. CM-11 Every template and address form pins MPS version; every rendered surface pins MXS version. CM-12 Event-scoped absolutely (G-1 inherited); cross-event learning leaves only via declared pipelines.

## 3. Aggregates (composes Herald §4/§7 with STD-002 contracts)
**Communication** (class taxonomy: INVITATION/REMINDER/RSVP_REQUEST/CONFIRMATION/ACCESS_CARD/PARKING/LIVESTREAM/THANK_YOU/SURVEY/OPERATIONAL; subject+recipient; lifecycle state; personalization payload) → **DeliveryAttempt** (channel, adapter, address, providerRef, error taxonomy) → **CommunicationEvent** (the ledger) → **Campaign / CampaignStage / StageEnrollment** (journey position per recipient; per-stage channel/retry/template sets; dynamic audience entry/exit mid-campaign with queued-send cancellation and correct-stage admission) → **AudienceDefinition** (consumes STD-002 audience snapshots; static or dynamic) → **PreferenceProfile** (catalog: DNC, Contact-PA/Adult-Child via purpose routing, Email/SMS-Only, WhatsApp-Preferred, Emergency-Only, Remote-Attendance-Only) → **SuppressionRecord** (MPS §7 taxonomy incl. deceased) → **InboundThread** (intent disambiguation; RSVP writeback to STD-002).

## 4. Plane assignment (constrains Herald by DI-009/DI-012 — partial Superseded finding)
Herald's operational (a) and per-campaign performance (b) analytics remain Execution-side projections of the ledger. Herald's cross-event intelligence layer (c) — fatigue curves, channel-effectiveness priors, send-time effectiveness — **does not live in this module**: those are Intelligence Plane graphs fed by declared pipelines from the ledger, returning as advice per CON-001 (send-time recommendation etc.), degrading to absence. Relay's eight AI micro-capabilities (Hypothesis-class) enter only as registered call sites under C16 and only where they fit this plane split.

## 5. Contracts
Consumes: STD-002 audience snapshots and change events; kernel delivery adapters (capabilities descriptor, send, webhook translator, address validator, health probe — Herald §9 shape adopted for the adapter contract); MPS/MXS packages; C16-issued keys. Publishes: communication facts to the ledger pipeline; RSVP writebacks to STD-002; access-card delivery hooks to C9 (dormant-with-record where the counterpart is absent, per Herald H5's precondition discipline). Advisory: requests send-time/channel advice via CON-001 with full self-contained context.

## 6. Acceptance corpus
Herald §15's edge-case catalogue (all 12+ cases) adopted as behavioural specifications, joined by Relay's validated production knowledge (WhatsApp template-quality/ban-risk handling, asset degradation per channel, webhook reconciliation) and MACS's lifecycle-bound notification obligations (MPS §9). Validation approach per Herald §16, adapted to module scope.

## 7. Open items
O-1 MPS v1.0 publication (CM-11). O-2 The CommunicationContact entity shape finalizes against the Data Acquisition canonical spec during that document's extraction pass. O-3 Escalation-record consumer (who works FAILED-terminal escalations) lands with the Event-Day/operator surface spec.

## 8. Capability dependency matrix (DI-037 artifact)
| Depends on | Consumed through | Provides to |
|---|---|---|
| MPS / MXS | versioned packages (address forms, templates, tone) | — |
| STD-002 Guest Domain | audience snapshots, contact links | RSVP writeback |
| Kernel delivery adapters | DI-027 (Herald §9 contract shape) | — |
| C16 AI Governance | issued keys, registered prompts | usage telemetry |
| CON-001 | advisory requests (send-time/channel) | accept/reject → C24 |
| Provides to | ledger pipeline (declared) | Intelligence Plane (engagement graphs) |
| Provides to | card-delivery hooks, status fanout | C9, STD-007 |

## 9. Objective interface (ARCH-002 lens)
Objective form: *achieve the RSVP/confirmation target by the specified date while respecting protocol, consent, budget, and fatigue caps.* Composes: campaigns/stages, preference routing, advisory send-time/channel advice, suppression discipline — CM-5/CM-9 courtesy gates are inviolable regardless of objective pressure.
