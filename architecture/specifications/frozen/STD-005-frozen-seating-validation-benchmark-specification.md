---
artifact_id: STD-005
name: Frozen Seating Validation & Benchmark Specification
version: frozen
status: Frozen — immutable historical evidence, constitutionally promoted
authored: Jun 2026 (principal–Claude architecture session; declared frozen at authorship)
promoted: 2026-07-11
provenance: maison-doclar-knowledge-extraction raw/historical-event-os/algorithms/ @ 9a645cc9 (attested by principal)
decision_refs: [DI-030, DI-034]
note: Original content below is verbatim and unchanged; only this metadata block is prepended.
---
# Aperture Seating Optimizer — Validation & Benchmark Specification

**Status:** Engineering validation protocol. Implementation-independent.
**Governing documents (frozen, immutable):**
1. *Formal Optimization Specification* ("the Model") — symbols $F$, $f_k$, $w_k$, $P_u$, $\delta_k$, H1–H10, V1–V5, D1–D6.
2. *Algorithm Design Specification* ("the Design") — Phases 0–4, neighborhoods N1–N11, invariants I1–I7, state structures, budgets.

This document does not modify either. It operationalizes the Model's V1–V5 gate and the Design's invariants into a concrete, permanent protocol. **Any implementation claiming conformance must pass every mandatory element here; any future optimizer must beat the incumbent under §13 before replacing it.**

---

# 1. Validation Philosophy

## 1.1 Six distinct claims, six distinct proofs

A statement like "the optimizer works" conflates six claims that fail independently and are therefore validated independently:

| Claim | Question answered | Proven by |
|---|---|---|
| **Mathematical correctness** | Does the *specification* describe a well-posed problem whose checks are themselves right? | Hand-verified micro-instances (§2) with independently derived expected outputs — solutions small enough to enumerate or verify by hand, so the test oracle does not depend on any optimizer. |
| **Implementation correctness** | Does the code faithfully implement the Model's constraints and objective? | The functional suite (§2), incremental-vs-full equivalence (§9), and the independent evaluator (V2) — a second implementation of $F$ sharing no code with the optimizer. |
| **Optimization quality** | How close is the returned $F$ to the best achievable? | Exact CP-SAT references on ≤ 300-guest instances (Model V5), instance upper bounds (Design §6), and the metric thresholds of §5. |
| **Runtime performance** | Is it fast enough, and does it scale as the Design projects? | The performance benchmarks of §6 on the fixed corpus of §3. |
| **Explainability correctness** | Do explanations *exactly* describe the returned solution? | Decomposition exactness and reconstruction tests of §8 — never review of prose plausibility. |
| **Reproducibility** | Same inputs, seed, config ⇒ same everything? | Byte-level identity procedures of §7. |

## 1.2 Principles binding all implementations

- **P1 — Independent oracles.** No test may verify the optimizer using the optimizer's own scoring path. The independent evaluator, hand-verified micro-instances, and CP-SAT references are the three oracle classes; every quality or correctness claim traces to at least one.
- **P2 — Feasibility is absolute.** A single hard-constraint violation (H1–H10 as configured) in any returned solution is a release-blocking failure regardless of objective score. There is no "mostly feasible."
- **P3 — Certified degradation.** Unseated verdicts and advisory warnings are *outputs to be validated*, not excuses: every certification must be independently re-checked (Model V2), and stress tests (§4) assert the *correct* degradation, not merely non-crash.
- **P4 — Determinism is byte-level.** "Approximately the same" is non-determinism. Floating-point tolerance exists only inside a single run's internal consistency checks (§9.5), never between runs.
- **P5 — Frozen corpus, versioned goldens.** Benchmarks are permanent artifacts under version control; golden outputs change only through the controlled refresh procedure (§10.6) with recorded justification.
- **P6 — Comparable by construction.** Every optimizer variant, present or future, is measured on the identical corpus, identical independent evaluator, identical budget accounting (§13); no bespoke benchmarks for new algorithms.

---

# 2. Functional Correctness Tests

Organization: each test family runs at three scales — **micro** (≤ 12 guests, hand-verified expected output), **property** (randomized instances with asserted invariants), and **corpus** (the §3 suite through the Phase 4 gate). "Pass criteria" below are per-run; the suite passes only if every run passes.

## 2.1 Assignment integrity (H1, I1)

- **Tested:** every eligible guest appears exactly once in the output — one table or one certified unseated verdict; no duplicates; no omissions; atomic units co-located; H7 pairs co-located or co-unseated.
- **Why:** the historical duplicate-suggestion bug class; integrity failures corrupt everything downstream (cards, check-in, day-of).
- **Pass:** output guest multiset = eligible guest set exactly; unit co-location holds for every unit.
- **Failure diagnostics:** the offending guest ids, their unit ids, the last-accepted-move tag for each (Design §8), and the phase in which integrity broke (bisected by replaying the recorded move sequence).

## 2.2 Eligibility (Model §1.6, D1)

- **Tested:** exactly the guests with `status ≠ HIDDEN ∧ rsvpStatus ≠ DECLINED` (∧ `¬pendingIntakeReview` when D1 = exclude) enter $G$; nobody else appears anywhere in the output.
- **Why:** D1 changes live behavior; a mismatch silently seats guests the operator believes are quarantined.
- **Pass:** set equality between computed $G$ and the fixture's expected eligibility list, under both D1 settings.
- **Diagnostics:** the symmetric difference, with each guest's status triple.

## 2.3 Capacity (H2, I2)

- **Tested:** $n_t \le c_t$ including fixed assignments; micro-instances where demand exactly equals capacity (all tables must fill), exceeds by one (exactly one certified unseat), and where fixed assignments pre-consume capacity.
- **Why:** off-by-one against `seedTableUsage`-style pre-occupancy is the classic regression.
- **Pass:** no table over capacity; the demand-exceeds-by-one micro yields exactly one unseated guest and it is the lowest-$\omega_g$-priority one consistent with feasibility.
- **Diagnostics:** per-table $n_t$, $c_t$, $n^{\text{fixed}}_t$, member list.

