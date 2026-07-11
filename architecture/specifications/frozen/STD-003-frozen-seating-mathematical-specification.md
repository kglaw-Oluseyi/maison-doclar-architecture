---
artifact_id: STD-003
name: Frozen Seating Mathematical Specification
version: frozen
status: Frozen — immutable historical evidence, constitutionally promoted
authored: Jun 2026 (principal–Claude architecture session; declared frozen at authorship)
promoted: 2026-07-11
provenance: maison-doclar-knowledge-extraction raw/historical-event-os/algorithms/ @ 9a645cc9 (attested by principal)
decision_refs: [DI-030, DI-034]
note: Original content below is verbatim and unchanged; only this metadata block is prepended.
---
# Aperture Seating Allocation — Formal Optimization Specification

**Status:** Normative mathematical model. Implementation-independent.
**Basis:** `kglaw-Oluseyi/maison-doclar-aperture` — Prisma schema and `lib/seating/allocator-engine.service.ts` and satellites, reviewed directly.
**Provenance markers:** every element is tagged **[CURRENT]** (reflects the implemented engine), **[EXTENSION]** (recommended addition from the prior review), or **[DECISION]** (a semantic choice the Principal must ratify because it changes or disambiguates existing behavior).

---

# 1. Problem Definition

## 1.1 Informal statement

Assign each eligible guest of a single event to at most one table of a single layout, such that all feasibility (hard) constraints hold, and a weighted sum of preference (soft) objectives is maximized, under uncertainty about which guests will actually attend.

## 1.2 Inputs

| Input | Source of truth | Tag |
|---|---|---|
| Guest set with attributes (household, VIP tier, ceremonial role, accessibility needs, RSVP status, tags via relationship edges) | `Guest`, `Household`, `GuestRelationship` | [CURRENT] |
| Table set with geometry, capacity, zone, type, lock/exclusion flags, accessibility flag, entrance proximity | `Table`, `Zone`, `Layout` | [CURRENT] |
| Relationship edges with strength ∈ {MUST_SEAT_TOGETHER, PREFER_TOGETHER, SEPARATE, INFORMATIONAL} | `GuestRelationship` | [CURRENT] |
| Rule configuration: ordered rule list → weights; accessibility enforcement flag; locked-seat respect flag | `SeatingRuleSet`, `ruleOrderJson` | [CURRENT] |
| Tag closeness ranks and row preferences | `RelationshipTagPreset` (`closenessRank`, `rowPreference`) | [CURRENT] |
| Layout anchor points (guest / point / fixture) | `LayoutAnchor` | [CURRENT] |
| Zone-segment map: guest→segment, segment→allowed zones, optional segment caps, tag→segment rules | `Layout.zoneSegmentMapJson` | [CURRENT] |
| Pre-existing assignments (treated as fixed) | `Assignment` | [CURRENT] |
| Audience mode ∈ {EVERYONE_AT_ONCE, CONFIRMED_FIRST} | run option | [CURRENT] |
| Optional table filters (target table type, target seat count) | run options | [CURRENT] |
| Segment-level attendance priors and risk tolerance ε | new (see §7) | [EXTENSION] |
| Precedence ranks (elder/title hierarchy, distinct from closeness) | new (see §5.8) | [EXTENSION] |
| Dispersion constraints ("same zone, different tables") | new (see §4.9) | [EXTENSION] |

## 1.3 Outputs

For every eligible guest, exactly one of:

- a **placement** (guest → table), with a confidence grade and a structured score decomposition (§8) from which the human-readable reason is rendered; or
- an **unseated verdict**, with a machine-checkable infeasibility cause (which constraint family blocked placement).

Plus run-level diagnostics: seated/unseated counts, household splits, cluster spillovers, household-of-two review list, per-table occupancy. [CURRENT]

## 1.4 Assumptions (explicit)

- **A1.** One event, one layout per run. Cross-event data never enters the model (standing isolation rule). [CURRENT]
- **A2.** Seat-level (within-table ordinal) assignment is out of scope: the model assigns tables, never seat numbers. The engine emits `suggestedSeatNumber = null` universally. [CURRENT]
- **A3.** Guest attributes and relationship edges are correct as given; the model does not infer new edges. [CURRENT]
- **A4.** Attendance probabilities, where used, are **segment-level priors**, never per-guest historical identities — the Nucleus architecture (no cross-event master guest ID) forbids the latter. [EXTENSION, architectural constraint]
- **A5.** Pre-existing manual assignments are immutable inputs: they consume capacity and constrain co-seating, but the optimizer may not move them. [CURRENT]
- **A6.** Determinism: identical inputs (including any random seed as an input) must yield identical outputs. [CURRENT — the engine is fully deterministic; any stochastic optimizer must take an explicit seed.]

## 1.5 Optimization goals

Maximize weighted soft-constraint satisfaction (§6) subject to full hard-constraint feasibility (§4), with:

