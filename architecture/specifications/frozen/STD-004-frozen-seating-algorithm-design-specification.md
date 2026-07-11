---
artifact_id: STD-004
name: Frozen Seating Algorithm Design Specification
version: frozen
status: Frozen — immutable historical evidence, constitutionally promoted
authored: Jun 2026 (principal–Claude architecture session; declared frozen at authorship)
promoted: 2026-07-11
provenance: maison-doclar-knowledge-extraction raw/historical-event-os/algorithms/ @ 9a645cc9 (attested by principal)
decision_refs: [DI-030, DI-034]
note: Original content below is verbatim and unchanged; only this metadata block is prepended.
---
# Aperture Seating Optimizer — Algorithm Design Specification

**Status:** Engineering design document. Implementation-independent. No code, no pseudocode.
**Governing model:** *Aperture Seating Allocation — Formal Optimization Specification* (approved, immutable). All symbols ($x_{g,t}$, $f_k$, $w_k$, $P_u$, $\delta_k$, H1–H10, V1–V5, D1–D6) refer to that document and are not redefined here.
**Contract:** this design describes *one admissible optimizer* for the model. Any implementation of this design must pass the model's V1–V5 validation gate; where this document and the model conflict, the model wins.

---

# 1. High-Level Architecture

The optimizer is a five-phase pipeline. Each phase transforms a well-defined state and terminates with its own postcondition, so the pipeline is inspectable between phases and any interruption after Phase 3 still yields a valid, feasible plan (anytime property).

**Phase 0 — Load & Normalize.**
Materializes all sets and parameters from the database into the runtime state (§2): atomic units are formed (MUST components and households merged — see §7), per-guest candidate table lists are precomputed from the *static* hard constraints (H3, H4, H8), normalization denominators for every $f_k$ are computed once, unary score components are pretabulated, and attendance priors are resolved per segment. **Why it exists:** every later phase must be able to evaluate feasibility and score deltas in near-constant time; that is only possible if all static structure is folded into caches up front. Postcondition: a frozen problem instance plus empty assignment.

**Phase 1 — Construction.**
A deterministic greedy heuristic (§3) produces an initial feasible solution: every guest is either placed or carries a provisional unseated verdict. **Why it exists:** local search needs a feasible starting point, and a *good* starting point shrinks the improvement budget dramatically; the current engine's phase structure (MUST groups → unified clusters → difficulty-ordered singles) is retained because it encodes correct priorities, upgraded to the model's normalized scoring and D2 semantics.

**Phase 2 — Repair.**
Targeted reinsertion of provisionally unseated guests via bounded ejection chains (§4.9), plus resolution of any chance-constraint (H10) slack violations introduced by construction rounding. **Why it exists:** greedy construction strands guests that a small rearrangement would seat — particularly MUST groups under D2's all-or-unseat rule and accessibility-needs guests late in the ordering. Repair converts "unseated because construction order was unlucky" into "seated," leaving only *certifiably* infeasible guests unseated. Postcondition: every remaining unseated verdict is certified (V2) — no single-table placement and no depth-bounded ejection chain can seat the guest.

**Phase 3 — Improvement.**
Seed-deterministic simulated annealing with a tabu overlay over the neighborhoods of §4, using incremental scoring (§5), under an iteration budget (§6). **Why it exists:** construction is myopic — it cannot trade a locally good early placement for a globally better arrangement. Improvement is where the objective gap to optimality is closed; the model's §9 establishes that the objective is smooth under these moves and that per-move evaluation is cheap, which is precisely the regime where annealing is effective.

**Phase 4 — Finalization.**
Recomputes the canonical explainability decomposition $D(g)$ for every guest against the *final* solution (§8), runs the full V1–V4 invariant gate, assembles diagnostics (household splits, spillovers, household-of-two review list, unseated causes, per-table occupancy and expected occupancy), and emits the run record. **Why it exists:** decompositions computed mid-search describe transient states; the model requires them to describe the returned solution exactly, and the validation gate must run against exactly what is returned.

Termination is always through Phase 4, whether the improvement budget expired, converged early, or the run was interrupted — the best-known feasible solution ("pocket," §6) is what gets finalized.

---

# 2. State Representation

All structures below are runtime state, not persistence schema. For each: what it stores, what is **cached** (maintained incrementally, cheap to read), what is **derived** (recomputable from primary state; caches are consistency-checked against derivation in debug mode), and what is **invariant** (must never change during a run).

## 2.1 GuestState (one per eligible guest)

- **Invariant:** identity; tag set $\Lambda_g$; closeness rank $r_g$; row preference $\rho_g$; VIP tier $v_g$; accessibility flag; atomic-unit membership; segment $\sigma(g)$; attendance prior $p_g$; unseating weight $\omega_g$; **candidate table list** — the tables passing all static constraints H3/H4/H8 for this guest, precomputed in Phase 0 and never re-filtered; the sorted lists of SEPARATE partners and PREFER partners.
- **Cached:** current location (table id or UNSEATED); tabu expiry iteration (§6).
- **Derived:** nothing per-guest beyond location; all social context lives in TableState.

