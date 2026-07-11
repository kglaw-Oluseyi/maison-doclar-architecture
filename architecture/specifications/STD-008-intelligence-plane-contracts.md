---
artifact_id: STD-008
name: Intelligence Plane Contracts (Wire Protocol)
version: "1.0-draft"
status: Draft — review pending; CTR-I1 ruling requested
effective_date: 2026-07-11
depends_on: [CON-001, MKC-001, MPS-001, CAP-001]
decision_refs: [DI-009, DI-010, DI-015, DI-019, DI-025, DI-035, DI-039, DI-040]
evidence_refs: [corpus research/EIDL §2.4 §9 @ 9a645cc9, corpus vision/APE §2–§9, CON-001, STD-002/006/007 pipeline clauses]
---
# Intelligence Plane Contracts (STD-008) — the Execution ⇄ Intelligence wire protocol
**This is a protocol specification, not an AI specification.** It defines what Execution may send, what Intelligence may return, what is guaranteed, what is forbidden, versioning, and failure semantics. Embeddings, retrieval, model selection, prompts, and evaluation internals live behind this contract and are invisible to it. **DI-035 compliance:** every element derives from CON-001, DI-009/012/015/019/025, MKC, and the frozen corpus (EIDL/APE).

## 1. The constitutional unit
One canonical interaction; everything reduces to it:
```
Execution ──AdvisoryRequest──► Intelligence ──AdvisoryEnvelope──► Execution decision ──DecisionRecord──► Decision Intelligence
```
Guest, Event, Vendor, Venue, Attendance (APE), send-time, seating priors, and every future producer are **producers behind the same envelope** — a producer is named in the envelope, never addressed by the requester. Requesters ask for a *capability topic*, not a producer. CON-001 does not fragment.

## 2. Message catalog (complete; nothing else crosses the boundary)
**A. AdvisoryRequest** (Execution → Intelligence): protocolVersion, requestId (globally unique, preserved end-to-end), topic, event-scoped context (self-contained per DI-019 — no discovery back into the requester, ever), pinned versions (MPS, MKC, supported envelope versions), deadline. **B. AdvisoryEnvelope** (Intelligence → Execution): protocolVersion, requestId (echoed), advisoryId (globally unique), producer, recommendation, confidence (calibrated; **confidence theater is prohibited** — wide intervals, staleness flags, and "prior-only, no observed evidence" provenance are mandatory where true, per APE §2.8), reasoning (host-readable), provenance, mpsVersion/mkcVersion/intelligenceVersion, generatedAt/expiresAt. **C. FactSubmission** (Execution → Intelligence, via a *declared pipeline* only): pipelineId+version, event-scoped facts, MKC sensitivity classes, consent basis where required. **D. LessonSubmission** (STD-007 ED-10 capture → C22): structured observation with venue/vendor/MPS tags. **E. DecisionRecord** (Execution → Decision Intelligence): requestId, advisoryId, disposition (accepted/modified/rejected/absent), actor, reason. **F. PipelineDeclaration** (registration, not runtime): source module, fact schema, sensitivity ceiling, purpose, retention — the MKC gate.

## 3. Guarantees (constitutional)
G-1 **Compatibility:** envelope version negotiated per request; field evolution is additive-only; unknown fields are ignored, never errors; required fields are immutable once published; removal/rename = major version, and majors run concurrently through a published deprecation window (DI-025 realized). An older Execution service keeps functioning as Intelligence evolves. G-2 **Identity:** requestIds and advisoryIds globally unique; requestId preserved end-to-end through logs, telemetry, and DecisionRecords. G-3 **Statelessness at the boundary:** every request self-contained; Intelligence never performs discovery against a requesting system (DI-019; Signature identically per DI-015). G-4 **Explainability:** an envelope without host-readable reasoning is protocol-invalid (STD-001 I-2 generalized). G-5 **Determinism of record:** an envelope is immutable once issued; re-asks yield new advisoryIds.

## 4. Prohibitions (constitutional)
P-1 **Advice never blocks execution**: absence is a valid response; no Execution surface may await Intelligence to proceed (CON-001, STD-007 ED-8). P-2 **No knowledge-plane identifiers** in any envelope field consumed into Execution records; advice attaches to event-scoped ids only (DI-009). P-3 **No writes** from any requester into the Knowledge Store; facts enter only via declared pipelines; Signature additionally admits knowledge only through deliberate human review (DI-015). P-4 **Forbidden content classes at the boundary** (EIDL §2.4 adopted as the binding MKC prohibition list, enforced as schema-level rejection in FactSubmission *and* AdvisoryRequest context): health beyond self-declared accessibility/dietary; income/wealth signals; **political affiliation**; photographs without explicit consent; precise home addresses (city/country class suffices); biometric data of any kind; any guest's opinion of another guest (conflict/SEPARATE data enters only via host/operator); anything about minors beyond count and dietary/accessibility. P-5 **Cross-event learning is segment-aggregate only** (EIDL): no individually-linked cross-event facts leave a pipeline without an MKC-recorded basis. P-6 Producers never call Execution; the boundary is strictly request/response plus declared pipelines.

## 5. Failure semantics
Timeout/unavailable → treated as absence (P-1), logged with requestId; retries are requester policy, never producer obligation. Malformed request → typed rejection (protocol error, never silent drop). Pipeline schema violation → fact rejected with MKC-classed reason; the source module alerts. Expired envelope (past expiresAt) → unusable for new decisions; historical DecisionRecords retain it by advisoryId. Producer degradation → envelope may carry degraded-confidence flags but never fabricated certainty (APE honest-uncertainty rule).

## 6. MKC enforcement points
(1) PipelineDeclaration approval — sensitivity ceiling and purpose bound before any fact flows; (2) boundary schema validation — P-4 classes rejected mechanically; (3) sensitive-read audit on Knowledge Store access serving an envelope; (4) DecisionRecords as mandatory closure — an envelope with no disposition after its window is itself a tracked fact.

## 7. CTR-I1 (recorded, ruling requested)
The Guest Knowledge Graph vision (pre-ratification) named political/religious affiliations as graph content; frozen EIDL §2.4 prohibits collecting political affiliation and biometrics outright. **Proposed resolution:** EIDL's never-collect list is ratified as the binding prohibition (P-4); the Graph's scope is constrained accordingly — political affiliation is out absolutely; religious *protocol* data (clerical titles, faith-relevant catering/etiquette already in MPS) remains as protocol fact, distinct from profiled "religious affiliation." Biometrics remain solely in C9's consent-and-purge regime, never in the Knowledge Store.

## 8. Capability dependency matrix (DI-037 artifact)
| Depends on | Consumed through | Provides to |
|---|---|---|
| CON-001 | realized verbatim as §1–§3 | all requesters |
| MKC | enforcement points §6 | Knowledge Store governance |
| MPS | version pinning in every message | protocol-correct advice |
| C16 AI Governance | producer registration + keys | telemetry |
| Provides to | AdvisoryEnvelope | STD-001/002/006/007 surfaces, Signature (DI-015) |
| Provides to | DecisionRecord stream | C24 Decision Intelligence |
