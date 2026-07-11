---
artifact_id: MPS
version: 0.1
status: Draft
effective_date: 2026-07-11
supersedes: none
depends_on: [MKC]
decision_refs: [DI-011]
evidence_refs: [knowledge/preservation-matrix.md#A8]
---
# Maison Protocol System (MPS)
**Status: v0.1 — assembly charter. Target: MPS v1.0 (constitutionally ratified).**

MPS is Maison Doclar's primary intellectual property: the encoded understanding of Nigerian and diaspora high-protocol event culture. It is versioned like source code; every event, recommendation, and protocol decision records the MPS version under which it was made. Lessons never edit history — they produce the next version.

## Scope
Titles and address forms · precedence rules · household composition grammar · VIP tiering · entourage/ADC/security-detail conventions · invitation culture · communication etiquette · diplomatic handling · seating politics · aso-ebi and gifting custom · protocol exceptions register.

## v1.0 assembly sources (knowledge lineage, per Matrix A8)
1. **Relay title & suppression taxonomy** (`maison-doclar-relay/src/db/schema.ts`) — confidence: Founder-verified, target Constitutionally ratified. The most complete title corpus in the estate (royal, chieftaincy, military, clerical, political; `deceased` as first-class suppression reason).
2. **Nucleus taxonomy** (`nucleus/prisma/schema.prisma`) — household/engagement model, five VIP tiers, six group types, child/interpreter models, couple-split and duplicate edge cases.
3. **MACS aso-ebi customs** (`md-macs/src/lib/`) — collection types, order pipeline, partner/guest role conventions.
4. **Aperture guest-trust & seating politics** (`maison-doclar-aperture/lib/guest-trust/`, `lib/seating/`) — trust checklist, relationship tag presets, tier handling in allocation rules.
5. **Founder register** (Human Knowledge Assets, declarative category) — elicited rules, marked Founder-authored until verified.

## Confidence vocabulary (per ICAA ruling)
`Founder-authored` → `Founder-verified` → `Constitutionally ratified`. "Canonical" is reserved for ratified content only.