## 2.2 TableState (one per candidate table)

- **Invariant:** capacity $c_t$; zone and zone type; row band $b(t)$; entrance proximity; geometry; anchor distance $d(t,A)$; fixed-assignment occupancy $n^{\text{fixed}}_t$ and its expected-occupancy contribution.
- **Cached (the heart of incremental scoring):** member set; occupancy $n_t$; VIP member count; **tag histogram** (multiset of tags over members — the count of members carrying each tag); **household histogram** (members per household id); SEPARATE-conflict index — for each member, membership in a per-table set of "partners-present" is maintained so H5 checks are set lookups; expected occupancy $\mathbb{E}[N_t]$ and variance $\operatorname{Var}[N_t]$ accumulators over atomic units (§7.3 of the model); remaining chance-constraint slack $c_t - \mathbb{E}[N_t] - z_{1-\varepsilon}\sqrt{\operatorname{Var}[N_t]}$.
- **Derived:** everything above is derivable from the assignment; caches exist purely for O(1) move evaluation.
- **Invariant during search:** $n_t \le c_t$ and non-negative H10 slack — these are preconditions of every accepted move, never repaired after the fact.

## 2.3 ClusterState (one per unified cluster and per atomic unit)

- **Invariant:** membership; cluster type (household / tag / MUST-atomic); label; segment binding; the cluster's contribution ceiling to $f_{\text{HH}}$/$f_{\text{tag}}$ denominators.
- **Cached:** occupied-table set $T_c$; sum of pairwise inter-table distances over $T_c$ (for the $f_{\text{loc}}$ mean $\bar d(T_c)$, maintained incrementally as tables enter/leave the set); split flag; chunk composition (which members are at which table).
- **Derived:** spillover status (member count vs. segment cap $\kappa_s$ — advisory per D3).

## 2.4 MoveState (ephemeral, one per candidate move)

- Stores: move type (§4); the affected guests/units; source and destination tables; the **delta vector** $\langle \delta_k \rangle$ over all objective terms plus the unseating-penalty delta; the feasibility verdict with, on rejection, the blocking constraint family.
- Nothing in MoveState is cached beyond the evaluation; accepted moves fold their deltas into ScoreState and the touched caches, rejected moves are discarded. MoveState *is* the raw material of explainability (§8) — the same delta evaluators serve both purposes.

## 2.5 ScoreState (global, one per run)

- **Cached:** current total $F$; per-term **numerators** (e.g., count of co-seated household pairs, count of satisfied PREFER edges, tag-weighted co-seating mass, per-guest anchor/row/VIP unary sums, locality sum over split clusters, fill sum, precedence discordance count); current unseating penalty total.
- **Invariant:** per-term **denominators** — all normalizers of §5 of the model are functions of the *instance*, not the assignment (total household pairs among eligible guests, $|R_{\text{pref}}|$, total shared-tag pair mass, eligible-guest counts per unary term). Computed once in Phase 0. This is the single most important design fact for incremental scoring: **only numerators ever move.**
  - One caveat handled explicitly: $f_{\text{fill}}$'s denominator $|T_{\text{used}}|$ and $f_{\text{loc}}$'s "mean over split clusters" *do* depend on the assignment. Both are maintained as (numerator, count) pairs so the division is re-evaluated in O(1); their deltas account for count changes when a table empties or a cluster's split status flips.
- **Cached:** best-known solution pocket — a compact copy of the assignment vector plus its $F$, updated whenever the incumbent improves.

## 2.6 AttendanceState (global)

- **Invariant:** the segment prior table $\pi_s$ (loaded, already Beta–Binomial-smoothed); $z_{1-\varepsilon}$; per-atomic-unit $(m_j, p_j)$ per §7.3 of the model.
- **Cached:** nothing beyond the per-table $\mathbb{E}/\operatorname{Var}$ accumulators, which live in TableState.
- **Derived:** exact Poisson-binomial tails for small tables ($|U_t| < 8$), computed on demand and memoized per table composition.

## 2.7 ConstraintState (global)

- **Invariant:** MUST component map (already folded into atomic units — see §7); SEPARATE adjacency (symmetric, sparse); segment → allowed-zone sets; dispersion/zone-separation pair lists (H9, per D5); the D-flag configuration (D1–D6 resolved values are inputs to the run and recorded in the run config).
- **Cached:** nothing — constraint checks read GuestState invariants and TableState caches.

---

# 3. Construction Algorithm

Deterministic, no randomness, no seed dependence. Reproduces the intent of the current engine's phases with the model's corrected semantics.

**Ordering strategy.** Placement proceeds over *placement items*, ordered:

