---
artifact_id: AMM-001
name: Autonomy Maturity Model
version: "1.0"
status: Ratified realization guide (not architecture)
effective_date: 2026-07-11
depends_on: [ARCH-002, CON-001, MKC-001]
decision_refs: [DI-043]
---
# Autonomy Maturity Model (AMM-001)
A realization guide: the evolutionary path each capability climbs, at its own pace. Not every capability reaches L4; some never should.

**L0 Manual** — humans operate; software records. **L1 Advice** — AI recommends via STD-008 envelopes; humans decide (CON-001 native). **L2 Approved execution** — AI prepares and executes on explicit human approval per action. **L3 Policy execution** — AI executes within ratified policy envelopes (budgets, caps, protocol gates); humans handle exceptions surfaced through defined escalation channels; every action attributable and DecisionRecord-closed. **L4 Long-horizon autonomy** — AI pursues persistent objectives across weeks/months, generating and adapting work, with human oversight at governance checkpoints (requires C25 Mission Planning, which remains reserved).

**Promotion rules (constitutional safeguards before delegation increases):** a capability may rise one level only when (1) its specification is Ready for Build and implemented, (2) its escalation channel is proven in operation, (3) its DecisionRecord closure rate is monitored, (4) the Q3 human floor from GOV-006 is mechanically enforced at every level — floors are level-independent, and (5) the promotion is itself a Decision Register entry. Demotion is always available operationally without ceremony (autonomy degrades to manual; never the reverse dependency — STD-007 ED-8 generalized).
Per-capability targets: GOV-006 Q2 column is the authoritative assignment.
