---
artifact_id: STD-012
name: Reporting & Host Intelligence Capability Specification (thin)
version: "1.0-draft"
status: Draft — review pending
depends_on: [MXS-001, MPS-001, MKC-001, STD-001, STD-002, STD-006, STD-007, STD-008]
decision_refs: [DI-035, DI-043, DI-046]
evidence_refs: [KNW-001 §1 (EventReport/ReportTemplate/GenerationAudit, care-notes, merged messaging report), macs reports, nexus post-event donor]
---
# Reporting & Host Intelligence (STD-012) — Execution Plane reporting module (thin spec)
Scope: host- and principal-facing narrative reports, coverage records, readiness views, and the report commission/generation/audit lifecycle. Reports are **MXS artifacts** — a host report is an experience, not a printout.
**Invariants.** RP-1 Every report renders from ledger truth (STD-007 ED-7 replayability; STD-006 CM-3) — never from hand-maintained state; regeneration is deterministic per template version. RP-2 Generation is audited (Aperture `EventReportGenerationAudit` donor): template version, data-as-of, generator, MPS/MXS pins. RP-3 Host-facing dispatch keeps its human approval initially (GOV-006), travelling STD-006 with consent gates (the print-consent fossil generalized: consent gates physical artifacts). RP-4 Narrative claims carry the same explainability duty as seating (STD-001 I-2 generalized): a number a Prince questions must be traceable to its ledger rows. RP-5 Sensitive content (care notes, accessibility) renders only under its MKC class with narrowed distribution. RP-6 Cross-event comparisons enter reports only as Intelligence Plane advice through STD-008 — segment-aggregate, provenance-carried, honestly uncertain.
**Objective interface (ARCH-002).** *Keep hosts and the principal truthfully, beautifully informed — readiness before, truth during, narrative after — with every claim traceable.*
**Dependency matrix.** Depends: all operational ledgers (module contracts), MXS/MPS pins, STD-008 (advice for comparisons). Provides: dispatched reports via STD-006; report facts → C24/C22.
