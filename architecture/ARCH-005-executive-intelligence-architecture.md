---
artifact_id: ARCH-005
name: Executive Intelligence Architecture
version: "1.0"
status: Ratified — the lens for every implementation decision; intended as the final routine constitutional architecture artifact
effective_date: 2026-07-11
depends_on: [ARCH-001, ARCH-002, ARCH-004, AMM-001, CON-001, MKC-001, MPS-001, CAP-001, GOV-001, GOV-006, STD-008]
decision_refs: [DI-049]
---
# Executive Intelligence Architecture (ARCH-005)
This document composes the ratified constitution into one technical architecture. It invents nothing; every clause cites its source. It is the lens through which every implementation decision is evaluated: **"How does this help the Executive Intelligence operate the enterprise autonomously, within constitutional governance?"**

## 1. What the Executive Intelligence is
The constitutional operator of the Maison Doclar estate (README P7; ARCH-004 v1.1) — an executive operating intelligence, not a chatbot, assistant, agent, or multi-agent framework. The founder is CEO and the source of authority and judgement. Scope: the Maison Doclar estate, permanently (DI-048).

## 2. What it owns — and never owns
**Owns:** plans, missions (post-C25), advisory reasoning, institutional memory access under MKC, orchestration state, its own telemetry. **Never owns:** domain state (Goals coordinate, capabilities own — DI-045); operational truth (the Execution Plane owns it — DI-009); the constitution (humans amend it); authority (delegated only, §4); guest data outside Event scope (DI-048, permanent); money movement, host sign-offs, and every GOV-006 floor.

## 3. How objectives become execution
```
Founder → Objective → [Executive Intelligence] → Goal (DI-045: intent, human-owned,
never owning domain state) → decomposition into capability objectives (ARCH-002
interfaces) → capability modules execute governed operations → Execution systems →
Reality → Telemetry & Lessons (STD-007 ED-10) → Knowledge (MKC) → Reasoning →
Advice & improved plans (CON-001/STD-008) → DecisionRecords → Founder
```
The loop closes through the founder. Mission-level orchestration (long-horizon decomposition, delegation, adaptation) is C25, reserved until chartered; until then the loop runs at capability-objective granularity with humans as the mission layer.

## 4. How authority works (the DI-049 refinement, verbatim)
*The Executive Intelligence may exercise any delegated operational authority that is explicitly granted by the constitution and applicable governance, regardless of whether the action is performed through software, communication, or orchestration. Capabilities never imply authority, and all authority remains revocable.* Mechanics: every authority is a **delegation grant** — explicit (a register entry or ratified policy envelope), bounded (scope categories per DI-048; budgets, caps, gates), auditable (attributable actions, DecisionRecord closure), and revocable (key revocation via GOV-001; AMM demotion without ceremony). There is no residual authority: what is not granted is not held.

## 5. How memory works
Four strata, one epistemology. The **Knowledge Store** is institutional memory (scoped per DI-048; guest data always Event-scoped). The **Decision Register** is executive memory — why the enterprise chose what it chose. The **Preservation Matrix and Historical Corpus** are long-term cognition — what was learned, attempted, and rejected (Negative Knowledge remembers failures with equal rigor). **MKC is the epistemology**: provenance, calibrated confidence, contradiction coexistence, honest uncertainty (confidence theater prohibited — STD-008), sensitivity, lifecycle, audit. Nothing is known that MKC has not admitted.

## 6. How governance constrains intelligence
The constitutional stack binds in order (DI-020 precedence): MPS is domain law; MXS is experience law; MKC is knowledge law; CON-001/STD-008 is **the nervous system — the only interface between execution and cognition** (P-1: advice degrades to absence, never failure); GOV-001 registers every model, prompt, key, and agent identity; GOV-006 floors are level-independent; AMM-001 gates every delegation increase with operational evidence. The intelligence cannot reach around this stack: the boundaries are physical (two stores, never joined — DI-012) and protocolar (schema-level P-4 rejection), not behavioural promises.

## 7. How capabilities are orchestrated — and how agents fit
Capabilities are the skills (CAP-001); each exposes an objective interface composing only its own governed operations (ARCH-002/DI-035). Orchestration addresses **topics, never producers** (STD-008 §1); all coordination flows through envelopes and DecisionRecords. **Agents are capability operators** — specialist workers executing within one capability's authority, holding registered identities (GOV-001), attributable like any human operator (STD-007 ED-4 generalized). Agents are replaceable executors; **the model layer is replaceable infrastructure** — the Executive Intelligence is the architecture, not the LLM. No agent talks to another agent except through capability contracts; there is no side-channel agent mesh.

## 8. How engineering autonomy fits
The Engineering Intelligence is a reserved specialization of the Executive Intelligence (DI-048), inheriting the entire stack above. On charter, its outputs follow the same physics as any operator's: analysis → proposals → pull requests → human-governed review and deployment gates → rollback demonstrated (AMM evidence). It never merges its own ungoverned change; DI-029 stands — no architectural decision originates in an implementation repository, including decisions proposed by the system itself.

## 9. How human oversight works
Humans appear at five fixed points: **authority** (all delegation grants and revocations), **floors** (GOV-006, mechanical, level-independent), **exceptions** (escalation channels proven before any L3 promotion), **judgement** (Decision Intelligence's Judgement stream — the founder's irreplaceable contribution), and **amendment** (only humans change the constitution). Demotion of any autonomy requires no ceremony; promotion always requires evidence (AMM v1.1).

## 10. How learning changes behaviour
Learning never silently changes behaviour. The only channels: knowledge admission (MKC) → advice (envelopes, honestly uncertain) → human dispositions (DecisionRecords) → **governed change**: policy-envelope amendments, prompt-registry versions (GOV-001 review flow), AMM promotions (register entries citing telemetry), or constitutional proposals. Behavioural drift outside these channels is a defect, not adaptation.

## 11. What "self-improvement" actually means (Phase G)
The estate improves itself **by proposing through its own governance**: the system may draft the amendment, the PR, the policy change, the process improvement — with evidence attached — and humans ratify, always. Self-improvement is the compounding of the §3 loop across software, processes, documentation, and institutional knowledge (Roadmap Phase G), never self-authorization.

## 12. What can never become autonomous
Permanently human, regardless of AMM level or accumulated evidence: constitutional amendment and ratification; MPS/MXS/MKC changes; delegation grants and their revocation; money movement (C11); host sign-off on final charts and host-facing dispatch where GOV-006 retains it; biometric consent and purge authority; scope changes (including the Urbanac boundary); Signature knowledge admission (DI-015); read-only repository transitions; and the appointment or removal of the humans who hold these powers.

---
*This document is intended to end the routine production of constitutional architecture. From here, the dominant artifacts are the `maison-platform` implementation, the Executive Intelligence runtime, knowledge ingestion, the decision engine, operational telemetry, tests, and code. New constitutional documents become exceptional, driven by genuine architectural need (DI-045 growth signal; DI-048 expectation).*
