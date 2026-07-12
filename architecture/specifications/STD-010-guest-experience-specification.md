---
artifact_id: STD-010
name: Guest Experience Capability Specification (thin)
version: "1.0-draft"
status: Draft — review pending
depends_on: [MXS-001, MPS-001, STD-002, STD-006, STD-007, STD-009]
decision_refs: [DI-035, DI-043, DI-046]
evidence_refs: [KNW-001 §11–§15 (pwa, guestbook/photo-wall, lookbook/giftpack, microsite/embeds, live), remote-view consent/OTP discipline]
---
# Guest Experience (STD-010) — Execution Plane guest-runtime module (thin spec)
Scope: the surfaces guests touch — personal portal, memory capture (unified guestbook/photo-wall), content experiences (lookbook/giftpack), published sites (microsite/embeds), live/remote participation. Composes STD-002 (guest state), STD-006 (delivery), STD-007 (event-day liveness), STD-009 (media artifacts); renders exclusively under MXS.
**Invariants.** GX-1 Every surface renders under a pinned MXS version at the Guest register — no surface defines its own visual language. GX-2 Guest authentication is possession-based (token/OTP/magic-link per remote-view/pwa donors), event-scoped, and never enters C17 (guests are constitutionally outside staff identity). GX-3 Memory capture is moderated-before-visible (photo-wall donor) with consent recorded per contribution; capsule delivery (guestbook donor) is a post-event obligation with delivery evidence. GX-4 Published sites follow commission→review→publish→export (microsite donor); publishing host-visible surfaces keeps its human approval (GOV-006). GX-5 Remote participation carries consent records, audio-request mediation, and the same protocol dignity as physical presence (remote-view donor). GX-6 Every guest-visible failure state is an MXS-designed experience, never a stack trace — brand-under-stress is part of the spec.
**Objective interface (ARCH-002).** *Keep every guest's digital experience worthy of the event — informed, responsive, delightful — escalating only where a guest's issue needs human care.*
**Dependency matrix.** Depends: MXS (pin), STD-002 (guest state snapshots), STD-006 (messages), STD-009 (artifacts), kernel storage. Provides: participation/memory facts → C22 pipeline; engagement signals → C10.