## 2.4 Accessibility (H4)

- **Tested:** with $e_1 = 1$, no accessibility-needs guest at a non-compatible table; the `"{}"` sentinel is treated as *no needs* (Model's acc(·) definition); with $e_1 = 0$, the constraint is inert; exhaustion yields the specific accessibility cause.
- **Why:** protected-population constraint; also the sentinel is an easy parser regression.
- **Pass:** zero violations; sentinel micro places the `"{}"` guest at a non-compatible table when optimal; exhaustion micro certifies the correct cause.
- **Diagnostics:** violating (guest, table) pairs with raw field values.

## 2.5 MUST groups (H6, D2)

- **Tested:** every MUST component co-located or fully unseated with the D2 cause; transitive closure correctness (chains A–B, B–C ⇒ {A,B,C} atomic); null-target edges contribute tags only, never fusion; a micro where the group fits only after an ejection chain (Phase 2 must seat it).
- **Why:** D2 is a deliberate behavior change from the live engine; the silent-split regression is the single most likely conformance failure.
- **Pass:** no partial placement of any component in any run of any suite; the ejection micro seats the group.
- **Diagnostics:** component membership, admissible-table capacities, the N9 chain search record (chains attempted, terminal blockers).

## 2.6 SEPARATE (H5)

- **Tested:** no SEPARATE pair co-seated; symmetry regardless of edge direction; interaction micro where a SEPARATE edge crosses two households that both prefer the same table (one must yield); clique pre-detection (a k-clique with < k admissible tables produces the pre-run warning).
- **Why:** these edges encode divorced parties and rival branches — a violation is a social incident at a live event, the highest-severity soft failure the product can produce.
- **Pass:** zero co-seatings; warning emitted on the clique fixture.
- **Diagnostics:** the pair, both members' full placement context, and whether the violating state ever existed transiently (it must not — feasible-only search).

## 2.7 Household constraints (H7 + $f_{\text{HH}}$ structure)

- **Tested:** households of two never split (co-located or co-unseated with review flag); split households larger than any table produce chunked placements with correct split flags and $f_{\text{loc}}$ participation; the 8-member micro where 5+3 beats 4+2+2 in broken-pair count returns the 5+3 shape (or better).
- **Why:** validates both the hard floor (H7) and that the normalized pair-counting objective drives the intended split shapes.
- **Pass:** as stated; the partition micro's broken-pair count ≤ the hand-computed optimum.
- **Diagnostics:** cluster chunk composition per table, broken-pair accounting.

## 2.8 Zone / segment restrictions (H8, D3)

- **Tested:** segmented guests only at tables in allowed zones; zoneless tables inadmissible for segmented guests; unsegmented guests unrestricted; household-level segment fallback resolution; advisory caps produce spillover notices without unseating (D3 = advisory) and hard unseating when configured hard.
- **Pass:** zero violations; correct notice/unseat behavior per D3 setting.
- **Diagnostics:** guest's resolved segment, resolution path (guest-level vs household-level), allowed zones, candidate list.

## 2.9 Attendance chance constraints (H10)

- **Tested:** with priors active, every table satisfies the surrogate slack; small tables (< 8 units) use the exact Poisson-binomial tail (fixture with a 4-seat table where surrogate and exact disagree — exact must govern); $\pi_s \equiv 1$ or $\varepsilon = 0$ reproduces the deterministic feasible set exactly (Model V4 backward-compatibility check); household-as-unit variance (a 6-member household at one table must be modeled as one Bernoulli unit — the fixture is constructed so per-guest independence would pass the slack check but unit-level correctly fails it).
- **Why:** the correlation fixture is the test that distinguishes a correct implementation from a plausible-looking wrong one.
- **Pass:** slack non-negative everywhere; the correlation fixture refuses the over-packed placement; the degenerate configuration is placement-identical to a no-priors run.
- **Diagnostics:** per-table $\mathbb{E}[N_t]$, $\operatorname{Var}[N_t]$, slack, unit composition.

## 2.10 Fixed assignments (A5, I4)

- **Tested:** pre-existing assignments byte-identical in output; their capacity and expected-occupancy contributions counted; social terms see fixed guests as tablemates (a movable guest with a PREFER edge to a fixed guest is drawn to that table); no move ever proposes a fixed guest.
- **Pass:** identity on fixed set; the PREFER-to-fixed micro places the movable guest correctly.
- **Diagnostics:** any moved fixed guest with the move record that touched it.

## 2.11 Candidate table correctness (Phase 0)

- **Tested:** for a sample of guests per run (100%, on micro instances), the precomputed candidate list equals the brute-force evaluation of H3/H4/H8 over all tables — no false inclusions (would admit violations) and no false exclusions (would strand guests and corrupt certifications).
- **Why:** every downstream feasibility conclusion, including unseated certification, is only as sound as these lists.
- **Pass:** set equality per sampled guest.
- **Diagnostics:** the differing tables with the constraint evaluation trace for each.

## 2.12 Deterministic construction (Design §3)

- **Tested:** Phase 1 output identical across repeated runs and across platforms; identical after a semantics-preserving input permutation (shuffled row order of guests/tables/edges must not change output — the canonical total orders, not input order, govern).
- **Why:** input-order sensitivity is the classic hidden nondeterminism; database row order is not stable.
- **Pass:** byte-identical Phase 1 snapshots.
- **Diagnostics:** first divergent placement item and the tie-break comparison record at that decision.

## 2.13 Deterministic optimization (V3)

- Covered in full by §7; the functional suite includes one smoke-level double-run identity check per corpus instance so nondeterminism is caught in the cheapest suite first.

---

# 3. Benchmark Dataset Suite

## 3.1 Corpus rules

Synthetic but Lagos-realistic: generated from parameterized distributions reflecting the operational patterns in the platform (household-centric lists, aso-ebi tag groups, VIP tiers up to PRINCIPAL, titled/ceremonial roles, 30–50% non-RSVP mass, entrance/high-table anchor layouts). Every instance is generated once from a recorded generator seed, then **frozen as data** — the generator is documentation, the files are the corpus. Each instance ships with: the input snapshot, the resolved D1–D6 config, the run seed(s), the reference outputs (§10), and for ≤ 300 guests the CP-SAT exact reference score. No instance derives from real guest data; names and phones are synthetic (the corpus lives in repositories and CI logs).

## 3.2 Standard corpus

| Id | Guests | Tables | Rel. density (edges/guest) | Households | VIP mix | Attendance priors | Accessibility | Expected difficulty |
|---|---|---|---|---|---|---|---|---|
| **S1** "Intimate dinner" | 25 | 4 × 8 (incl. 1 HEAD_TABLE) | 1.5 (dense PREFER, 1 SEPARATE) | 8 (sizes 1–4) | 1 PRINCIPAL, 2 PRIORITY | off ($p \equiv 1$) | 1 guest, 2 compatible tables | Trivial feasibility; tests exactness of small-instance optimality (CP-SAT gap must be ~0) |
| **S2** "Board luncheon" | 60 | 8 × 8 | 0.8 | 20 | 6 VIP, VIP zone of 2 tables | ACCEPTED-heavy (π ≈ 0.9) | 3 guests | Low; VIP zone contention |
| **S3** "Family celebration" | 100 | 12 × 10 (2 zones) | 1.2, 3 tag groups | 30 (two of size 9 — must split) | 8 VIP | mixed (0.85 / 0.55) | 4 | Low-medium; first split-shape and locality pressure |
| **M1** "Society wedding A" | 180 | 20 × 10 (3 zones + head table) | 1.8, 5 aso-ebi tags (sizes 12–25) | 55 | 15 VIP incl. 2 PRINCIPAL | 0.85 / 0.5 by status | 6 | Medium; canonical mid-size, CP-SAT reference available |
| **M2** "Corporate gala" | 250 | 28 × 9, segments (staff / sponsors / general) | 1.0, heavy segments | 70 | 20 VIP concentrated in one segment | 0.9 / 0.6 | 8, clustered in one zone | Medium; segment + accessibility interaction |
| **M3** "Chieftaincy investiture" | 300 | 32 × 10 incl. head table + FRONT band | 2.0, precedence ranks on 40 titled guests, row preferences | 85, several compounds of 8–12 | 25 VIP, elder-heavy | 0.8 / 0.45 | 10 | Medium-high; the $f_{\text{prec}}$ / $f_{\text{anchor}}$ / $f_{\text{row}}$ showcase; last CP-SAT-referenced size |
| **L1** "Grand wedding" | 500 | 55 × 10 | 1.6, 8 tag groups | 150 | 35 VIP | 0.85 / 0.5, ε = 0.05 | 12 | High; the primary product-scale benchmark |
| **L2** "Festival banquet" | 750 | 80 × 10, 5 zones, dispersion pairs (D5) | 1.4, 2 rival-branch SEPARATE clusters | 220 | 40 VIP | 0.8 / 0.4, overbooking active | 18 | High; H9 + H10 under load |
| **L3** "State occasion" | 1000 | 105 × 10 | 1.8 | 300 | 60 VIP, 4 PRINCIPAL | mixed, ε = 0.05 | 25 | High; the headline scalability instance |
| **XL1** "Mega convocation" | 1500 | 155 × 10 | 1.2 | 450 | 70 VIP | 0.75 / 0.4 | 30 | Very high; memory + budget pressure |
| **XL2** "Stadium reception" | 2000 | 205 × 10 | 1.0 | 600 | 80 VIP | 0.7 / 0.35, heavy overbooking | 40 | Very high; the Design §9 outer projection point |

Each instance also exists in a **variant matrix**: {priors on/off} × {D2 all-or-unseat / legacy-relaxed} × {default weights / single-rule weights}, so configuration interactions are exercised on realistic data, not only on stress fixtures.

---

# 4. Synthetic Stress Tests

Adversarial fixtures asserting *correct degradation* (P3). For every case: the run must terminate within budget, return a feasible solution, and produce the named diagnostics — silent success on an impossible instance is itself a failure.

| Case | Construction | Why difficult | Expected behavior | Acceptable degradation | Required diagnostics |
|---|---|---|---|---|---|
| **X1 Impossible MUST group** | 14-member component, max table 12 | D2 forbids the easy out | Whole component certified unseated after N9 exhaustion | Only that component unseated; rest unaffected | Cause "must-group exceeds every admissible table," min capacity delta to admit it |
| **X2 Oversized households** | Several households of 15–25, tables of 10 | Split-shape optimization under pair-count objective | Chunked, adjacent (high $f_{\text{loc}}$), split flags set | Broken pairs ≤ hand-derived optimum + 10% | Chunk maps, locality distances |
| **X3 Dense conflict graph** | 40 guests, SEPARATE density 0.3 (near k-coloring) | Feasibility is the hard part | Feasible coloring found or minimal certified unseats | Unseated ≤ brute-force minimum (computable at this size) | Pre-run clique warnings, per-guest blockers |
| **X4 Conflicting preferences** | A PREFER B, B SEPARATE C, C PREFER A triangles at scale | Soft/hard interplay; no perfect solution exists | Hard always wins; $f_{\text{pref}} < 1$ by exactly the infeasible-edge mass | Satisfied-preference count = instance max (computable) | Per-edge satisfaction ledger |
| **X5 Extreme VIP concentration** | 60% of guests VIP, VIP zone = 1 table | Saturation term ν under stress | No pathological pile-up; VIP mass spread with graceful $f_{\text{VIP}}$ | $f_{\text{VIP}}$ ≥ hand-derived ceiling − 10% | VIP distribution per table |
| **X6 Accessibility bottleneck** | 12 accessibility guests, 1 compatible table of 8 | Guaranteed shortfall of 4 | Pre-run propagation warning; exactly 4 certified unseats, lowest-priority consistent with feasibility | No compatible seat left empty while an accessibility guest is unseated | Shortfall count, flag-change suggestion |
| **X7 Heavily restricted zones** | 6 segments, near-exact zone capacities, one segment over by 3 | Segment starvation | Pre-run warning; 3 certified unseats with segment cause (D3-hard) or spillover notices (D3-advisory) | Exact overflow count, no cross-segment leakage | Segment demand/capacity table |
| **X8 Sparse attendance** | π ≈ 0.3 across the board | $f_{\text{fill}}$ vs. social terms tension | N10 drains tables; used tables meet fill targets | $\tilde n_t / c_t \ge 0.6$ on used tables | Drain decisions, expected occupancy per table |
| **X9 High overbooking** | Expected demand ≈ 1.3 × physical capacity, ε = 0.05 | H10 infeasibility at instance level | Pre-run feasibility check fires; expected-value priority seating; surplus certified | The reported "feasible ε" is correct (re-checkable) | The ε-frontier figure, per-table slack |
| **X10 Disconnected venue** | Two table clusters 10× canvas apart, anchors in one | $f_{\text{loc}}$/$f_{\text{anchor}}$ geometry extremes | Close-rank guests concentrate near anchors; split clusters avoid straddling the gap | No split cluster spans components unless capacity forces it | Locality sums per split cluster |
| **X11 Extreme tag clustering** | One tag on 80% of guests | Tag term degeneracy — near-constant term | No blowup; other terms decide; $f_{\text{tag}}$ near its structural max | Runtime within 1.5× the same-size corpus instance | Tag-mass accounting |
| **X12 Pathological symmetry** | Identical tables, interchangeable guests, flat weights | Plateau landscape; tests tabu overlay and tie-breaks | Deterministic outcome; no thrash (acceptance telemetry shows plateau handling) | Budget consumed without pocket oscillation | Acceptance curve, tabu hit rate |
| **X13 Impossible precedence** | High-precedence guests segment-locked to FAR zones | $f_{\text{prec}}$ is soft — "impossible" means structurally unsatisfiable, not infeasible | Hard constraints honored; $f_{\text{prec}}$ at its structural ceiling; *no* unseating caused by a soft term | $f_{\text{prec}}$ = computable ceiling ± 5% | Discordance ledger naming the hard-constraint blockers |
| **X14 Impossible segment allocation** | A segment whose allowed zones contain zero tables | Empty candidate lists for a population | Phase 0 detects; certified unseats with segment cause; instance-level warning | Zero wasted search on the doomed population | The empty-candidate-list report |
| **X15 Degenerate instances** | Zero tables / zero guests / all tables locked / all weights zero | Edge-of-domain | Valid empty or trivial run with instance-level diagnostic (Design §11.8–11.9) | No crash, no hang | The specific degeneracy named |

---

# 5. Objective Quality Evaluation

All metrics computed by the **independent evaluator** (P1) on final solutions. Normalized terms are read directly; thresholds are per-instance-class (trivial instances must hit near-ceiling; adversarial instances are governed by §4's degradation bounds instead).

| Metric | Definition | Threshold (standard corpus) |
|---|---|---|
| **Objective score** $F$ | Model §6, independently recomputed | Must equal optimizer-reported $F$ exactly (V2); quality judged via gap metrics below |
| **Optimality gap** | $1 - F / F^{\text{CP-SAT}}$ where exact reference exists (≤ 300 guests) | ≤ 5% (Model V5); S1 ≤ 0.5% |
| **Bound gap** | $1 - F / \text{UB}$ (Phase 0 structural upper bound) elsewhere | ≤ 15% on L/XL; tracked, trend-monitored |
| **Per-objective contributions** | Each $w_k f_k$ and each $f_k \in [0,1]$ | Reported always; thresholds per term below |
| **Household cohesion** | $f_{\text{HH}}$: realized / possible household pairs | ≥ 0.95 when no household exceeds max table capacity; else ≥ structural ceiling − 5% |
| **Relationship satisfaction** | $f_{\text{pref}}$: satisfied PREFER edges / total | ≥ 0.9, or = instance-max on conflict fixtures |
| **VIP satisfaction** | $f_{\text{VIP}}$ mean per Model §5.3 | ≥ 0.8 where VIP zone capacity ≥ VIP demand |
| **Attendance utilization** | $\sum_t \tilde n_t / \sum_t \mathbb{E}$-effective capacity over used tables | ≥ 0.75 with priors active |
| **Table utilization** | $f_{\text{fill}}$; plus count of used tables below drain threshold | $f_{\text{fill}} \ge 0.85$; zero sub-threshold used tables after N10 |
| **Precedence satisfaction** | $f_{\text{prec}}$ = 1 − normalized discordance | ≥ 0.9 on M3-class; = structural ceiling ± 5% on X13 |
| **Locality** | $f_{\text{loc}}$ over split clusters | ≥ 0.7; no split cluster spanning > 3 tables unless size-forced |
| **Anchor quality** | $f_{\text{anchor}}$ mean | ≥ 0.75 on anchor-bearing instances |
| **Row preference** | $f_{\text{row}}$ match rate | ≥ 0.9 where band capacity suffices |
| **Cluster cohesion** | $f_{\text{tag}}$ | ≥ 0.8, or structural ceiling − 5% where groups exceed tables |
| **Fairness** | Distribution of per-guest decomposition totals $\sum_k \delta_k(g)$: report Gini and the bottom-decile mean | Bottom-decile mean ≥ 0.25 × overall mean; no guest with a *feasible strictly-better* table left in place at run end (spot-audited on samples) |
| **Unseated penalty** | Count and $\sum \omega_g u_g$; split into certified-infeasible vs. other | Non-certified unseats = **0** (release-blocking); certified count = instance-known value on stress fixtures |
| **Overall optimization score** | For dashboards: $F / \text{UB}$, alongside — never instead of — the vector above | Trend-monitored; a composite scalar never gates a release by itself |

**Interpretation discipline:** thresholds bind on the standard corpus with default configuration. A threshold miss is a quality regression to be investigated per §14, and blocks release only via §10's criteria — but a P2/P3 violation (infeasibility, uncertified unseat) blocks unconditionally.

---

# 6. Performance Benchmarks

Measured on a pinned reference environment (fixed container image and hardware class, recorded alongside results — absolute numbers are environment-relative; *ratios and complexity trends* are the portable claims). Default budgets from the Design §6; cold-cache (Phase 0 included) unless stated.

**Per-size targets (total wall time, default budget, standard corpus):**

| Guests | Construction | Repair | Improvement | Finalization | **Total** | Peak memory |
|---|---|---|---|---|---|---|
| 100 | ≤ 50 ms | ≤ 20 ms | ≤ 400 ms | ≤ 30 ms | **≤ 0.5 s** | ≤ 64 MB |
| 300 | ≤ 120 ms | ≤ 50 ms | ≤ 1.2 s | ≤ 80 ms | **≤ 1.5 s** | ≤ 128 MB |
| 500 | ≤ 200 ms | ≤ 80 ms | ≤ 2.0 s | ≤ 150 ms | **≤ 2.5 s** | ≤ 192 MB |
| 1000 | ≤ 500 ms | ≤ 150 ms | ≤ 5.0 s | ≤ 350 ms | **≤ 6 s** | ≤ 384 MB |
| 2000 | ≤ 1.2 s | ≤ 300 ms | ≤ 12.5 s | ≤ 1.0 s | **≤ 15 s** | ≤ 768 MB |

**Rate and efficiency targets (all sizes):**

- **Average move evaluation cost:** amortized over Phase 3, within 3× of the smallest-size instance's per-move cost — near-flat per-move cost across sizes is the empirical signature of correct O(deg + tags) incremental scoring; superlinear growth means a hidden full scan.
- **Moves per second:** ≥ 300k proposals/s at 1000 guests on the reference environment (consistent with the 2M-proposal budget inside the 5 s improvement envelope).
- **Cache efficiency:** debug-mode consistency sampling (Design §10) enabled at its default rate must cost ≤ 15% overhead; zero consistency failures.
- **Scalability law:** across the size ladder, total improvement time within ±30% of linear in the proposal budget, and Phase 0 within ±30% of linear in $n \cdot m$ — deviations are investigated as complexity regressions even when absolute targets still pass.
- **Warm-start mode** (Design §12 dynamic arrivals): re-run at 1000 guests with ≤ 5% changed statuses and a quarter budget completes ≤ 1.5 s.

---

# 7. Determinism Verification

**Claim under test (V3):** identical instance + identical seed + identical resolved configuration (D1–D6, weights, budgets, ε) ⇒ identical run, byte for byte.

**Procedures:**

1. **Double-run identity:** every corpus instance twice per CI cycle; hash-compare the full output bundle.
2. **Cross-platform identity:** the same runs on at least two OS/architecture combinations per release. Floating-point reproducibility across platforms is a *conformance requirement on implementations* (fixed evaluation order, no platform-variant fast-math); a cross-platform mismatch is an implementation bug by definition, not an accepted tolerance.
3. **Input-order insensitivity:** shuffled-row variants of each instance (semantics identical, storage order permuted) must produce identical outputs — canonical total orders, not I/O order, must govern (ties to §2.12).
4. **Seed sensitivity (sanity inverse):** different seeds must be *allowed* to differ; a suite where 32 seeds produce identical improvement trajectories on a plateau-free instance indicates the seed isn't wired through, which is its own failure.
5. **Config-echo check:** the recorded run config in the output must equal the resolved input config; determinism claims are only meaningful over the *recorded* tuple.

**What must be byte-identical:** final assignments and unseated verdicts; all reported scores and per-term numerators; the accepted-**move sequence** (recorded as an ordered log of (iteration, move type, endpoints) — proposal-level identity is implied by stream determinism and is spot-verified on micro instances); every decomposition $D(g)$; the complete diagnostics package; telemetry *content* (counters, curves, event logs). **Exempt:** wall-clock timestamps and duration fields, which are segregated into a non-hashed envelope so the hashable bundle is total.

---

# 8. Explainability Validation

The claim is never "explanations look reasonable"; it is "explanations are arithmetic consequences of the returned solution."

1. **Decomposition exactness (V2):** for every placed guest, $\sum_k \delta_k(g, \text{tab}(g) \mid x_{-g}) = \Delta F$ of the counterfactual unseat, recomputed by the independent evaluator, within $10^{-9}$. Every guest, every run — not sampled.
2. **Whole-solution reconstruction:** $\sum_{g} \sum_k \delta_k(g)$ under symmetric half-credit, plus the unseating penalty, must reconstruct $F$ exactly. This catches double-counted pairwise credit that per-guest checks miss.
3. **Pairwise contribution audit:** for sampled satisfied PREFER edges and household pairs, each endpoint's decomposition carries exactly half the edge value; for sampled *unsatisfied* edges, zero appears in both.
4. **Unary contribution audit:** anchor/row/VIP-zone components in $D(g)$ equal the pretabulated (guest, table) values for the final table — verifying the decomposition reads the same pretable the optimizer used.
5. **Counterfactual explanations:** for sampled guests, "why table 7 over table 12" — the delta-vector difference between the two placements — must be reproducible by the independent evaluator, and the sign must agree with the optimizer's final preference unless a hard constraint blocks table 12 (in which case the explanation must cite the blocking family, and re-checking must confirm it).
6. **Certification re-check:** every unseated verdict's cause independently re-verified: all candidate tables re-tested against the named family; a certification that doesn't re-verify is release-blocking.
7. **Structural annotation truth:** split flags match actual multi-table clusters; spillover notices match actual cap excess; move-provenance tags match the recorded accepted-move log.
8. **No-divergence-by-construction check:** a static conformance requirement — the implementation must attest (and code review must confirm) that finalization decompositions invoke the identical delta evaluators used in Phase 3, per the Design §8; a second scoring implementation anywhere except the independent evaluator is a conformance violation even if its numbers currently agree.

---

# 9. Incremental Scoring Verification

Proves Design §5's central claim: incremental deltas ≡ full recomputation, always.

1. **Random move testing:** on each corpus instance, generate seeded random feasible moves of every neighborhood type; for each, compare the incremental delta vector against (full recompute after − full recompute before). Per-CI volume: ≥ 10⁵ moves across the suite.
2. **Mass randomized swaps:** a dedicated soak mode executes ≥ 10⁷ accepted random moves on M1/L1-class instances with full-recompute checks sampled every 10³ moves and cache-derivation checks every 10⁴; the terminal state must pass a complete I5/I6 audit. Run nightly, not per-commit.
3. **Cache corruption detection:** fault-injection tests deliberately corrupt one cached value (a tag histogram count, an occupancy, a variance accumulator) and assert the debug-mode consistency sampler *detects* it within its sampling window — validating the detector itself, not just the absence of bugs.
4. **Delta verification per neighborhood:** every N1–N11 move type has micro fixtures where the exact delta of every term is hand-derived, including the tricky corrections: mutual-term correction in swaps (N2/N4/N6), first/last-member cluster-table-set transitions in $f_{\text{loc}}$, the (numerator, count) denominators of $f_{\text{fill}}$/$f_{\text{loc}}$ when a table empties or a split heals, and the $\omega_g P_u$ penalty on seat/unseat transitions.
5. **Floating-point discipline:** within-run comparisons use absolute tolerance $10^{-9}$ on normalized quantities; additionally, a drift test asserts that after the 10⁷-move soak, |cached $F$ − recomputed $F$| < $10^{-6}$ — bounding accumulation error, and motivating (but not mandating) exact integer numerator arithmetic where the term structure permits.
6. **State consistency:** after every accepted move in debug mode (sampled schedule per Design §10), member sets, histograms, occupancy, E/Var accumulators, cluster table sets and locality sums re-derived and compared (I6).
7. **Rollback validation:** rejected moves must leave zero trace — state hash before proposal = state hash after rejection, asserted continuously in the soak. Pocket-revert (intensification) and soft-restart transitions must land on states whose caches pass a full I5/I6 audit.

---

# 10. Regression Suite

1. **Golden corpus:** the full §3 suite plus all §4 stress fixtures, frozen (P5).
2. **Golden outputs:** for each (instance, seed, budget, resolved config) tuple: the hashable output bundle of §7 — assignments, scores, per-term numerators, decompositions, diagnostics, accepted-move log. Stored as content-addressed artifacts; CI compares hashes, storing full bundles for diffing on mismatch.
3. **Golden objective scores:** tracked independently of the byte hash so a *quality* regression (lower $F$) is distinguishable from a *behavior* change (different but equal-quality solution).
4. **Golden explanations & diagnostics:** included in the bundle hash; certification causes and warning sets are part of the contract, not decoration.
5. **Runtime thresholds:** §6 per-phase and total targets enforced with a 20% CI-noise allowance on wall time (never on memory); three consecutive breaches of the same threshold = regression even within allowance.
6. **Golden refresh procedure:** goldens change only when an intentional algorithm/config change lands. Refresh requires: the change's design rationale, a full quality report (old vs. new on every §5 metric, every instance), explicit note of any instance whose $F$ decreased and why that is accepted, and Principal sign-off recorded in the corpus changelog. Silent golden updates are prohibited; CI must fail if goldens and optimizer version claims are inconsistent.
7. **Acceptable score deviation:** none, for a given optimizer version — determinism makes scores exact. Across versions, deviations are governed by the refresh procedure; a new version may not decrease $F$ on more than 10% of standard-corpus instances nor by more than 2% on any single instance without explicit acceptance in the refresh record.
8. **CI tiers:** *per-commit* — micro suites, functional correctness on S/M instances, double-run determinism smoke, incremental spot checks; *nightly* — full corpus with goldens, stress suite, the 10⁷-move soak, cross-platform determinism; *release* — everything, plus CP-SAT gap re-measurement, performance suite on the reference environment, and the comparative report (§13) if an optimizer change is shipping.
9. **Release-blocking criteria:** any P2 violation; any non-certified unseat; any determinism failure; any decomposition exactness failure; any golden mismatch without a signed refresh; memory threshold breach; CP-SAT gap > 5% on any referenced instance; stress-suite behavioral deviation from §4's expected-behavior column.

---

# 11. Weight Calibration Methodology

**Scope note:** operator-facing weights are ordinal (the rule-order → integer rank mapping is frozen in the Model). Calibration therefore has two distinct objects: (a) the **structural parameters** — saturation $\nu$, distance scale $d_0$, fill target $\beta$, default ε, drain threshold — which are fixed constants of the product, and (b) validation that the **rank semantics** behave as operators expect ("rank 1 beats rank 2" must be *visible* in outcomes). Per-event weight tuning by hosts is a product feature, not a calibration target.

1. **Offline calibration:** grid/Latin-hypercube sweeps of structural parameters over the standard corpus, scoring each configuration on the §5 metric vector. Selection is by dominance on the vector, not a scalar collapse.
2. **Historical replay:** completed events (starting with the Alakija dataset post-event) replayed as instances: reconstruct the frozen pre-event state, run the optimizer, and compare against the *actual* human-finalized seating on the §5 metrics plus an edit-distance measure (moves needed to transform optimizer output into the human plan). The human plan is a reference point, not ground truth — divergences are classified as optimizer-better / human-better / equivalent by expert review (below).
3. **Sensitivity analysis:** one-at-a-time and pairwise perturbation of each structural parameter and each rank position; report $\partial f_k / \partial \theta$ profiles per instance class. Flat sensitivities identify dead parameters; cliff sensitivities identify fragile defaults — both are findings requiring action.
4. **Pareto frontier analysis:** for the principal tensions (household cohesion vs. tag cohesion; fill vs. social terms under priors; anchor vs. row), sweep the relevant weight ratios and plot the achievable frontier per instance class. Default rank ordering must sit on or near the frontier; a dominated default is a calibration bug.
5. **Weight perturbation (monotonicity, Model V4):** raising $w_k$ with others fixed must not decrease $f_k$ at the returned optimum, at adequate budget, across the corpus — run as part of calibration and as a nightly property test.
6. **Cross-validation:** structural parameters chosen on a calibration half of the corpus must hold their §5 thresholds on the held-out half; per-instance-class overfitting of defaults is rejected.
7. **Expert review:** structured review by Maison Doclar planners on rendered seating charts of replayed and synthetic events — blind A/B between configurations where feasible, scored on a fixed rubric (protocol correctness, family grouping, aso-ebi presentation, front-of-room composition). Expert findings feed threshold revisions through the golden refresh procedure, never ad-hoc.
8. **Objective evaluation of any weight change:** a proposed change ships with: frontier position, sensitivity profile, corpus-wide metric deltas, replay edit-distance deltas, and expert rubric scores. The decision record lives with the golden refresh (§10.6).

---

# 12. Production Telemetry

Every production run emits a structured record (extending the Design §11 diagnostics package):

- **Runtime:** per-phase durations; total; budget consumed vs. granted; warm/cold start flag; instance shape (n, m, edge count, unit count, split-cluster count).
- **Search dynamics:** acceptance rate per epoch; temperature curve; reheat and soft-restart events; pocket-improvement timeline (iteration, ΔF); final bound gap $1 - F/\text{UB}$.
- **Move statistics:** proposals / accepts / rejects per neighborhood; rejection reasons by constraint family; adaptive-weight trajectory; tabu hit and aspiration counts.
- **Cache statistics:** debug-sampler executions and outcomes (must be zero failures); memoized Poisson-binomial hit rate; peak memory.
- **Constraint activity:** per-family rejection counts; pre-run propagation warnings fired; H10 slack distribution at completion.
- **Repair:** unseated-in / seated-by-repair / certified-out counts; chain depth distribution; per-cause certification tallies.
- **Objective evolution:** $F$ per epoch; per-term $f_k$ start vs. end; unseated penalty trajectory.
- **Outcome summary:** the full §5 metric vector; warning frequencies by type; the resolved config echo and seed.

**Operational use — alert conditions:**

- *Correctness sentinels (page immediately):* any cache-sampler failure; any non-certified unseat; any Phase 4 gate failure; config echo mismatch.
- *Quality drift (dashboard + weekly review):* bound gap trending up at fixed budget across comparable instance shapes; per-term $f_k$ distributions shifting vs. corpus baselines; certification causes rising for a constraint family (often means real-world data drift — e.g., accessibility flags not being maintained on venues).
- *Performance drift:* per-move cost rising at fixed instance shape (the incremental-scoring signature metric, §6); Phase 0 time growing superlinearly (schema/data bloat); memory creep.
- *Search-health signals:* acceptance rate pinned near zero from epoch 1 (temperature calibration broken on this instance shape); pocket flat while acceptance is healthy (plateau — candidate for budget or neighborhood-mix review); restart frequency rising (instances outgrowing default budgets).
- *Product signals:* warning frequencies by event — a spike in segment-starvation warnings across events usually indicates an operator-workflow problem (zone maps drawn too tight), not an optimizer problem; telemetry should make that distinction visible rather than absorbing it.

---

# 13. Comparative Evaluation Framework

For fairly judging Greedy-only, Tabu, SA (incumbent), LNS, CP-SAT/OR-Tools, genetic, and hybrid candidates — now and later.

1. **Common everything (P6):** identical frozen corpus and stress suite; identical independent evaluator computing $F$ and the §5 vector; identical D-config; identical output-bundle format so §7/§8 checks apply to every candidate.
2. **Runtime normalization:** two protocols, both reported — *equal-budget* (identical proposal counts where the paradigm admits them; otherwise equal wall time on the reference environment, with the wall-clock nondeterminism caveat recorded) and *quality-target* (time to reach the incumbent's $F$, or DNF). Anytime candidates additionally report quality-vs-time curves at fixed checkpoints (10%, 25%, 50%, 100% of budget). Exact solvers (CP-SAT) report time-to-proof and time-to-incumbent-quality separately.
3. **Quality comparison:** per-instance paired differences in $F/\text{UB}$ (and $F/F^{\text{CP-SAT}}$ where referenced), plus the full §5 vector — a candidate that wins scalar $F$ by sacrificing fairness or certification quality is visible, not hidden.
4. **Statistical treatment:** stochastic candidates run 31 seeds per instance; paired comparison on per-instance median and on seed-matched pairs via Wilcoxon signed-rank at α = 0.01 across the corpus, with Holm correction over instance classes; report effect size (median paired ΔF/UB) alongside significance — significance without material effect size does not justify replacement.
5. **Conformance is a precondition:** a candidate enters quality comparison only after passing §2, §7, §8, §9 in full. A faster, higher-scoring optimizer that cannot produce exact decompositions or byte-determinism is not a candidate; it is a research artifact.
6. **Reporting format:** a fixed comparative report — conformance attestation; per-class quality tables with significance and effect sizes; anytime curves; performance table (§6 format); stress-suite behavior diffs; failure-mode narrative for every instance where the candidate lost.
7. **Replacement criteria (all required):** full conformance; no P2/P3 regressions anywhere; statistically significant $F/\text{UB}$ improvement with median effect ≥ 1% corpus-wide *or* ≥ 3% on a declared target class with no class regressing > 0.5%; performance within §6 targets; stress behavior equal or better on every fixture; a signed golden refresh (§10.6). Replacement is a Principal decision on this evidence; the framework produces the evidence, not the verdict.

---

# 14. Failure Analysis

Standing diagnostic workflows, each starting from telemetry (§12) and the recorded run bundle. All workflows exploit determinism: **replay with the recorded seed reproduces the failure exactly**, which is the single most valuable debugging property the system has — every workflow below assumes it.

- **Constraint conflicts (guest unseated unexpectedly).** Pull the certification record → re-verify independently (if re-verification fails, this is a §8.6 release-blocking bug, stop) → inspect the candidate list vs. brute force (§2.11) → walk the N9 chain log for the terminal blockers → classify: data problem (wrong flags/segments on real records), instance problem (venue genuinely too small — check the pre-run propagation warnings that should have fired), or optimizer problem (repair budget/depth insufficient — reproduce with raised d, b).
- **Poor convergence (high bound gap).** Check the bound first — structural ceilings can make UB loose (X11-type instances); recompute the tightened per-term ceilings. Then the acceptance curve: pinned-low from start → temperature calibration vs. this instance shape; healthy-then-flat → plateau, inspect tabu hit rates and N11 burst outcomes; oscillating pocket → intensification parameter review. Escalate: re-run at 4× budget — if the gap closes, it's a budget-sizing issue; if not, a landscape issue → CP-SAT the instance (or a downscaled homomorph) to bound what's achievable.
- **Unexpected runtime.** Per-phase breakdown → if Phase 0: instance shape vs. pretable cost (n·m growth, tag cardinality explosion); if Phase 3: per-move cost vs. baseline — a rise at fixed shape is a hidden full-scan regression (§6 signature metric); profile the delta evaluators by neighborhood using the move statistics to identify which type degraded.
- **Cache inconsistencies.** Any sampler failure: replay with sampling every move to localize the first corrupting accepted move → identify the move type → its §9.4 micro fixtures should be extended to cover the discovered case *before* the fix lands (test-first regression capture).
- **Non-determinism.** Diff the two output bundles → find the first divergent accepted move → the divergence class is almost always one of: unordered iteration (map/set order leaking into proposals), wall-clock leakage into decisions, uninitialized tie-break, or platform floating-point divergence → each has a targeted check in §7; add the failing instance to the input-order-shuffle suite permanently.
- **Low-quality solutions (metrics pass, output looks wrong).** First establish which claim fails: recompute the §5 vector — if metrics are genuinely fine but the chart looks wrong to a planner, the gap is in the *model's* objective coverage, not the optimizer; route to weight calibration (§11.7 expert rubric) and, if confirmed, to a Model change proposal (new term via the Design §12 contract) — never to optimizer hacks.
- **Operator complaints.** Intake maps the complaint to a metric or constraint: "family split up" → $f_{\text{HH}}$/chunk map for that household; "elder at the back" → $f_{\text{prec}}$/$f_{\text{row}}$ decomposition for that guest — the per-guest $D(g)$ answers "why is this guest here" directly and should be the first artifact consulted; "these two must never be together" → was the SEPARATE edge present in the input? (Most complaints in this class are data-entry gaps, and telemetry should quantify that ratio to drive product workflow fixes rather than optimizer churn.)

---

# 15. Research Roadmap

Each item below is benchmarkable inside this framework without new machinery beyond what is named — that is the framework's design intent: research proposals arrive with a §13 comparative report, or they don't arrive.

- **Dynamic attendance updates.** Warm-start re-optimization as statuses change (Design §12). Benchmark: replay sequences derived from real RSVP timelines (status-change event streams over the corpus instances); metrics = §5 vector at each checkpoint plus a *stability* metric (guests moved per update) — stability becomes a new objective-term candidate evaluated on the Pareto machinery of §11.4.
- **Multi-stage events.** The cross-stage stability term (Design §12) on two-instance chained fixtures added to the corpus; comparison = joint $F$ across stages plus transition-cost, against the naive independent-solve baseline.
- **Seat numbering (Phase 5).** A separate ordinal model with its own micro-verified protocol rules (head-table flanking, couple centring); validated by exact enumeration per table (≤ 12! is enumerable with pruning at table scale) — this framework contributes the upstream frozen table assignments and the determinism/goldens discipline. Given the investiture use case, first in the queue.
- **Vision-based venue modelling.** Automated extraction of tables/zones/fixtures from floor-plan imagery. Validated as a *data pipeline*: extraction accuracy against hand-labelled plans, then end-to-end — optimizer quality on extracted vs. hand-built venue models on the same guest data; the corpus gains image-sourced variants of M/L instances.
- **Reinforcement learning / learned heuristics.** A learned move-selection or construction policy is just another candidate under §13: full conformance (including byte-determinism at fixed policy weights and seed — a real constraint on architecture choices), then the standard comparative report. The 31-seed protocol and effect-size gate prevent benchmark-overfitting claims.
- **Adaptive objective weighting.** Learning structural parameters or per-instance-class defaults from replay and expert-rubric data (§11.2, §11.7). Evaluated by cross-validated frontier position (§11.4, §11.6); guarded by the monotonicity property test.
- **Online optimization (event-day).** Continuous re-optimization during check-in with arrivals fixed as they scan. Benchmark: simulated arrival streams (from CheckInLedger-shaped traces) over corpus instances; metrics = §5 vector at close-of-arrivals plus per-update latency (target: sub-second warm updates) plus stability.
- **Distributed optimization.** Only relevant beyond ~5000 guests or for portfolio solving (many events concurrently). Benchmark: identical corpus, wall-time-normalized against the single-process incumbent, with the determinism requirement retained per partition — a distributed candidate that cannot maintain reproducibility fails conformance regardless of speed, which is a deliberate bar.

---

*End of specification. Together with the Model and the Design, this completes the governing document set: any implementation must satisfy the Model, follow or justifiably deviate from the Design, and pass this protocol. Deviations from the Design are permitted where this protocol still passes; deviations from the Model are not permitted at all.*