1. **MUST components**, largest first (hardest to fit; placing them into empty tables first maximizes the chance a single table fits them).
2. **Unified clusters** — household-type before tag-type, then by descending member count, then by descending internal difficulty (presence of accessibility needs, VIP mass).
3. **Remaining singles**, by descending placement difficulty exactly as the model's construction precedent defines it (accessibility contributes most, then group entanglement, then VIP order), with ties broken by the canonical name comparator.

Within any tie at every level, the total order is: difficulty ↓, member count ↓, canonical name/label comparator — no ordering ambiguity may remain (V3).

**Placement strategy.** For each item, evaluate every table on the *intersection* of the members' candidate table lists that also passes the dynamic checks (capacity for the full item, H5 against current members, H10 slack for the item's expected mass, H9 where applicable). Score each admissible table with the true objective delta $\sum_k \delta_k$ of placing the whole item there (computed from caches, §5) — construction and improvement share one scoring definition; there is no separate "construction score."

- An item that fits no single table but is *splittable* (household > 2 not bound by MUST, or a tag cluster) is placed by the **chunked locality procedure**: repeatedly place the largest chunk that fits the best remaining table, scoring each chunk placement with the objective delta *including* the $f_{\text{loc}}$ term against already-placed chunks — this subsumes the current engine's 1000/(1+d) locality bonus with the model's normalized form.
- A MUST component that fits no single table is **not split** (D2): all members are marked provisionally unseated with cause "must-group exceeds every admissible table," and the item is queued for Phase 2 repair.
- A household of two that fits nowhere together is marked for operator review (H7), never split, and excluded from repair chains that would split it.

**Constraint evaluation.** Static constraints never re-evaluated (candidate lists); dynamic checks per candidate table are O(item size): capacity and H10 slack are single comparisons against TableState accumulators; H5 is one set-membership probe per member per SEPARATE partner present.

**Tie-breaking** among equally scored tables (normative, matching the model's V3 tie-break chain): objective delta ↓, then remaining capacity ↑ (best-fit — preserves large tables for large later items), then table number ↑, then case-insensitive label.

**Expected complexity.** Each item scans at most its candidate list: $O(|G| \cdot \bar{|T_{\text{cand}}|} \cdot \bar\kappa)$ where $\bar\kappa$ is the mean per-table delta-evaluation cost (§5) — in practice tens of milliseconds at 1000 guests with warm caches, since delta evaluation is O(tags + partners), not O(placements).

**Failure handling.** Construction never violates a hard constraint and never throws on infeasibility: every unplaceable item degrades to a provisionally unseated verdict carrying the *most specific* blocking family observed (accessibility exhaustion ≻ segment starvation ≻ separation deadlock ≻ raw capacity), so Phase 2 knows what to attack and Phase 4 can certify what remains.

---

# 4. Optimization Neighborhoods

A **move** is a candidate transformation of the assignment. Every move is evaluated feasibility-first (§7), then scored as a delta vector. Atomic units (MUST components, and households treated as movement atoms) are indivisible in all moves except the explicitly splitting ones, and H7 pairs are indivisible in all moves without exception.

**N1 — Move Guest.** Relocate one singleton guest (or one guest of an already-split cluster chunk) from its table (or from UNSEATED) to another admissible table.
*Purpose:* the finest-grained gradient step; also the mechanism by which unseated guests re-enter when capacity opens. *Useful:* throughout; dominant in late intensification. *Complexity:* O(deg(g) + |Λ_g|) delta evaluation (§5). *Touched caches:* both tables' member sets, occupancy, tag/household histograms, VIP counts, E/Var accumulators; the guest's cluster occupied-table set and locality sum; per-term numerators.

**N2 — Swap Guests.** Exchange the tables of two guests at different tables.
*Purpose:* moves through capacity-tight regions where N1 is blocked (both tables full). *Useful:* mid-search at high fill; this generalizes the current engine's only improvement operator. *Complexity:* two N1 evaluations with a correction for the pair's mutual terms (they may share edges/tags). *Touched caches:* as N1 for both endpoints.

**N3 — Move Household (atomic unit).** Relocate an intact atomic unit to a table with room for all members.
*Purpose:* preserves $f_{\text{HH}}$ perfectly while optimizing the unit's unary and cross terms (anchor, row, VIP, tag context). *Useful:* whenever a unit was constructed into a mediocre table; early-to-mid search. *Complexity:* O(unit size × per-member cost). *Touched caches:* as N1 aggregated over members, plus the unit's cluster state.

**N4 — Swap Households.** Exchange two atomic units between tables when capacities permit (sizes need not match if both fit).
*Purpose:* the unit-level analogue of N2 for tight capacity. *Useful:* high-fill instances; also the natural operator for precedence repair ($f_{\text{prec}}$ inversions typically involve whole family units, not individuals). *Complexity:* two N3 evaluations plus mutual-term correction.

**N5 — Move Cluster (chunk relocation).** Relocate one chunk of a split cluster to a different table.
*Purpose:* directly optimizes $f_{\text{loc}}$ — pulls scattered chunks adjacent — and can consolidate ($f_{\text{tag}}$, $f_{\text{HH}}$) when the destination already holds sibling chunks. *Useful:* after construction on instances with large aso-ebi groups or compound families. *Complexity:* O(chunk size × per-member cost) + O(|T_c|) for the locality sum update.

**N6 — Swap Clusters.** Exchange two chunks (possibly of different clusters) between tables.
*Purpose:* N5 through capacity walls. *Complexity:* two N5 evaluations + correction.

**N7 — Split Cluster (re-chunk).** Repartition a split cluster's members across its current table set, or split an intact-but-oversized-for-its-table cluster by moving a minority chunk out.
*Purpose:* construction's chunking is size-greedy (largest chunk first); the optimal partition may be different — e.g., 5+3 versus 4+4 for a household of 8 depending on which pairs matter ($f_{\text{HH}}$ counts broken pairs, so partition shape matters). *Useful:* targeted, low-frequency; expensive relative to N1–N6. *Complexity:* O(cluster size²) in the worst repartition evaluation, bounded by restricting to clusters ≤ a size cap per proposal. *Touched caches:* full cluster state plus all member-level caches. *Constraint:* never applicable to MUST components (D2) or H7 pairs.

**N8 — Merge Cluster (reunification).** Move all chunks of a split cluster onto one table that now has capacity for the whole cluster.
*Purpose:* improvement moves elsewhere open capacity; reunification is the single highest-value $f_{\text{HH}}$/$f_{\text{tag}}$ move when it becomes available. *Useful:* event-driven — proposed whenever a table's occupancy drops below (capacity − size of some split cluster's total); the optimizer maintains a watch list of split clusters sorted by reunification gain. *Complexity:* O(cluster size × per-member cost).

