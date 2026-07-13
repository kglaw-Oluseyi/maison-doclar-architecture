---
artifact_id: STD-WC
name: The World-Class Standard — Measurable Definition
version: "1.0"
status: Ratified
effective_date: 2026-07-13
supersedes: none
depends_on: [ARCH-001, ARCH-005, GOV-001, GOV-005]
decision_refs: [DI-080]
---
# STD-WC: The World-Class Standard — Measurable Definition
**Status:** Ratified (DI-080) — binding from this date forward.
**Applies to:** Every system, module, service, and line of code in the Maison Doclar enterprise.
**Principle:** "World class" is not a feeling. It is conformance to the thresholds below, measured continuously, with evidence. Anything that cannot be measured against this standard is not yet specified well enough to build.

**Ratification note (recorded here because it is part of the artifact's own history, per the HANDOVER-001/DI-034 discipline that frozen and ratified text carries its provenance):** this artifact's original draft header specified two pending conditions — CTO per-threshold review and founder ratification. This entry closes founder ratification, delivered directly by the founder. A per-threshold CTO review, as a discrete recorded exercise, was not separately conducted before ratification; DI-080 records this honestly rather than implying it occurred. See DI-080 for the disposition and the standing CTO obligation this leaves open.

---

## 0. The Meta-Rule

1. World class = top-decile measurable performance against the best published industry benchmark for that dimension, plus zero violations of the hard invariants in §1.
2. **The Ratchet:** every threshold below is a floor, reviewed every 6 months against current elite benchmarks. Thresholds may only move up.
3. **The Evidence Rule:** a claim of conformance without automated measurement is a non-conformance. Every threshold must have a dashboard, a test, or an audit artifact.
4. **The Trade-off Rule:** thresholds may be traded off only by an explicit, logged decision-register entry stating what was sacrificed, why, and the restoration date. Silent shortfall = defect.

## 1. Hard Invariants (binary — one violation = not world class, full stop)
- Zero data loss of guest, financial, or governance records. Ever.
- Zero silent failures: every error logged, classified, handled or alerted.
- Zero secrets in code, logs, or version control. 100% in managed secret storage, rotated ≤ 90 days.
- Zero destructive operations without rollback path.
- Zero authority expansion without ratification (capability ≠ authority).
- Zero production changes outside the pipeline.
- Every placement/decision/output affecting a human is explainable from recorded provenance.

## 2. Reliability & Availability (Google SRE benchmarks)
- Guest-facing surfaces during a live event: 99.95% availability, external synthetic probes.
- Internal/admin surfaces: 99.9%.
- Event-day critical path: 99.99% during event windows + defined degraded mode.
- Every service has a written SLO, error budget, and owner; budget exhausted halts feature work.
- MTTR Sev-1: < 30 min; detection < 5 min.
- DR: RTO < 4 hours; restore drills quarterly, evidenced.

## 3. Performance (Core Web Vitals + latency SLOs)
- Web: LCP < 2.5s, INP < 200ms, CLS < 0.1 at p75 on real Nigerian mobile networks.
- API: p50 < 200ms, p99 < 1s interactive; p99 < 100ms gate/check-in.
- Batch (2,000-guest allocation): < 60s, deterministic, progress feedback.

## 4. Security (OWASP ASVS L2 minimum; L3 for auth/payments/PII)
- 100% endpoints authenticated/authorized, default-deny.
- SAST + dependency scanning every commit; external pentest annually.
- Critical vulns patched < 48h, high < 7 days.
- TLS 1.2+ in transit, encrypted at rest, PII access 100% audit-logged.
- Immutable audit trail for every state-changing action.

## 5. Code Quality (measured, per repository, CI-enforced)
- 100% strict typing; suppressions counted and capped.
- ≥ 85% branch coverage domain logic, 100% money/authority/guest-data paths. No coverage decrease commit-over-commit.
- Every bug fix ships with a regression test.
- Mutation testing ≥ 70% on core domain packages.
- Zero merges without review.
- Static analysis clean; cyclomatic complexity > 15 requires refactor or logged exemption.

## 6. Delivery Performance (DORA "Elite" tier)
- On-demand, multiple deploys/day capability.
- Lead time (commit → production): < 1 day.
- Change failure rate: < 5%.
- Failed-deployment recovery: < 1 hour.
- 100% pipeline deploys, automated verification.

## 7. Data & Analytics
- One authoritative source per fact.
- Guest records ≥ 99% complete on required fields, duplicate rate < 0.5%, honorific accuracy 100% on VIP tiers.
- Every derived number traceable to source records.
- Zero-downtime migration pattern mandatory.

## 8. Experience Quality
- WCAG 2.2 AA, automated + manual audit per release.
- First-time RSVP completion < 3 min unaided, ≥ 95% completion rate.
- 100% human-readable error messages; raw stack trace to user = Sev-2.
- No placeholder copy in production.

## 9. Operability & Observability
- 100% structured logs/metrics/traces with correlation IDs.
- Zero alert fatigue policy.
- Runbooks for every Sev-1, rehearsed pre-flagship-event.
- Cost tracked monthly; >20% MoM change investigated within a week.

## 10. Governance & AI-Specific Standards
- 100% of architectural decisions in the register before implementation, with provenance.
- AI-generated code held to identical bar, no discount.
- Intelligence Plane: 100% advice-only; any write path to operational truth is a §1 violation.
- Autonomous execution only via ratified instruction sets: dry-run + atomic apply + replayable logs.

## 11. The Scorecard
- Every service: §1 pass/fail + §§2–10 green/amber/red vs. threshold.
- World class = 100% of §1 + ≥ 90% green with zero red, sustained a full quarter.
- Reviewed monthly, standing agenda of the founder–CTO session.

## 12. What This Standard Deliberately Rejects
- "Best effort," "should be fine," any quality claim without a number and a measurement.
- Perfection as a target: the target is top-decile measured performance, zero invariant violations, ratcheting upward.
