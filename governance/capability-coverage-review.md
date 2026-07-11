---
artifact_id: GOV-006
name: Capability Coverage Review (C1–C24)
version: "1.0"
status: Ratified-pending-review
effective_date: 2026-07-11
decision_refs: [DI-043]
---
# Capability Coverage Review — three questions per capability
**Q1 Specified? · Q2 Autonomously operable (target AMM level)? · Q3 Mandatory human approvals (the floor autonomy never erases)?**

| Cap | Q1 Specified | Q2 Target | Q3 Permanent human floor |
|---|---|---|---|
| C1 MPS | v1.0 in review (founder session pending) | L1 | Every rule change ratified by founder/authority — permanent |
| C2 Guest | STD-002 draft | L3 | Ambiguous merges; PII-sensitive corrections; host-originated changes review |
| C3 Seating | STD-001 **ratified** | L3 | Host sign-off on final chart — permanent |
| C4 MXS | Charter only — **gap: constitution needs authoring (design authority)** | L1 | All changes by design authority — permanent |
| C5 Event-Day | STD-007 draft | L2→L3 | Door overrides; incident escalations; emergency actions audited |
| C6 Communications | STD-006 draft | L3 | DNC emergency override (audited); new template classes; budget ceiling changes |
| C7 Guest Experience | **Gap: thin spec or explicit waiver needed** (partially covered via STD-006/007 surfaces) | L3 | Publishing host-visible surfaces initially |
| C8 Media | STD-009 draft (contract) | L2→L3 | Creative acceptance initially; budget changes |
| C9 Access | **Gap: thin spec needed** (EDR contract shape exists in STD-007) | L2 | Biometric enrolment consent; purge audit — permanent |
| C10 Reporting | **Gap: thin spec needed** | L3 | Host-facing final dispatch initially |
| C11 Revenue Ops | Deferral (Nexus realizes; spec at migration) | L1–L2 | **Money movement always human-approved — permanent** |
| C12 Vendor/Compliance | Deferral (Nexus) | L2 | Contract execution; compliance attestations — permanent |
| C13 Planning | Deferral (Nexus + roadmap) | L3 | Milestone commitments to clients |
| C14 Corporate Intel | Orion realizes; spec light | L3 | — |
| C15 Venue Intel | Producer behind STD-008 (spec with producer wave) | L1 by nature (advice) | n/a — advisory only |
| C16 AI Governance | GOV-001 charter; realization spec thin | L2→L3 | Policy changes; new provider approval |
| C17 Identity | Decided DI-027; thin spec at kernel build | L2 | Role elevation; org admin grants |
| C18–21 Commodity kernel | DI-027 package set; specs at implementation | L3–L4 | Infra by policy |
| C22 Event Intel | Producer behind STD-008; capture in STD-007 ED-10 | L3 capture | Lesson admission via MKC review |
| C23 Vendor Intel | Producer behind STD-008 (Venari donor) | L1 by nature | Recommendations never auto-book — booking is C11/C12 with human floor |
| C24 Decision Intel | Seeded; recording via STD-008 DecisionRecord | L4 recording / advisory analysis | — |

**Coverage verdict:** boundary and heavyweight capabilities are specified. Before the Implementation Roadmap: thin specs or explicit waivers for **C7, C9, C10**; the **MXS constitution authoring** is scheduled founder/design-authority work (with the founder session). Nexus-realized capabilities are explicit deferrals, specified at migration time. Producers (C15/C22/C23 internals, APE) are specified per-producer in the Intelligence wave, all behind STD-008.
