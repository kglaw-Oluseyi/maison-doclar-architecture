---
artifact_id: EIX-002
name: Executive Intelligence Surface Map v1
version: "1.0"
status: Ratified companion to EIX-001
effective_date: 2026-07-12
decision_refs: [DI-057]
---
# EIX-002 — Surface Map v1
Every surface, relationship, navigation path, and AI touchpoint (◆ = advisory envelope surfaces here; ✋ = permanent human floor visible in UI). Personas: F Founder · O Operator · P Planner · H Host · G Guest · E Event-day Staff.

```
                                   FOUNDER (F)
                                       │
                    ┌──────────────────┼──────────────────────┐
                    ▼                  ▼                       ▼
        S1 EXECUTIVE DASHBOARD   S2 COMMAND BAR         S13 FOUNDER WORKSPACE
        briefing · health ◆      intent → missions       delegation console ✋
        recommendations ◆        search · quick acts     AMM promotions ✋ (evidence-cited)
        alerts · approvals ─┐    (ubiquitous, all F/O/P)  judgement capture · MPS/MXS queue ✋
                    │       │          │                  assumptions · growth signal
                    ▼       └──────────┼──────────────┐
        S3 MISSION WORKSPACE (F/O/P)   │              ▼
        objectives · graph/timeline    │      S4 APPROVAL INBOX (F/O) ✋◆
        observations ◆ · decisions     │      drafted comms · seating proposals
        health · replans ◆ ────────────┼──►   vendor shortlists · schedule changes
                    │                  │      accept/modify/reject → DecisionRecord
     ┌──────────────┼──────────────────┼───────────────┬───────────────┬─────────────┐
     ▼              ▼                  ▼               ▼               ▼             ▼
S6 GUEST        S7 COMMS          S8 SEATING       S11 VENDOR      S12 CREATIVE   S5 OPERATIONS
WORKSPACE (O/P) CENTRE (O)        STUDIO (O)       WORKSPACE (O)   STUDIO (O)     CENTRE (O)
households      campaigns/stages  canvas · runs ◆  performance ◆   commissions    live event
journey · MPS   inbound intents ◆ explanations     contracts(Nexus) events/AT_RISK arrivals·checkin
dedup ledger ✋  suppression       action stack     risk ◆          proof review ✋ incidents·devices
quality · recs ◆ send-time ◆      host cycle ─┐    never-auto-book✋ MXS acceptance degradation banner
     │              │                  │      │                                        │
     │              │                  │      ▼                                        ▼
     │              │                  │   S9 HOST WORKSPACE (H)              E-DEVICE SURFACES (E)
     │              │                  └─► review/approve seating ✋           Director · Concierge
     │              │                      reports (traceable numbers)        Scanner · Kiosk
     │              │                      change requests                    (snapshot-ack'd, offline-first)
     ▼              ▼
S10 GUEST SURFACES (G)
portal · RSVP · memory capture · lookbook/giftpack · remote attendance
(possession auth; MXS-designed failure states)

                    GOVERNANCE & COGNITION TIER (F, O-read)
   S14 INTELLIGENCE & KNOWLEDGE CONSOLE ◆        S15 GOVERNANCE CONSOLE ✋
   unclosed envelopes · overrides · reversals    AI registry · budgets · keys/revocation
   MKC curation queue ✋ · observations           agent identities & authority footprints
   (Situations here, if chartered)
                                   │
                                   ▼ (future, on charter)
                     S16 ENGINEERING INTELLIGENCE WORKSPACE
                     conformance · PRs awaiting gates ✋ · repo health · debt
```

**Navigation grammar:** S2 reaches everything; S1 → S3 → domain workspaces is the descent path (summary → mission → detail); S4 is reachable in one action from anywhere; every ◆ opens its reasoning in one click; every ✋ renders its floor explicitly ("this always requires you"). Event-day: S5 and E-surfaces are the world; everything else recedes.
