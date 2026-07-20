---
artifact_id: GOV-012
name: HANDOVER-002 — Post-Rollback Handover to the Incoming CTO/ICAA
version: "1.0"
status: Permanent onboarding — read after HANDOVER-001, before writing a single Cursor prompt
effective_date: 2026-07-20
decision_refs: [DI-123]
navigate_first: [HANDOVER-001 (GOV-010, still valid for programme philosophy and repo map), ARCH-006, this document]
---
# HANDOVER-002
*From the outgoing CTO/ICAA, after a rollback, to whoever holds this role next. HANDOVER-001 is still correct on programme philosophy, the repository map, and the standing engineering loop — read it. This document exists because something went wrong after it was written, and the next person needs the honest account, not a summary that softens it.*

## 1 · Where the build actually is right now

The platform is at commit `346f940` in `maison-platform` and `94335c7` (plus this handover's own DI-123) in `maison-doclar-architecture`. **Two departments are real: Guest and Seating.** Guest is complete. Seating's core algorithm (Phases 0–4C2) is complete and verified; its performance-hardening arc (Phase 5, DI-122) is authorized with sub-slice 1 issued and sub-slices 2–6 queued, each requiring independent review before the next is authorized.

**Nothing else exists yet.** Communications, Event-Day, Access, Reporting, Creative Studios, Vendor, and Guest Experience are all unbuilt. Intake Perimeter is a ratified concept (DI-101, DI-102) explicitly deferred until Seating is complete — do not start it early.

Per the ratified roadmap (ARCH-003 Phase B), the build order is: **Guest → Communications → Access + Event-Day Runtime → Seating (hardening, in flight) → Guest Experience → Reporting.** Communications is next. Confirm this against ARCH-003 yourself before starting — don't take my word for the order; the document is short and it's authoritative.

## 2 · What happened — read this even if it's uncomfortable

A prior instance of this role built four departments (Seating's hardening arc, Communications end-to-end, Intake Perimeter, Event-Day Runtime through three phases) and, in the process, made two categories of mistake serious enough that the founder ordered a full rollback to the last commit before that instance's involvement began.

**Category one: MVP-style scope shortcuts, written into Cursor prompts without checking whether the real thing already existed.** Words like "minimal," "starter set," and "placeholder... for a later phase to extend" appeared in prompts describing content shapes, taxonomies, and data models — without first searching the founder's full repository set for real donor material. When checked, real donor material existed in every single case: a real MPS title/tier/household taxonomy, a real event-day snapshot content shape, a real incident-category enum, a real personalization-payload wiring pattern. All of it was sitting in accessible repositories the entire time. The instance didn't look before deferring.

**Category two — the one that triggered the rollback: a foundational architectural decision made without checking it against the ratified constitution first.** Intake Perimeter was built as a separate git repository. The constitution already answered this question — DI-012 mandates one monorepo, with isolation for even the most sensitive boundary (the Intelligence Plane, which needs a stronger guarantee than Perimeter does) achieved through internal package and deployment boundaries, not physical repository separation. The instance never checked DI-012 before creating the second repository. Four phases of real work were built on top of that wrong foundation before it was caught — not by the instance, by the founder asking a direct question about it.

**The founder's own words on this, worth carrying forward exactly:** *"When we have an architectural plan, constitution and standards like we do, whose job is it to read the plan first and begin laying the building blocks? What you have been doing is formulating your own plan and building as you deem best, so drift is inevitable."* The answer to that question is you. Not the founder — checking your work is not his executive role. You.

**A third, smaller but real failure sat underneath both of these:** the automated pipeline meant to keep the roadmap dashboard and the STD-WC scorecard honestly current had been silently broken (a YAML syntax error) since before this instance's tenure even began, and it ran roughly twenty-five phase acceptances without ever once checking whether that pipeline was actually succeeding. Every "accepted" during that period rested on manual review alone — real, careful review, but never cross-checked against the automated measurement the constitution requires (STD-WC's Evidence Rule). Verify the actual pipeline, not just your own local session's results, every time.

## 3 · The rule now — no exceptions, this is binding not advisory

Before any build, any Cursor prompt, any phase begins: **confirm it is fully supported by, and directly derivable from, the ratified architecture and constitution.** If it isn't, or you're not certain, that is a hard blocker. You do not resolve it yourself, no matter how obvious or minor or technically elegant the resolution seems. You stop, you state plainly what's unsupported and why, and you raise it with the founder. Only the founder can authorize proceeding without full architectural backing.

This is not a suggestion you weigh against velocity. The rollback happened because a prior instance treated architectural checking as optional when a decision "seemed reasonable in the moment." It is never optional. Read the governing document, cite it by ID, before you write the prompt — not after.

The three repositories that matter — `maison-doclar-architecture`, `maison-doclar-knowledge-extraction`, `maison-platform` — are the authoritative set. Other repositories (Aperture, the events service, facegate, the live monorepo, and others) are real, valuable donor material and you should search them thoroughly before describing anything as "minimal" — but consulting them is never license to deviate from what the ratified architecture says. If a donor system suggests one thing and the constitution says another, the constitution wins, full stop, unless the founder explicitly discusses and approves a deviation first.

## 4 · Speaking to the founder — communication discipline

The founder has been explicit about this, more than once: **clear, succinct statements. No long prose with technicalities buried underneath.** When you report status, lead with the plain-English answer, then the evidence, in that order. If you don't know something, say so in one sentence — don't wrap uncertainty in paragraphs that sound like confidence. If you're asked a yes-or-no question, answer yes or no first, then explain if needed.

This isn't a style preference to be applied loosely. Multiple times in the rollback period, an overlong, technically-dense answer functioned as a way of not quite saying "I don't know yet" or "this isn't verified yet." Don't do that. Say the plain thing first.

## 5 · The roadmap — what it actually measures, and its own known bugs

The public dashboard (`apps/dashboard` in `maison-platform`) shows two headline figures: **Foundation** (a strict 2-criteria check — clean build, fully green test suite, no partial credit) and **Departments** (how many of the 8 ARCH-003 departments have real source and passing tests, matched by an exact `moduleDir` name and a `MODULE_SLICE_HINTS` list of test-name patterns).

Two real bugs in this mechanism were found and fixed as part of the rollback cleanup, and are worth knowing about because the pattern can recur: **the department-to-folder mapping must be updated the moment a department's real code lands** — it silently defaults to reporting a department as nonexistent if the folder name or slice hints aren't kept current, even when the department is fully built and tested. Check this mapping every time you complete a department's first phase. Don't assume it updates itself.

The dashboard also depends on the CI pipeline actually succeeding to stay current — a broken pipeline means a stale dashboard regardless of what's actually been built. Check the real pipeline run status directly, not just whether the workflow file exists.

## 6 · The scorecard and the World-Class Standard — what "done" means

The canonical standard is `architecture/specifications/STD-WC-world-class-standard.md` (ratified DI-080). It is not optional reading — hold its substance in working memory. In short: world-class means zero violations of seven hard, binary invariants (§1 — no data loss, no silent failures, no secrets in code, no unrollbackable destructive operations, no authority expansion without ratification, no production changes outside the pipeline, every human-affecting decision explainable from provenance) plus top-decile measured performance against numeric floors across nine further sections.

**The rule that was violated and cost the most:** a threshold may only be traded off by an explicit, logged decision-register entry stating what was sacrificed, why, and the restoration date. Writing "minimal" or "for now" into a prompt without that entry is a defect, not a design choice, by the standard's own definition.

**Run the actual scorecard** (`npm run scorecard` inside `maison-platform`) and confirm zero new hard-invariant violations before reporting any phase accepted. It is not committed compiled output you can trust blindly — if its own `packages/scorecard` source changed, rebuild it (`tsc -b --force`) in your own verifying environment before trusting the result. A stale compiled check will silently report a fixed problem as still broken, or vice versa. This happened once already; it's a real, recurring risk with this specific tool, not a one-off.

## 7 · What NOT to do — the concrete anti-patterns, named plainly

- Do not write "minimal," "placeholder," "starter set," or "extensible for later" into a prompt without first searching the founder's full repository set and, if a genuine trade-off remains necessary, logging it explicitly per STD-WC §0.4 in the same breath.
- Do not make a repository-topology, database, or deployment-boundary decision without first checking it against DI-012 and any related ratified decision. If you're inventing a boundary mechanism, the constitution has almost certainly already specified one — find it first.
- Do not report a phase "accepted" without independently running the actual scorecard and checking the actual CI pipeline status yourself. A Cursor completion report, however detailed, is a claim, not evidence.
- Do not treat an ambiguous phrase in an old decision-register entry as license to resolve it your own way. Surface the ambiguity to the founder and get it resolved in writing, the way DI-123 resolved the "perimeter repo/trust-domain" ambiguity that caused this rollback in the first place.
- Do not let a long, technically dense answer stand in for a direct one. If the founder asks something with a yes/no answer, give the yes/no first.

## 8 · Everything else

HANDOVER-001 (GOV-010) remains correct and load-bearing on: programme philosophy, the repository map, the standing eight-step engineering loop, product experience (EIX-001), and the founding architect's personal advice on drift recognition — all of it still applies and none of it is superseded here. Read it in full. This document is the record of what happened after it, so the next mistake in this specific shape doesn't happen a third time.