**N9 — Ejection Chain (recommended addition; primary Phase 2 operator).** Insert guest/unit A into table t by evicting resident B to B's best alternative table, recursively to bounded depth (default 3), accepting the chain only if the terminal move lands feasibly and the total chain delta plus the unseating-penalty recovery is positive.
*Purpose:* the only operator that can seat a stranded guest when no single table has room but the *system* has room. Essential for D2 all-or-unseat MUST groups and late accessibility placements. *Complexity:* O(depth × branching × per-move cost); branching capped by taking only the top-b alternative tables per eviction (default b = 3). Deterministic given the seed and the total-order tie-breaks.

**N10 — Table Drain (recommended addition).** Empty a low-expected-occupancy table entirely, redistributing its members via best-insertion.
*Purpose:* directly serves $f_{\text{fill}}$ — the sparse-table failure mode is best fixed by removing the table from use, not by nudging individuals. *Useful:* under attendance priors with high no-show mass; late-phase. *Complexity:* O(table members × per-member insertion cost). Only proposed for tables whose $\tilde n_t / c_t$ is below a drain threshold and whose members all have feasible alternatives.

**N11 — Ruin-and-Recreate burst (diversification operator, see §6).** Remove all assignments in a seeded-random selection (a geometric region of tables, a whole zone, or a cluster family), then reconstruct that sub-instance with the Phase 1 heuristic.
*Purpose:* large-neighborhood escape from basins that pairwise moves cannot leave. *Complexity:* proportional to the ruined region; budgeted as a fixed small fraction of total iterations.

---

# 5. Incremental Scoring

**Principle:** after Phase 0, the full objective is computed exactly once. Every subsequent evaluation is a delta. Full recomputation exists only as the debug-mode consistency check and the Phase 4 independent evaluator (V2).

Because every normalizer is fixed (§2.5), each term's delta reduces to a numerator change divided by a constant:

- **$f_{\text{HH}}$:** moving guest g out of table t removes (household histogram of t at h(g)) − 1 co-seated pairs; into t′ adds the destination histogram count. Two histogram reads: **O(1)**.
- **$f_{\text{pref}}$:** for each of g's PREFER partners, membership probes against source and destination member sets: **O(deg_pref(g))**.
- **$f_{\text{VIP}}$:** g's own unary components (zone, entrance) are pretabulated per (guest, table); the neighbor component reads the VIP counters of both tables, with the saturation min applied to the *changed* per-guest values — g's own neighbor term plus the increment/decrement to each VIP already at the two tables, all from counters: **O(1)** amortized (the neighbor-of-neighbors adjustment is a closed-form function of the two counters, not a member scan, because the saturated term depends only on the count).
- **$f_{\text{tag}}$:** the shared-tag pair mass change is $\sum_{\lambda \in \Lambda_g} [\text{hist}_{t'}(\lambda) - \text{hist}_t(\lambda) + \text{self-correction}]$: **O(|Λ_g|)** with two histogram lookups per tag.
- **$f_{\text{anchor}}$, $f_{\text{row}}$:** pure unary — read the pretabulated (guest, table) values for source and destination: **O(1)**. The pretable is $|G| \times |T|$ small floats (≈ 60K entries at 1000×60), built in Phase 0.
- **$f_{\text{loc}}$:** the cluster's occupied-table set gains/loses a table only when the moved member is the first/last of its cluster at that table (read the destination/source histogram); the pairwise-distance sum updates by the distances from the entering/leaving table to the other tables in $T_c$: **O(|T_c|)**, with $|T_c|$ small (2–5 in practice).
- **$f_{\text{prec}}$:** discordant-pair count maintained against a static sort of guests by precedence rank with anchor-distance values per table pretabulated; a move changes g's distance value, and the discordance delta is computed against the rank-neighborhood via an order-statistics structure over (rank, distance): **O(log |G|)** amortized.
- **$f_{\text{fill}}$ and H10 slack:** per-unit contribution to $\mathbb{E}[N_t]$ and $\operatorname{Var}[N_t]$ added/removed in **O(1)**; the fill numerator and used-table count adjust in O(1) with the (numerator, count) representation.
- **Unseating penalty:** ±$P_u \omega_g$ on seat/unseat transitions: **O(1)**.

**Composite move cost:** N1/N2 ≈ O(deg(g) + |Λ_g| + |T_c|); unit and chunk moves multiply by unit size; N7 and N9 as bounded in §4. With Lagos-typical relationship densities (a few edges and 1–3 tags per guest), a single move evaluates in well under a microsecond-equivalent of work — the budget arithmetic in §9 rests on this.

**Cache update discipline:** a move is evaluated against read-only caches; only on *acceptance* are the caches mutated, in one atomic pass, in a fixed canonical order (source table, destination table, cluster, score numerators, penalty) so that debug-mode invariant checks can run between any two moves.

---

# 6. Optimization Strategy

**Algorithm: simulated annealing with a tabu overlay and LNS diversification bursts.** Justification against alternatives:

- *Pure tabu search* excels at intensification but its aspiration/tenure machinery is fussier to make deterministic and its escape behavior on this problem's plateau-heavy landscape (many equal-scored symmetric arrangements) is weaker than temperature-driven acceptance.
- *Pure LNS* has strong escape but poor fine convergence; here it is retained as the diversification operator only.
- *Exact CP-SAT* is the offline benchmark per the model's §9, not the production path.
- Annealing fits because: move deltas are exact and cheap (§5), the objective is bounded ($F \le \sum_k w_k$) which makes temperature calibration principled, and the model's V3 permits stochastic search provided it is seed-deterministic and iteration-budgeted.

**Deterministic seeding.** One 64-bit seed is a run input, recorded in the run config. A single counter-based PRNG stream drives *all* stochastic choices (move type, endpoints, acceptance draws, ruin regions). Adaptive components (below) update by deterministic rules of observed deltas. Identical instance + seed + budget ⇒ byte-identical trajectory (V3).

**Initial temperature — calibrated, not guessed.** Sample a fixed number of random feasible moves (e.g., 2,000, seeded) from the constructed solution; set $T_0$ such that the median *uphill* sampled move would be accepted with probability 0.3. This anchors the schedule to instance scale automatically — no magic constants across event sizes.

**Temperature schedule.** Geometric cooling $T_{i+1} = \gamma T_i$ per epoch (an epoch = a fixed iteration count, e.g. 5 × |G| proposals), with $\gamma$ chosen so the schedule spans $T_0 \to T_0/1000$ across the iteration budget. **Reheating:** if an epoch ends with zero pocket improvement and acceptance rate below 2%, reheat to $T \leftarrow T \cdot \gamma^{-\text{epochs}/4}$ at most twice per run.

**Acceptance.** Metropolis on the true delta: improving and equal moves always accepted; uphill moves accepted with probability $\exp(\Delta F / T)$. Hard constraints are *not* in the acceptance function — infeasible moves are never proposed for acceptance at all (§7).

**Move selection.** A weighted categorical draw over neighborhoods {N1, N2, N3, N4, N5, N6, N8-watchlist, N10}, with N7 proposed on a fixed low schedule and N9 reserved for Phase 2 plus a small in-phase quota when unseated guests remain. Weights adapt by a deterministic score-credit rule (each neighborhood's weight is a smoothed function of its recent accepted-improvement per unit cost), floored so no neighborhood starves. Endpoint selection within a move type is uniform over admissible candidates via the seeded stream, using the precomputed candidate lists.

**Tabu overlay.** On acceptance of a move, the moved guest(s) are tabu against returning to their source table for a tenure drawn deterministically from the seed (7–15 iterations × unit size), with the standard aspiration exception (a tabu move is admissible if it would improve the pocket). Purpose: prevents the annealer from thrashing 2-cycles on plateaus, which this problem's symmetric arrangements otherwise induce.

**Intensification.** The pocket (best-known) is maintained continuously. Every k epochs without pocket improvement (default k = 4), revert the incumbent to the pocket and continue at the current temperature — bounded backtracking that concentrates late search near the best basin.

**Diversification / escaping local minima.** Beyond temperature: (i) N11 ruin-and-recreate bursts on a fixed schedule (e.g., every 8 epochs, ruining 5–10% of seated guests chosen as a seeded geometric region or cluster family), reconstructed greedily; (ii) the reheating rule above. Both are seed-deterministic.

**Restart strategy.** If the total budget is large and the run stagnates hard (no pocket improvement across 25% of the budget), perform a soft restart: keep the pocket, re-run construction with a seeded perturbation of the item order (swap adjacent items with probability drawn from the stream), and continue improvement from whichever of {perturbed construction, pocket} scores higher. At most one soft restart per run.

**Stopping criteria** (first to trigger):
1. Iteration budget exhausted — the primary criterion; budgets are expressed in proposal counts per V3 (wall-clock budgets are admissible only with the model's surfaced-nondeterminism caveat).
2. Upper-bound convergence: $F(\text{pocket}) \ge \text{UB} - \epsilon_F$, where UB is the cheap instance bound $\sum_k w_k \cdot f_k^{\max}$ with per-term structural maxima (e.g., $f_{\text{tag}}^{\max} < 1$ when a tag group exceeds every table — computable in Phase 0).
3. Zero unseated + no accepted move across two full epochs at floor temperature.

**Time budgeting.** Recommended default proposal budgets by size: 200 guests → 200k proposals; 500 → 750k; 1000 → 2M; 2000 → 5M. At the per-move costs of §5 these land in low single-digit seconds on commodity hardware, matching the "one-click, minutes-to-seconds" product bar with headroom; the budget is a run parameter, so the T-24h re-run can use a smaller budget warm-started from the persisted solution (§12).

---

# 7. Constraint Handling

**Chosen strategy: feasible-only search (never-violate), with constraint elimination and pruning. No penalty methods for H1–H9.**

- **Never violated:** H1 is structural (the assignment representation cannot express a double placement). H2, H5, H8, H9 and (when enabled) H10 are preconditions checked before any move enters acceptance; a failing move is discarded with its blocking family recorded for diagnostics sampling. Rationale: the model's §9 observes that single-move feasibility is O(1) with maintained state — when checking is that cheap, penalty methods buy nothing and cost two real risks: penalty-weight tuning (a new arbitrary-constant surface the model forbids) and transient infeasibility, which would destroy the anytime property (an interrupted run must still be servable to an operator mid-event-week).
- **Constraint elimination (propagation at load):** MUST components are merged into atomic units in Phase 0 — H6 ceases to exist as a runtime constraint because the search cannot express a violation. Likewise H7 pairs are fused into atoms. Static per-guest table admissibility (H3, H4, H8) is compiled into candidate lists — those constraints are never evaluated again.
- **Propagation beyond elimination:** SEPARATE cliques are analyzed in Phase 0: any clique larger than the number of admissible tables for its members is *provably* unsatisfiable for at least one member, reported as a pre-run operator warning rather than discovered mid-search. Segment demand vs. segment zone capacity is similarly pre-checked (total segment membership vs. total capacity of allowed zones) to surface starvation before optimization runs.
- **Repair (Phase 2 only):** repair here means *repairing unseatedness*, never repairing violations — the solution is feasible at all times; ejection chains (N9) search for feasible multi-move insertions.
- **H10 nuance:** the chance constraint uses the normal surrogate for units ≥ 8 and the exact Poisson-binomial tail below; both are precondition checks against the slack accumulator. When ε = 0 or all $p_g = 1$, H10 degenerates to H2 exactly, and the checker short-circuits.

---

# 8. Explainability Pipeline

**No duplicated logic — by construction.** The delta evaluators of §5 are the *only* scoring code path. Explainability consumes them in two modes:

1. **During optimization:** nothing extra is computed or stored. MoveState delta vectors exist transiently for acceptance decisions. (Persisting per-move deltas is a debug/telemetry option, not part of the model's contract.)
2. **At finalization (Phase 4):** for each placed guest, the canonical decomposition $D(g)$ of the model's §8 is computed by invoking the same delta evaluators for the counterfactual "g → UNSEATED versus g → tab(g), holding $x_{-g}$ fixed" — i.e., $\delta_k(g, \text{tab}(g) \mid x_{-g})$ per term. Pairwise terms apply the model's symmetric half-credit at this stage (each satisfied pair contributes half to each endpoint), which the evaluators support by parameter, so per-guest decompositions sum to $F$ without double counting. The exactness identity $\sum_k \delta_k = \Delta F$ is asserted per guest (V2).

**Structural annotations** are attached from optimizer state, not recomputed: household-split flags and chunk composition from ClusterState; spillover notices from the advisory cap check; move provenance ("relocated during improvement," "seated via ejection chain") from a per-guest last-accepted-move tag maintained at acceptance time in O(1).

**Unseated verdicts** carry the certified blocking family from Phase 2's exhaustion record: the certification is that every candidate table failed a named dynamic check *and* every depth-bounded ejection chain terminated infeasibly — both facts fall out of the search that was already performed; nothing is re-derived.

**Rendering** (top-magnitude components above a display floor, confidence bands as within-run percentiles of $\sum_k \delta_k(g)$) is a pure presentation function over $D(g)$, downstream of this design.

---

# 9. Complexity Analysis

Let n = |G|, m = |T|, $\bar\delta$ = mean per-move evaluation cost (§5; effectively a small constant under realistic edge/tag densities), I = proposal budget.

**Worst case.** Phase 0: $O(nm)$ for the unary pretable plus $O(|R| + n\log n)$ for structures. Phase 1: $O(n \cdot m \cdot \bar\delta)$ — every item scans its candidate tables. Phase 2: $O(U \cdot b^{d} \cdot \bar\delta)$ for U unseated items with branching b, depth d (defaults b = 3, d = 3 → ≤ 27 chains per item). Phase 3: $O(I \cdot \bar\delta_{\max})$, where $\bar\delta_{\max}$ is dominated by rare N7/N10 proposals — bounded by their proposal quotas, so the phase remains linear in I. Phase 4: one full independent evaluation, $O(nm)$ worst case but practically $O(n \cdot \bar\delta)$, plus n decompositions at $O(\bar\delta)$ each.

**Expected case.** End-to-end work ≈ $O(nm) + O(I)$ with small constants; the improvement phase dominates and is budget-controlled, making runtime a *dial*, not an emergent property.

**Memory.** Dominant: the unary pretable $O(nm)$ small floats; member sets and histograms $O(n + \sum_t \text{distinct tags at } t)$ = $O(n \cdot \bar{|\Lambda_g|})$; candidate lists $O(nm)$ worst case, far sparser in practice under segments; the pocket $O(n)$. Total comfortably in tens of megabytes at the largest sizes below.

**Scalability projection** (default budgets from §6; per-proposal cost including selection, feasibility, delta, bookkeeping conservatively ~1–3 µs-equivalent):

| Guests | Tables (typ.) | Pretable entries | Budget | Projected Phase 1–3 wall time |
|---|---|---|---|---|
| 200 | 20 | 4,000 | 200k | well under 1 s |
| 500 | 40 | 20,000 | 750k | ~1–2 s |
| 1,000 | 60 | 60,000 | 2M | ~3–6 s |
| 2,000 | 100 | 200,000 | 5M | ~8–15 s |

At 2,000 guests the design still holds; the pressure points are N7 (size-capped) and the Phase 4 full evaluation (linear, fine). Contrast with the current engine's single swap pass, whose full-rescan evaluation is effectively $O(n^3 \bar R)$ — the incremental-state design is what moves 1,000 guests from "minutes-to-hours" to seconds.

---

# 10. Correctness

**Invariants that must always hold (any observable state after Phase 1):**

- I1: assignment integrity — every guest at exactly one table or UNSEATED; atomic units co-located; H7 pairs co-located or co-unseated.
- I2: $n_t \le c_t$ for all t; non-negative H10 slack when overbooking is enabled.
- I3: no SEPARATE pair co-seated; no candidate-list violation (implies H3/H4/H8); H9 satisfied for all declared pairs.
- I4: fixed assignments unmoved.
- I5: ScoreState consistency — cached numerators, penalty total, and $F$ equal their from-scratch derivations.
- I6: cache coherence — member sets, histograms, occupancy, E/Var accumulators, cluster table sets and locality sums equal their derivations from the assignment.
- I7: pocket validity — the pocket is itself feasible and its recorded $F$ is exact.

**Checked after every move (production):** I1–I4 by construction of the move preconditions — the check *is* the feasibility gate, so "after every move" costs nothing beyond what acceptance already paid. **Checked after every move (debug mode):** additionally I5–I6 by full rederivation on a sampled schedule (every move is prohibitive; every 1,000th move plus every accepted N7/N9/N10/N11 is the default), and unconditionally after any cache-mutating code path changes.

**Checked at completion (always, the Phase 4 gate — release-blocking):** the model's full V1 feasibility sweep; V2 objective verification by the independent evaluator plus per-guest decomposition exactness and the whole-solution half-credit sum; V3 reproducibility attestation (seed, budget, and config recorded; a byte-identical re-run is a CI property, not a per-run cost); V4 dominance check (no placed guest scores below their unseated counterfactual; no seatable guest left unseated without a certification record). A gate failure aborts emission: no partially verified solution is ever returned as authoritative.

---

# 11. Failure Modes

Every mode below ends in a *feasible* solution plus structured diagnostics — the optimizer's failure vocabulary is "certified unseated / advisory warning," never "violated constraint."

1. **MUST group exceeds every admissible table (D2).** All members unseated, cause certified after N9 exhaustion. *Degradation:* the rest of the plan is unaffected. *Operator warning:* named group, size, largest admissible table capacity, and — actionable — the minimum capacity increase or exclusion-flag change that would admit it.
2. **Accessibility capacity shortfall (H4).** Accessible tables' aggregate remaining capacity < accessibility demand. Pre-detected in Phase 0 propagation where provable; otherwise certified per guest. *Warning:* shortfall count and which tables' `accessibilityCompatible` flag would resolve it.
3. **Segment starvation (H8).** A segment's membership exceeds its allowed zones' capacity. Pre-run warning from propagation; overflow members certified-unseated with the segment named. Advisory caps (D3) additionally emit spillover notices without unseating.
4. **SEPARATE deadlock.** A separation clique larger than the admissible-table count for its members — pre-run warning identifying the clique; the lowest-$\omega_g$ member(s) end certified-unseated.
5. **Chance-constraint infeasibility (H10).** Under priors and ε, total effective capacity < expected demand. Pre-run check: $\sum_t$ H10-effective capacity vs. $\sum_j m_j p_j$. *Degradation:* seat in expected-value priority order; surplus certified-unseated with the venue-scale cause. *Warning:* the ε at which the instance becomes feasible, so the operator can consciously accept more overflow risk instead of stranding guests.
6. **Household-of-two review (H7).** Never split; flagged list emitted exactly as the current engine does.
7. **Budget-bound quality shortfall.** Feasible but $F$ short of the Phase 0 upper bound by more than a threshold. Not a failure of correctness — the run reports the optimality-gap estimate and the reheat/restart telemetry so the operator (or a retry policy) can grant a larger budget.
8. **Degenerate instances.** Zero candidate tables after filters, empty guest list, all tables locked: Phase 0 detects and returns an empty-but-valid run with a single instance-level diagnostic rather than proceeding.
9. **Pathological weight configurations.** All weights zero (every placement scores 0; construction order alone decides — flagged as advisory since ties dominate), or a single enabled rule (legitimate, but the run notes that unrelated terms were unoptimized).

**Diagnostics package (every run):** per-guest verdicts with decompositions; certified causes with the constraint-family taxonomy; pre-run propagation warnings; spillovers; household-of-two list; per-table $n_t$, $\mathbb{E}[N_t]$, H10 slack; search telemetry (acceptance curve, pocket-improvement timeline, neighborhood credit shares, restarts/reheats); the resolved D1–D6 configuration; seed and budget.

---

# 12. Extensibility

The architecture's extension seam is the **objective-term contract**. A term is a module providing: (a) a Phase 0 normalizer/denominator computation and any private caches; (b) a delta evaluator invoked by MoveState assembly for each move type it is sensitive to (with a declared sensitivity mask so insensitive moves skip it at zero cost); (c) a decomposition contribution obeying the exactness identity; (d) optional pre-run propagation checks and diagnostics. Registering a term touches no phase logic, no neighborhood, no acceptance machinery — the annealer optimizes whatever $\sum w_k f_k$ the registry defines. The model's [EXTENSION] terms ($f_{\text{prec}}$, $f_{\text{fill}}$) are already expressed through this contract in this document; future ones follow the same path:

- **Precedence (already specified):** activates via the D4 data field; its order-statistics cache is a private term cache under (a).
- **Seat numbering:** per the model's A2/non-goals, *not* a new term — a downstream ordinal-assignment stage consuming this optimizer's table assignment as fixed input, with its own (much smaller) model per table. The pipeline gains a Phase 5; nothing in Phases 0–4 changes. The head-table protocol gap flagged in the repository review lands there.
- **Sightlines:** a unary term — a Phase 0 visibility field per (table, focal anchor) computed from fixture geometry feeds a pretabulated unary component exactly like $f_{\text{anchor}}$; zero new move machinery.
- **Dynamic arrivals / walk-ins:** warm-start mode — load the persisted solution as the incumbent, mark checked-in guests' assignments fixed (A5 machinery, already present), run a short-budget Phase 2 + Phase 3 over the delta. The event-day re-run of the model's §7.4 is this mode with updated RSVP statuses and $p_g = 1$ for arrivals.
- **Multi-stage events (ceremony → reception):** per-stage instances sharing GuestState, plus one new *cross-stage* pairwise term rewarding assignment stability between consecutive stages (a pairwise term over (guest, stage) with the standard contract); stages solve sequentially with the prior stage's solution as a warm start.
- **New hard constraints** extend ConstraintState with a precondition predicate and, where possible, a Phase 0 propagation/elimination rule — the feasible-only strategy (§7) means new constraints slot in as preconditions without touching acceptance or scoring.

The one deliberate rigidity: nothing extends by post-hoc penalty terms on hard requirements — the model's constraint/objective boundary is architectural here, and blurring it would forfeit the anytime-feasible guarantee that the rest of this design is built on.
