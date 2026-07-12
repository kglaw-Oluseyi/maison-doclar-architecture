---
artifact_id: STD-011
name: Access & Guest Identity Capability Specification (thin)
version: "1.0-draft"
status: Draft — review pending
depends_on: [MKC-001, STD-002, STD-006, STD-007]
decision_refs: [DI-027, DI-035, DI-043, DI-046]
evidence_refs: [KNW-001 §7 (facegate: consent/purge, EDR), Aperture card pipeline (JWT QR, delivery, check-in ledger), KNW-003 fossils]
---
# Access & Guest Identity (STD-011) — Execution Plane access module (thin spec)
Scope: event-scoped guest credentials (QR/cards/tokens), biometric enrolment/verification integration (facegate as the realizing surface), access grants, and the credential half of check-in. Credential *rendering* is C9-in-Platform per DI-027d; verification decisions flow inward per STD-007 ED-11.
**Invariants.** AC-1 Credentials are event-scoped and die with the event (constitutional; fossil-backed). AC-2 **Biometrics are deletable by design**: consent recorded at enrolment; purge is a first-class, human-auditable admin action (facegate `purge-biometrics` fossil); biometric data never enters the Knowledge Store (STD-008 P-4 — permanent). AC-3 Credential issuance binds to STD-002 guest state at issue time and re-validates against the current snapshot at the door — a revoked guest's credential fails closed. AC-4 Every verification attempt is ledgered with device/session attribution (STD-007 ED-3/ED-4), success or failure. AC-5 Credential delivery travels STD-006 (class ACCESS_CARD) with delivery evidence; undelivered credentials surface in readiness (STD-002 G-10). AC-6 Signed tokens follow the estate standard (HS256, shared-secret discipline); one-time-reveal for any secret material (Gateway knowledge asset #4).
**Objective interface (ARCH-002).** *Every legitimate guest passes the door in seconds; every illegitimate attempt fails closed and visibly; consent and deletability are never compromised for throughput.*
**Dependency matrix.** Depends: STD-002 (guest state), STD-006 (delivery), STD-007 (ledger/EDR), kernel storage/identity. Provides: verification decisions → STD-007; credential status → readiness; consented enrolment facts → nothing beyond C9 (never the Knowledge Store).