- graceful degradation — when a guest cannot be feasibly placed, produce an unseated verdict with a cause, never a constraint violation;
- robustness to no-shows — expected realized occupancy should be high without breaching physical capacity beyond tolerance ε (§7);
- explainability — every placement's score must decompose exactly into named term contributions (§8).

## 1.6 Scope

Table-level assignment for one layout of one event, for guests with `status ≠ HIDDEN` and `rsvpStatus ≠ DECLINED`, at 200–1000+ guests and typically 20–100 tables.

> **[DECISION D1]** The engine currently does **not** exclude `pendingIntakeReview` guests, contradicting the schema comment ("excluded from live calculations until operator approves initial intake"). This spec defines eligibility as *excluding* intake-pending guests; ratify or strike.

## 1.7 Non-goals

- Within-table seat ordering and ceremonial protocol order (couple centred, flanking precedence). Flagged in the prior review as the sharpest gap for the investiture, but it is a distinct ordinal-assignment problem layered on top of this model — deliberately out of scope here.
- Sightline/obstruction geometry (fixtures as occluders).
- Table placement/venue geometry optimization (table positions are inputs, not variables).
- Multi-layout or multi-event optimization.
- RSVP prediction per named individual (see A4).

---

# 2. Decision Variables

## 2.1 Primary

$$x_{g,t} \in \{0,1\} \quad \forall g \in G,\ t \in T$$

