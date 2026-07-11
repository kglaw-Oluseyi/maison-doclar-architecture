---
artifact_id: STD-009
name: Media & Commission Contract (Platform ⇄ Studios Wire Protocol)
version: "1.0-draft"
status: Draft — review pending
effective_date: 2026-07-11
depends_on: [MXS-001, MKC-001, CAP-001, STD-006, STD-008]
decision_refs: [DI-027, DI-035, DI-039, DI-041, DI-042]
evidence_refs: [KNW-001 §10 (Assets personalised-video chain, worker registry), KNW-001 §18 (video-utility recipe), DI-027d access-vs-delight rule, badges design-workflow donor]
---
# Media & Commission Contract (STD-009) — the sole Platform ⇄ Studios interface
Protocol specification in the STD-008 style: what the Platform may send, what Studios may return, guarantees, prohibitions, versioning, failure semantics. Production internals (renderers, providers, DAWs, pipelines) live behind the contract. **DI-035 compliance:** derives DI-027d, the Assets commission-chain donor, and STD-008's protocol grammar.

## 1. The constitutional unit
```
Platform ──Commission──► Studios ──[CommissionEvents]──► ArtifactDelivery ──► Platform acceptance ──► DecisionRecord
```
Every media need (personalised video, voice, music, cinematography, lookbook assets, guest gifts) is a **Commission**; every Studios system is a producer behind the same message. The one ratified exception stands (DI-027d): **credential rendering** (cards, badges, QR — anything that grants or verifies access) is C9 in the Platform worker tier, never a Commission. The dividing question remains *does it grant access or does it delight?*

## 2. Message catalog
**A. Commission** (Platform → Studios): protocolVersion, commissionId (globally unique), event-scoped subject context (self-contained; no discovery back into Platform data, ever), media class, brief (creative requirements), **MXS version pin** (the artifact must render under the pinned experience language), delivery requirements (formats, channel-degradation set per STD-006's asset-degrade knowledge — e.g., WhatsApp-deliverable variant verified per the video-utility recipe), deadline/milestone, consent basis where the subject is a guest likeness (MKC class recorded; corpus P-4 photograph-consent rule applies). **B. CommissionEvent** (Studios → Platform, append-only): ACCEPTED, IN_PRODUCTION, PROOF_READY, REVISION_REQUESTED→REVISED, DELIVERED, DECLINED(reason), AT_RISK(reason, new estimate) — the ledger grammar, mirroring STD-006 CM-3. **C. ArtifactDelivery**: artifact **references only** (B2 keys via the kernel storage adapter — never rows, never inline payloads), per-variant manifest (format, channel-fitness verification results), provenance (producer, MXS version rendered under, source-commission lineage), rights/usage class. **D. AcceptanceRecord** (Platform → Studios + C24): accepted/revision/rejected with reason — closing the loop as a Decision Intelligence fact.

## 3. Guarantees
MC-1 Compatibility per STD-008 G-1 (negotiated versions, additive-only, unknown-ignored, immutable required fields). MC-2 commissionId preserved end-to-end; all events and deliveries reference it. MC-3 Every artifact is MXS-pinned and channel-verified before DELIVERED (the compress-until-accepted-then-verify recipe is contractual, not best-effort). MC-4 AT_RISK is mandatory early signalling — silent lateness is a protocol violation; escalation flows to the operator surface, not to automated cancellation. MC-5 Deliveries are immutable; corrections are new versions under the same commissionId.

## 4. Prohibitions
MP-1 Studios never reads Platform data; the Commission's self-contained context is the entire truth available. MP-2 The Platform never reaches into Studios pipelines or storage internals; references only. MP-3 No guest PII beyond the commission's declared, consent-based context; guest likenesses require recorded consent basis (MKC). MP-4 No access-granting artifact may be commissioned (DI-027d); such requests are protocol-rejected toward C9. MP-5 No shared tables, queues, or schemas — messages only.

## 5. Failure semantics
DECLINED and deadline-lapse both resolve to operator escalation with the commission intact (re-commission is a new decision, recorded). Malformed commissions are typed rejections. Studios unavailability degrades to absence — event-critical media therefore always has a lead-time policy at commissioning, not a synchronous dependency (P-1 spirit inherited).

## 6. Objective interface (ARCH-002 lens)
Objective form: *deliver commissioned media meeting the MXS standard before its milestone, signalling risk early and escalating only where creative or budget judgement is required.* An autonomous planner operates this capability entirely through §2's messages: commissioning against milestones, reading CommissionEvents as observations, treating AT_RISK as the exception channel. No new primitives.

## 7. Capability dependency matrix (DI-037 artifact)
| Depends on | Consumed through | Provides to |
|---|---|---|
| MXS | version pin per commission | brand-correct artifacts |
| MKC | consent classes on likeness/PII | governed subject context |
| Kernel storage | B2 references (DI-027) | artifact transport |
| STD-006 | channel-degradation requirements; delivery hooks | deliverable variants |
| STD-008 grammar | protocol style, DecisionRecord closure | C24 acceptance stream |
| Provides to | ArtifactDelivery | STD-006 (send), guest-experience surfaces, C10 reporting |
