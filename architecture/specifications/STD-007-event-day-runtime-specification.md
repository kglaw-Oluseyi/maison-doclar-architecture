---
artifact_id: STD-007
name: Event-Day Runtime Capability Specification
version: "1.0-draft"
status: Draft — review pending
effective_date: 2026-07-11
depends_on: [MPS-001, MXS-001, MKC-001, CON-001, CAP-001, STD-001, STD-002, STD-006]
decision_refs: [DI-012, DI-018, DI-027, DI-035, DI-037]
evidence_refs: [KNW-001 §5 (md-events), KNW-001 §1 (Aperture event-day), KNW-001 §7 (facegate EDR), KNW-003 fossils, md-events snapshot/hub/fanout services @ clone, Aperture DeviceSession/DeviceSnapshotAck/CheckInLedger/IncidentLog models, live apps admin/mc/room/live-socket]
---
# Event-Day Runtime Specification (STD-007) — Execution Plane event-day module
**DI-035 compliance:** derived throughout; every invariant names its donor. C5 is where reputation is won in real time; this module's physics are dictated by A-003 (Validated: venue internet degrades).

## 1. Scope
Owns: event-day snapshots and their distribution, device fleet (sessions, roles, acknowledgements), check-in and re-entry ledger, incident log, floor assignments, show control (MC/room/display orchestration), the on-venue Hub, and the lesson-capture obligation feeding Event Intelligence. Does not own: guest truth (consumes STD-002 snapshots), credentials/biometrics (C9 — facegate integrates via the EDR contract shape), seating decisions (consumes STD-001 allocations), messaging (STD-006, e.g. access-card delivery hooks).

## 2. Invariants (all derived)
ED-1 **The snapshot is authoritative during degraded operation** (md-events snapshot ingestion→publication→delivery pipeline; Aperture `EventDaySnapshot`): devices operate against the last acknowledged snapshot version; live queries are an optimization, never a dependency. ED-2 **Acknowledgement before advancement** (Aperture `DeviceSnapshotAck` with `snapshotVersion`): a snapshot version is not "current" for a device until acked; the runtime always knows which version each device holds. ED-3 **Append-only operational ledger** (Aperture `CheckInLedger`, `IncidentLog`; specializes DI-012's fact log): check-ins, re-entries, incidents, and overrides are appended, never mutated; corrections are new attributable entries. ED-4 **Every transition is attributable** (`deviceId` + `deviceSessionId` + operator on ledger rows; DeviceSession `secretVersion`/`operatorName`): no anonymous operational action exists. ED-5 **Commands are idempotent** (fanout services' delivery semantics; Herald/STD-006 idempotency discipline generalized): re-delivered fanouts (seating updates, check-in propagation, card-delivery status) converge — duplicate application is a no-op by key. ED-6 **Offline-first degradation ladder** (the Hub: `hub-provision`/`hub-bridge`/`hub-mode`): cloud → venue Hub bridge → device-local snapshot; each rung serves reads and queues writes with replay on reconnection; identity verification degrades to local session validation (DI-027 C17 event-day sovereignty). ED-7 **Replayable timeline** (ED-3 + ED-2 corollary): the event day can be reconstructed from the ledger + snapshot versions — required by reporting (C10) and Event Intelligence (C22). ED-8 **Advice never blocks execution** (CON-001): Intelligence Plane suggestions (e.g., open-second-lane priors) degrade to absence; no event-day surface awaits an advisory response. ED-9 **Role-scoped device surfaces** (md-events Director/Concierge/Scanner/Kiosk; live's admin/mc/room split): each device session binds one role; capability follows role, rendered per MXS register (Event-day Staff). ED-10 **Lesson capture is a workflow obligation** (DI ratified via Deliverable 04): the runtime prompts structured observations at defined moments (doors, peak, incident close, teardown); lessons carry venue/vendor/MPS-version tags into the C22 pipeline. ED-11 **Access decisions flow inward** (facegate EDR contract: `edr-connection`, `edr-client`): verification systems push decisions to the runtime; the runtime never calls out synchronously to verify at the door. ED-12 Event-scoped absolutely; snapshots, sessions, and ledgers die with the event (credentials per C9 rules).

## 3. Aggregates
**EventDaySnapshot** (versioned, published, content-addressed) → **DeviceSession** (role, secretVersion, operator binding) → **DeviceSnapshotAck** (device × version) → **CheckInLedger** (append-only; entry/re-entry/exit kinds; source = scan/manual/EDR) → **IncidentLog** (category taxonomy from donor, escalation state) → **FloorAssignment** (usher/concierge posts — md-attend donor) → **ShowControl** (cue sheets, MC prompts, room/display states — live donor) → **HubSession** (provisioned venue bridge, mode, replay queue) → **LessonCapture** (structured observation, tags, capture moment).

## 4. Contracts & dependency matrix (DI-037 artifact)
| Depends on | Consumed through | Provides to |
|---|---|---|
| STD-002 Guest Domain | audience/guest snapshots (module contract) | guest state at the door |
| STD-001 Seating | published allocations (module contract) | seating fanout to devices |
| STD-006 Communications | access-card delivery hooks (dormant-with-record) | delivery status fanout |
| C9 Access/facegate | EDR decision push contract | verification results into ledger |
| C17 Identity | kernel client (local validation in Hub mode) | attributable sessions |
| MPS / MXS | versioned packages | protocol-correct, register-correct surfaces |
| CON-001 Intelligence | advisory requests (self-contained) | accepted/rejected advice → Decision Intelligence |
| C22 Event Intelligence | declared lesson pipeline | LessonCapture facts |
| Kernel workers/storage/observability | DI-027 packages | fanout execution, snapshot storage, tracing |

## 5. Acceptance corpus
Donor behaviours adopted as specifications: snapshot ingestion/publication/delivery semantics and CSV tolerance (md-events), ack-versioning (Aperture), Hub provision/bridge/replay, fanout idempotency, re-entry verification flow (facegate EDR), MC/room cue propagation (live). Edge cases carried: device joins mid-event (must ack current snapshot before serving), duplicate scan bursts, Hub-partition split-brain (ledger merge by attributable append, never overwrite), operator handover mid-shift (session rebind, attribution preserved), incident during network loss (queued with local timestamp authority).

## 6. Open items
O-1 Incident category taxonomy final shape (donor enum extraction pass). O-2 ShowControl cue-sheet grammar (live deep extraction). O-3 Hub hardware/provisioning standard (implementation-guidance document, not constitutional).