x_{g,t} = 1 iff guest g is assigned to table t. This is the only true decision. [CURRENT — the engine's `Placement` records are exactly the support of x.]

## 2.2 Derived (definitional, not free)

- **Unseated indicator:** $u_g = 1 - \sum_{t \in T} x_{g,t} \in \{0,1\}$. A guest is either at exactly one table or unseated. [CURRENT]
- **Table occupancy:** $n_t = \sum_{g \in G} x_{g,t} + n^{\text{fixed}}_t$, where $n^{\text{fixed}}_t$ is the count of pre-existing immutable assignments at t. [CURRENT — the engine's `used` map with `seedTableUsage`.]
- **Co-seating:** $y_{g,g',t} = x_{g,t} \cdot x_{g',t}$. In a linear formulation this is an auxiliary binary with the standard linearization $y \le x_{g,t},\ y \le x_{g',t},\ y \ge x_{g,t}+x_{g',t}-1$; in a search formulation it is simply evaluated. All pairwise objectives are functions of y. [CURRENT — implicit in `guestsAtTable`.]
- **Expected occupancy:** $\tilde n_t = \sum_g p_g\, x_{g,t} + \tilde n^{\text{fixed}}_t$ where $p_g$ is the attendance prior (§7). [EXTENSION]

## 2.3 Not variables

Attendance probabilities $p_g$ are **parameters**, not decision variables (§7). Cluster membership is derived from data (households, MUST components, tag groups), not decided. Weights are configuration parameters.

---

# 3. Sets and Parameters

## 3.1 Sets

| Symbol | Definition | Source |
|---|---|---|
| $G$ | Eligible guests: `status ≠ HIDDEN ∧ rsvpStatus ≠ DECLINED` (∧ `¬pendingIntakeReview` per D1) | `Guest` |
| $G^{\text{fixed}}$ | Guests with pre-existing assignments (disjoint handling per A5) | `Assignment` |
| $T$ | Candidate tables of the layout, after optional filters (target type / seat count), excluding `excludedFromAllocator` and, when `respectLockedSeats`, `locked` tables | `Table` |
| $Z$ | Zones; $z(t) \in Z \cup \{\varnothing\}$, zone type $\zeta(z) \in$ {GENERAL, VIP, ACCESSIBILITY, STAFF, RESERVED, COCKTAIL, DINING, ENTERTAINMENT} | `Zone` |
| $H$ | Households; partial map $h(g) \in H \cup \{\varnothing\}$; $H_k = \{g : h(g)=k\}$ | `Household` |
| $R$ | Directed relationship edges $(g, g', \lambda, s)$ with tag $\lambda$ and strength $s$; edges with null target reference the event's named anchor and carry tag information only | `GuestRelationship` |
| $R_{\text{must}}, R_{\text{pref}}, R_{\text{sep}}$ | Edge subsets by strength (INFORMATIONAL edges contribute tags only, no constraint) | derived |
| $M$ | MUST components: connected components of $G$ under undirected $R_{\text{must}}$ (union-find) | derived [CURRENT] |
| $\Lambda$, $\Lambda_g$ | Tag universe; $\Lambda_g$ = lowercased tags of all edges incident to g | derived [CURRENT] |
| $A$ | Anchor points in the canvas plane (guest-, point-, or fixture-derived) | `LayoutAnchor` |
| $S$ | Segments; partial map $\sigma(g) \in S \cup \{\varnothing\}$ (guest-level, falling back to household-level); allowed zones $Z_s \subseteq Z$; optional cap $\kappa_s$ | `zoneSegmentMapJson` |
| $C$ | Unified placement clusters (household-type or tag-type), used by the constructive phase and the cluster-integrity objective | `buildUnifiedPlacementClusters` [CURRENT] |
| $B$ | Row bands {FRONT, MIDDLE, BACK} computed from table geometry relative to anchors; band map $b(t) \in B$ | `computeTableRowBands` [CURRENT] |

## 3.2 Parameters

| Symbol | Definition | Source | Tag |
|---|---|---|---|
| $c_t \in \mathbb{N}$ | Physical capacity of table t | `Table.capacity` | [CURRENT] |
| $\text{acc}(g) \in \{0,1\}$ | Guest has accessibility needs (non-empty, non-`{}` field) | `Guest.accessibilityNeeds` | [CURRENT] |
| $\text{acc}(t) \in \{0,1\}$ | Table is accessibility-compatible | `Table.accessibilityCompatible` | [CURRENT] |
| $v_g \in$ {PRINCIPAL, PRIORITY, STANDARD, NONE} | VIP tier, with strict order PRINCIPAL ≻ PRIORITY ≻ STANDARD ≻ NONE | `Guest.vipTier` | [CURRENT] |
| $\pi(t) \in$ {NEAR, MID, FAR, ∅} | Entrance proximity band | `Table.proximityToEntrance` | [CURRENT] |
| $d(t, A) \ge 0$ | Minimum Euclidean distance from table t's rectangle to any anchor point | `minAnchorDistance` | [CURRENT] |
| $r_g \in \mathbb{N} \cup \{\infty\}$ | Closeness rank: best (lowest) `closenessRank` among g's tags; ∞ if untagged/unranked | `buildGuestClosestRankMap` | [CURRENT] |
| $\rho_g \in B \cup \{\varnothing\}$ | Row preference derived from g's tags' `rowPreference` | `guestRowPreference` | [CURRENT] |
| $w_k \ge 0$ | Weight of soft term k. **Semantics:** with the ordered-rule UI, the i-th of n enabled rules gets integer weight $n - i + 1$; disabled rules get 0 (`orderToWeights`). Legacy float weights on `SeatingRuleSet` remain admissible. | `allocator-rule-order` | [CURRENT] |
| $e_1, e_2 \in \{0,1\}$ | `enforceAccessibility`, `respectLockedSeats` | `SeatingRuleSet` | [CURRENT] |
| $p_g \in [0,1]$ | Attendance prior, $p_g = \pi_{s(g)}$ from segment prior (§7); $p_g \equiv 1$ recovers the current deterministic model | new | [EXTENSION] |
| $\varepsilon \in (0,1)$ | Per-table overflow risk tolerance (§7.4) | new | [EXTENSION] |
| $q_g \in \mathbb{N} \cup \{\infty\}$ | Precedence rank (elder/title hierarchy), independent of $r_g$ | new | [EXTENSION] |

---

# 4. Hard Constraints

A solution is **feasible** iff all of the following hold. H1–H8 are mandatory and reflect the implemented engine; H9–H10 are extensions; D-flags are decisions.

**H1 — Assignment integrity.** $\sum_{t \in T} x_{g,t} + u_g = 1 \quad \forall g \in G.$
Every guest is at exactly one table or explicitly unseated. Duplicate suggestions are a model violation (the engine throws on them). [CURRENT]

**H2 — Physical capacity.** $n_t \le c_t \quad \forall t \in T.$
Occupancy including fixed assignments never exceeds capacity. Under the attendance extension this is replaced at *planning* time by the chance constraint H10 while remaining the day-of invariant. [CURRENT]

**H3 — Table admissibility.** $x_{g,t} = 0$ for every table excluded from the allocator, and, when $e_2 = 1$, for every locked table. (Equivalently: such tables are removed from $T$.) [CURRENT]

**H4 — Accessibility.** If $e_1 = 1$: $\text{acc}(g) = 1 \wedge \text{acc}(t) = 0 \Rightarrow x_{g,t} = 0.$
Guests with accessibility needs may only sit at accessibility-compatible tables. If no such table has capacity, the guest is unseated with that specific cause. [CURRENT]

**H5 — Separation.** $x_{g,t} + x_{g',t} \le 1 \quad \forall (g,g') \in R_{\text{sep}},\ \forall t.$
SEPARATE pairs never share a table. Symmetric regardless of edge direction. [CURRENT]

**H6 — MUST co-location.** For each MUST component $m \in M$: either all members share one table — $x_{g,t} = x_{g',t}\ \forall g,g' \in m, \forall t$ — or **all** members of m are unseated ($u_g = 1\ \forall g \in m$) with cause "no table fits the must-group."
> **[DECISION D2]** This is stricter than the current engine: today, when no table fits a MUST group, its members silently fall through to individual greedy placement — MUST degrades to best-effort. That silently violates the stated semantics of MUST_SEAT_TOGETHER. This spec makes all-or-unseat normative; ratify (recommended) or codify the current relaxation explicitly as "MUST-preferred."

**H7 — Household-of-two integrity.** A household with exactly two members is never split: co-located or both unseated (flagged for operator review). [CURRENT — this is the one group the engine already refuses to split.]

**H8 — Segment/zone restriction.** If $\sigma(g) \ne \varnothing$ and $Z_{\sigma(g)} \ne \emptyset$: $z(t) \notin Z_{\sigma(g)} \Rightarrow x_{g,t} = 0$ (tables without a zone are inadmissible for segmented guests). Guests without a segment, and segments with an empty zone list, are unrestricted. [CURRENT]

**H9 — Zone-level dispersion.** For a declared dispersion pair $(g, g')$: $x_{g,t}=x_{g',t'}=1 \Rightarrow z(t) = z(t') \wedge t \ne t'$ (same zone, different tables) — the "clustered but not monolithic" compound-family requirement; and for a declared zone-separation pair: $z(t) \ne z(t')$. [EXTENSION — not expressible today; requires a new relationship strength or constraint entity. Data-model change: Principal's call.]

**H10 — Probabilistic capacity (planning-time).** $\Pr[N_t > c_t] \le \varepsilon$ where $N_t$ is realized occupancy under the attendance model (§7.4). Replaces H2 during planning when overbooking is enabled; H2 remains the physical day-of invariant. [EXTENSION]

**Not hard constraints (by design):** VIP zone placement, anchor proximity, row preference, tag cohesion, household togetherness above the H6/H7 minima — all soft (§5). Segment caps $\kappa_s$ are advisory today: exceeding them emits a *spillover notice*, not an infeasibility. [CURRENT] > **[DECISION D3]** Keep advisory, or promote $\sum_{g:\sigma(g)=s} \sum_{t} x_{g,t} \le \kappa_s$ to hard? Spec default: advisory (matches implementation).

---

# 5. Soft Constraints

Each soft term is a function $f_k(x) \in [0,1]$ after normalization, so weights are comparable across terms and across events of different sizes. The engine's current raw scores are noted; normalization denominators are the extension that makes weights meaningful (today, a term touching more pairs silently dominates).

Notation: $\text{tab}(g)$ = the table with $x_{g,t}=1$; pair-at-same-table indicator $y_{gg'} = \sum_t y_{g,g',t}$.

## 5.1 Household togetherness — $f_{\text{HH}}$

**Purpose:** members of a household sit together (family units, couples, compounds).
**Definition:** over households with ≥ 2 seated members,
$$f_{\text{HH}} = \frac{\sum_{k \in H} \sum_{\{g,g'\} \subseteq H_k} y_{gg'}}{\sum_{k \in H} \binom{|H_k \cap G_{\text{seated}}|}{2}}$$
— realized co-seated household pairs over all possible household pairs. **[CURRENT raw form:** for each guest, count of already-placed household members at the candidate table.**]**
**Interpretation:** 1 = every household intact (up to capacity-forced splits); a split large household degrades proportionally to broken pairs, which correctly prefers a 5+3 split over 4+2+2 for a household of 8.

## 5.2 Relationship preference — $f_{\text{pref}}$

**Purpose:** honor explicit PREFER_TOGETHER edges.
**Definition:** $f_{\text{pref}} = \dfrac{\sum_{(g,g') \in R_{\text{pref}}} y_{gg'}}{|R_{\text{pref}}|}$ — fraction of preference edges satisfied. [CURRENT raw form: +w per satisfied partner at candidate table.]
**Interpretation:** directly reportable to the host as "N of M stated preferences honored."

## 5.3 VIP placement — $f_{\text{VIP}}$

**Purpose:** VIPs in VIP zones, near other VIPs, near the entrance.
**Definition:** for each VIP guest g (i.e. $v_g \ne$ NONE), a per-guest score
$$\phi_{\text{VIP}}(g) = \tfrac{1}{2}\,\mathbb{1}[\zeta(z(\text{tab}(g))) = \text{VIP}] \;+\; \tfrac{3}{8}\,\min\!\Big(1, \tfrac{\#\text{VIP tablemates}}{\nu}\Big) \;+\; \tfrac{1}{8}\,\mathbb{1}[\pi(\text{tab}(g)) = \text{NEAR}]$$
and $f_{\text{VIP}} = \frac{1}{|G_{\text{VIP}}|}\sum_{g \in G_{\text{VIP}}} \phi_{\text{VIP}}(g)$, with $\nu$ a saturation count (default 4).
**[CURRENT raw form:** +2w for VIP zone, +w per VIP tablemate (unbounded), +0.5w for NEAR — the 2 : 1-per-neighbor : 0.5 ratios above preserve those relativities while bounding the neighbor term, which today can dominate everything at a large VIP table.**]**
**Interpretation:** 1 = every VIP in a VIP zone at a VIP-dense, entrance-near table.

## 5.4 Tag cohesion — $f_{\text{tag}}$

**Purpose:** guests sharing a tag (aso-ebi group, "Lagos university set", family branch) cluster at tables.
**Definition:** $f_{\text{tag}} = \dfrac{\sum_{\{g,g'\}} |\Lambda_g \cap \Lambda_{g'}| \, y_{gg'}}{\sum_{\{g,g'\}} |\Lambda_g \cap \Lambda_{g'}|}$ — tag-weighted realized co-seatings over tag-weighted possible co-seatings among seated guests. [CURRENT raw form: +w per shared tag per tablemate.]
**Interpretation:** for a tag group larger than any table, the maximum is < 1 by necessity; the multi-table locality term (§5.7) covers the residual.

## 5.5 Anchor proximity — $f_{\text{anchor}}$

**Purpose:** closer-ranked circles sit nearer the layout's focal anchors (high table, stage).
**Definition:** per guest with finite rank $r_g$ and $|A| > 0$:
$$\phi_{\text{anchor}}(g) = \frac{1}{r_g} \cdot \frac{1}{1 + d(\text{tab}(g), A)/d_0}$$
with $d_0$ a canvas distance scale, and $f_{\text{anchor}}$ the mean over eligible guests. **[CURRENT raw form:** $w \cdot \frac{1}{r_g} \cdot \frac{1000}{1 + d}$ — the constant 1000 is an arbitrary canvas-pixel scale; the spec replaces it with explicit $d_0$ (calibration: $d_0$ = half the layout diagonal gives ≈ 0.33–1.0 across a typical canvas).**]**
**Interpretation:** rank-1 tags at anchor-adjacent tables score ≈ 1; deep ranks or far tables decay toward 0.

## 5.6 Row preference — $f_{\text{row}}$

**Purpose:** tags carrying FRONT/MIDDLE/BACK expectations land in the matching band (elders forward).
**Definition:** $f_{\text{row}} = \frac{1}{|G_\rho|}\sum_{g \in G_\rho} \mathbb{1}[b(\text{tab}(g)) = \rho_g]$ over guests with a stated preference, using the band map $b(\cdot)$ computed from geometry relative to anchors. [CURRENT — `scoreRowPreferenceBonus`; the spec fixes the match reward at 1 and mismatch at 0; graded adjacency credit (MIDDLE for a FRONT preference = ½) is an admissible refinement.]

## 5.7 Cluster locality (multi-table spillover) — $f_{\text{loc}}$

**Purpose:** when a household/tag cluster must split across tables, the chunks should be adjacent, not scattered.
**Definition:** for each split cluster c occupying table set $T_c$, $\phi_{\text{loc}}(c) = \frac{1}{1 + \bar d(T_c)/d_0}$ where $\bar d(T_c)$ is the mean pairwise inter-table distance within $T_c$; $f_{\text{loc}}$ = mean over split clusters (1 if no cluster splits). **[CURRENT raw form:** during construction, chunk placement adds $\frac{1000}{1+\min\text{dist to prior chunks}} \times \max(w_{\text{HH}}, w_{\text{tag}})$ — a constructive heuristic; the spec restates it as an evaluable objective so the improvement phase can also optimize it.**]**

## 5.8 Precedence placement — $f_{\text{prec}}$ [EXTENSION]

**Purpose:** hierarchical precedence (elders, titled guests, chieftaincy protocol) is a *different ordering* from closeness-to-host; today it free-rides on $r_g$ and $\rho_g$, which conflates a senior Oba with a close cousin.
**Definition:** given precedence ranks $q_g$, require monotonicity in expectation: $f_{\text{prec}} = 1 - \tau^{-}\big(q, d(\text{tab}(\cdot), A)\big)$ where $\tau^{-}$ is the normalized count of *discordant* pairs (a strictly higher-precedence guest seated strictly farther from the anchors than a lower one), i.e. one minus a Kendall-style disagreement rate.
**Interpretation:** 1 = no precedence inversions; each inversion (junior nearer than senior) costs proportionally. Requires the new $q$ parameter — data-model addition, Principal's call.

## 5.9 Attendance robustness / table balance — $f_{\text{fill}}$ [EXTENSION]

**Purpose:** under no-show priors, avoid tables that will *look* empty (the sparse-table failure mode at 30–50% no-show).
**Definition:** $f_{\text{fill}} = \frac{1}{|T_{\text{used}}|} \sum_{t \in T_{\text{used}}} \min\!\Big(1, \dfrac{\tilde n_t}{\beta\, c_t}\Big)$ where $\tilde n_t$ is expected occupancy and $\beta \in (0,1]$ the target fill fraction (default 0.85).
**Interpretation:** 1 = every occupied table expects ≥ β of capacity present. This is the objective-side counterpart of the chance constraint H10: H10 caps overflow risk; $f_{\text{fill}}$ rewards visual fullness.

---

# 6. Objective Function

$$\boxed{\ \max_{x} \; F(x) \;=\; \sum_{k \in K} w_k\, f_k(x) \;-\; P_u \sum_{g \in G} \omega_g\, u_g \ }$$

with $K = \{\text{HH}, \text{pref}, \text{VIP}, \text{tag}, \text{anchor}, \text{row}, \text{loc}, \text{prec}, \text{fill}\}$.

**Term-by-term:**

| Term | Why it exists | Scale | Weight source |
|---|---|---|---|
| $w_{\text{HH}} f_{\text{HH}}$ | Family units intact — the strongest default expectation in Nigerian event seating | [0,1] | rule order (today typically rank 1 → weight 5) |
| $w_{\text{pref}} f_{\text{pref}}$ | Host's explicit pairwise wishes | [0,1] | rule order |
| $w_{\text{VIP}} f_{\text{VIP}}$ | Status placement — VIP zones, entrance, peer proximity | [0,1] | rule order |
| $w_{\text{tag}} f_{\text{tag}}$ | Aso-ebi and social-set cohesion | [0,1] | rule order |
| $w_{\text{anchor}} f_{\text{anchor}}$ | Closeness-ranked circles near focal points | [0,1] | rule order |
| $w_{\text{row}} f_{\text{row}}$ | Band expectations (elders forward) | [0,1] | fixed companion weight to anchor term [CURRENT: additive bonus] |
| $w_{\text{loc}} f_{\text{loc}}$ | Split clusters stay adjacent | [0,1] | derived: $\max(w_{\text{HH}}, w_{\text{tag}})$ [CURRENT] |
| $w_{\text{prec}} f_{\text{prec}}$ | Hierarchical precedence, decoupled from closeness | [0,1] | new rule-order entry [EXTENSION] |
| $w_{\text{fill}} f_{\text{fill}}$ | Robustness to no-shows; no ghost tables | [0,1] | new rule-order entry [EXTENSION] |
| $-P_u \sum \omega_g u_g$ | Unseating penalty: unseating must never be *cheaper* than a bad placement, else the optimizer dumps hard guests | see below | structural |

**Weight semantics [CURRENT, normative]:** weights come from the ordered rule list — the i-th of n enabled rules receives integer weight $n - i + 1$; disabled rules receive 0. Because every $f_k \in [0,1]$, a weight of 5 vs 1 now means literally "full satisfaction of this term is worth 5× full satisfaction of that term" — which is exactly the interpretation the ordered-list UI implies but the current un-normalized scores do not deliver.

**Unseating penalty:** $\omega_g = 1 + \mathbb{1}[v_g \ne \text{NONE}] + \mathbb{1}[\text{acc}(g)]$ (VIPs and accessibility-needs guests are costlier to strand), and $P_u = \sum_k w_k$. Justification for $P_u$: it guarantees that seating any guest anywhere feasible dominates unseating them, since one guest's maximum possible contribution to all normalized terms combined is ≤ $\sum_k w_k \cdot O(1/|G|)$ — i.e., no placement can be worse than absence. This is the only "large constant" in the model and it is derived, not arbitrary.

**No other arbitrary constants:** the 1000-pixel scale is replaced by $d_0$ (§5.5), the unbounded VIP-neighbor accumulation by saturation $\nu$ (§5.3), and confidence thresholds (HIGH/MEDIUM/LOW) are downgraded from objective machinery to reporting bands over the per-guest decomposition (§8).

---

# 7. Attendance Probability Model

## 7.1 Segments and priors [EXTENSION; architecture-compliant]

Define attendance segments as feature buckets over *within-event* observable signals only:

$$s(g) = \big(\text{rsvpStatus}_g,\ \text{band}(r_g),\ v_g,\ \text{engagement}_g\big)$$

where band(·) coarsens closeness rank (e.g. {1–2, 3–5, 6+, unranked}) and engagement is a coarse channel signal (e.g. {responded-inbound, delivered-read, delivered-unread, undelivered/none} from message state and `lastInboundMessageAt`). No component identifies a guest across events — this satisfies the Nucleus constraint (A4).

Each segment carries a prior show rate $\pi_s \in (0,1)$; then $p_g = \pi_{s(g)}$. Setting $\pi_s \equiv 1$ recovers the current deterministic engine exactly, so the extension is strictly backward-compatible.

## 7.2 Calibration from CheckInLedger

After each completed event e, for each segment s compute attempts $n_{e,s}$ (guests in segment at final pre-event snapshot) and successes $k_{e,s}$ (of those, guests with a check-in record). Aggregate across events with Beta–Binomial smoothing:

$$\pi_s = \frac{\alpha_s + \sum_e k_{e,s}}{\alpha_s + \beta_s + \sum_e n_{e,s}}$$

with pseudo-counts $(\alpha_s, \beta_s)$ encoding the informal Lagos prior until data accumulates (e.g. ACCEPTED segments start near 0.85/0.15-shaped priors, PENDING near 0.5/0.5 within the stated 0.3–0.7 belief). Aggregation is over anonymous segment counts — never guest identity — so it can safely live in a cross-event stats table. Snapshot semantics matter: attempts must be measured from a frozen pre-event guest state (the `EventDaySnapshot` mechanism), not the post-event mutable guest list.

## 7.3 Expected attendance and occupancy

Treat attendance as independent Bernoulli($p_g$) **per household**, not per guest — household members' attendance is strongly correlated, so model the household as the atomic Bernoulli unit with size $|H_k|$ and probability $p_{H_k} = \max_{g \in H_k} p_g$ (conservative for capacity), singletons as themselves. Then for table t with assigned atomic units $U_t$ (each unit j of size $m_j$, probability $p_j$):

$$\mathbb{E}[N_t] = \sum_{j \in U_t} m_j p_j, \qquad \operatorname{Var}[N_t] = \sum_{j \in U_t} m_j^2\, p_j (1 - p_j).$$

Ignoring the household correlation (treating guests independently) understates variance by up to a factor of the household size — this is why the unit-level model is normative, not optional.

## 7.4 Overbooking / chance constraint (H10)

Planning-time capacity: $\Pr[N_t > c_t] \le \varepsilon$, operationalized by the one-sided normal surrogate

$$\mathbb{E}[N_t] + z_{1-\varepsilon} \sqrt{\operatorname{Var}[N_t]} \;\le\; c_t$$

with $z_{1-\varepsilon}$ the standard normal quantile (ε = 0.05 → z ≈ 1.645). Interpretation: a 10-seat table in a 0.75-show segment can carry ~12 expected invitees at ε = 0.05. Governance:

- ε is an event-level operator setting; ε → 0 disables overbooking (surrogate reduces to H2 when all $p_g = 1$).
- Physical H2 always governs day-of: the planned overflow buffer is realized through the designated overflow zone and the fast T-24h re-run on updated statuses — both already supported by the deterministic engine + `EventDaySnapshot`.
- The normal surrogate is admissible for $|U_t| \gtrsim 8$; for small tables use the exact Poisson-binomial tail (cheap at table scale).

## 7.5 Where probability enters the objective

Only two places: the chance constraint H10 and the fill term $f_{\text{fill}}$ (§5.9). All pairwise social terms remain computed over *assigned* guests, not expectation-weighted — deliberately: a guest who does attend must find their social context intact; discounting their neighbors' presence would optimize for the average event instead of the realized one.

---

# 8. Explainability Model

## 8.1 Principle

Explanations are a **projection of the objective**, never a parallel computation. (The current engine violates this: `scoreTableForGuest` computes numbers while `buildReason` independently re-derives narrative — they can drift.) Normatively:

## 8.2 Score decomposition

Define the per-guest marginal contribution of term k, given the rest of the solution $x_{-g}$:

$$\delta_k(g, t \mid x_{-g}) \;=\; w_k \Big[ f_k(x_{-g} \cup \{g \to t\}) - f_k(x_{-g} \cup \{g \to \varnothing\}) \Big]$$

— how much placing g at t moves term k versus leaving g unseated, holding everyone else fixed. The decomposition record for a placement is the vector

$$D(g) = \big\langle (k,\ w_k,\ \delta_k(g, \text{tab}(g) \mid x_{-g})) : k \in K \big\rangle \;\cup\; \text{binding hard-constraint annotations}$$

**Exactness requirement:** $\sum_k \delta_k(g, t) = \Delta F(g \to t \mid x_{-g})$ — the components must sum to the true objective delta of the placement, machine-checkable (§10). Pairwise terms attribute symmetric credit: a satisfied edge (g, g′) contributes half its value to each endpoint's decomposition, so per-guest decompositions sum to $F$ over the whole solution without double counting.

## 8.3 Rendering and reporting bands

Human-readable reasons are rendered from $D(g)$ by emitting the top-magnitude components above a display floor ("Seated with 3 household members (+2.1); aso-ebi cluster 'coral set' (+0.8); FRONT row honored (+0.5)"), plus any structural annotations (household split, cluster spillover, swap-pass move provenance). Unseated verdicts carry the blocking constraint family from §4 (the engine's `unseatedReason` taxonomy, made machine-structured). Confidence bands (HIGH/MEDIUM/LOW) become thresholds over $\sum_k \delta_k(g)$ percentiles within the run — reporting only, with no feedback into optimization. Persisting $D(g)$ alongside each `AllocatorRunSuggestion` makes runs comparable across weight configurations.

---

# 9. Complexity Analysis

**Variable count.** $|G| \times |T|$ binaries: at 1000 guests × 60 tables, 60,000 primary variables; co-seating auxiliaries $y_{g,g',t}$ in a linearized exact model would add up to $\binom{1000}{2} \times 60 \approx 3 \times 10^7$, dominated in practice by the sparse relationship/household/tag pair structure (typically $10^3$–$10^4$ relevant pairs).

**Search space.** $(|T|+1)^{|G|} \approx 61^{1000} \approx 10^{1786}$ raw assignments before constraint pruning.

**Hardness.** With pairwise co-seating rewards and separations plus capacities, the problem contains **capacitated graph partitioning** (partition a weighted affinity graph into ≤ |T| parts of bounded size maximizing intra-part weight) as a special case, which is NP-hard; the anchor/row terms add positional costs making it a generalized quadratic-assignment relative. Exact optimality proofs at n = 1000 are therefore not a design goal.

**Why heuristics are appropriate and sufficient.** (i) The hard-constraint structure is shallow — feasibility of a single move is O(1) with maintained table-state (member sets, tag histograms, occupancy, expected-occupancy accumulators), so local search explores cheaply. (ii) The objective is smooth under single-guest moves and pairwise swaps: every term changes by a locally computable delta — precisely the $\delta_k$ of §8, so explainability and incremental evaluation are the *same* machinery. (iii) The acceptance bar is host-perceptual (≥ 90% satisfaction vs. manual expert planning), not proof-of-optimality. The sanctioned regime, consistent with the prior review: deterministic greedy construction (current phases 1–3) followed by a time-budgeted, seed-deterministic improvement phase (annealing/tabu over move + swap neighborhoods) with incrementally maintained state — replacing the current single O(n²)-pair pass whose per-evaluation full rescans make it effectively O(n³·R̄). A CP-SAT exact solve at table granularity remains feasible for ≤ ~300-guest events as an offline benchmark to measure the heuristic's optimality gap; it is a validation instrument, not the production path (sidecar-architecture decision already flagged).

---

# 10. Validation Criteria

An optimizer **correctly implements this model** iff all of the following hold on every run:

**V1 — Feasibility invariants (machine-checked post-run):**
- Exactly one of {placed at one table, unseated} per eligible guest; no duplicate guest in output (H1).
- $n_t \le c_t$ for all t, counting fixed assignments (H2); under overbooking, the H10 surrogate holds for all t.
- No placement at an excluded/locked-while-respected table (H3); no accessibility violation when enforced (H4); no SEPARATE pair co-seated (H5); every MUST component fully co-located or fully unseated per D2 (H6); no household-of-two split (H7); no segment-zone violation (H8).
- Fixed assignments unmoved (A5).

**V2 — Objective verification:**
- Reported $F(x)$ equals independent recomputation of $\sum_k w_k f_k(x) - P_u \sum \omega_g u_g$ from the raw solution by a separate evaluator (no shared code path with the optimizer).
- Every placement's decomposition satisfies exactness: $\sum_k \delta_k(g) = \Delta F$ within numerical tolerance $10^{-9}$; per-guest decompositions sum to $F$ over the solution (§8.2).
- Every unseated verdict's cause is *certified*: re-checking all tables against the named constraint family confirms none was feasible.

**V3 — Determinism and reproducibility:**
- Identical inputs + identical seed ⇒ byte-identical output (assignments, decompositions, diagnostics). All tie-breaks are total orders (the current score → capacity → table number → case-insensitive label chain is the normative tie-break for construction).
- The improvement phase's time budget must be expressed in iterations or evaluation counts, not wall-clock, wherever reproducibility is required; wall-clock budgets are admissible only with the caveat that reruns may differ, which must then be surfaced, not silent.

**V4 — Monotonicity and dominance checks (property tests on the model, not the code):**
- Setting all $p_g = 1$ and disabling extensions reproduces the current deterministic model's feasible set exactly.
- Increasing $w_k$ (others fixed) never decreases $f_k$ at the returned optimum (weak monotonicity under adequate search budget).
- No returned solution is dominated by unseating: for every placed guest, $\Delta F(g \to \text{tab}(g)) \ge \Delta F(g \to \varnothing)$ — the $P_u$ construction guarantees this is achievable; the check confirms the optimizer didn't strand a seatable guest.

**V5 — Benchmark harness:**
- A fixture suite of synthetic events (sizes 200 / 500 / 1000; no-show priors off / 0.75 / mixed; MUST/SEPARATE densities low / high; at least one infeasible-by-construction case per hard-constraint family, asserting the correct unseated cause).
- For fixtures ≤ 300 guests, an exact reference solution (CP-SAT, offline) establishes the optimality gap; the heuristic must achieve ≥ 95% of exact $F$ on the suite.
- Regression: the current engine's outputs on frozen fixtures are recorded as the baseline; any successor must be feasibility-equal and objective-≥ on every fixture.

---

## Open decisions (require ratification before implementation)

| # | Decision | Spec default |
|---|---|---|
| D1 | Exclude `pendingIntakeReview` guests from eligibility (schema comment vs. current engine behavior) | Exclude |
| D2 | MUST groups: all-or-unseat (spec) vs. current silent best-effort split | All-or-unseat |
| D3 | Segment caps $\kappa_s$: advisory spillover (current) vs. hard | Advisory |
| D4 | Adopt precedence rank $q_g$ (new data-model field) to decouple hierarchy from closeness | Adopt |
| D5 | Adopt zone-dispersion constraint entity (H9) for "clustered not monolithic" / rival branches | Adopt |
| D6 | Overbooking risk ε default and whether ε is operator-visible per event | ε = 0.05, operator-visible |
