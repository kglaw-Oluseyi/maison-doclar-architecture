---
artifact_id: GOV-008
name: Estate Inventory — operational source of truth
version: living
status: Ratified (DI-054); updated on every infrastructure change
effective_date: 2026-07-12
---
# Estate Inventory
Verification legend: ✅ live-verified via API this date · ◻ reported by principal / known from programme record, **unverified from this environment** (egress limits). Anything ◻ must be confirmed by principal/Cursor before being relied on operationally.

## GitHub (✅ 36 repositories under kglaw-Oluseyi, all single-branch `main`)
**Created by this programme (3):**
| Repo | Purpose | Head at inventory |
|---|---|---|
| maison-doclar-architecture | Constitutional repository | ✅ active |
| maison-doclar-knowledge-extraction | Extraction workspace + Historical Design Corpus (11 docs, Batch 1) | ✅ active |
| maison-platform | The Executive Intelligence realization monorepo (4 slices, 16 tests) | ✅ active |

**Pre-existing estate (33):** per GOV-002 classification register — Living systems (aperture *(pushed 2026-07-12 — Signature development continues)*, relay, nexus, events, attend, facegate, remote-view, pwa, guestbook, photo-wall, lookbook, giftpack, microsite, embeds, live, badges, assets, video-utility, voxlier, creative-studio, djos, macs, scribe, cleo, orion, shared, gateway, plavent, maisondoclar-djos) · Deprecated/Knowledge donors (nucleus, music, intelligence) · Reference (ESLA). `urbanac` excluded from scope (DI-001).

## Databases
| Instance | Purpose | Status |
|---|---|---|
| Neon — Platform project | maison-platform four-schema memory (identity/decisions/execution/knowledge) | ◻ provisioned by principal; **project name, branch, and connection unconfirmed to the programme** — confirm before cutover |
| Neon — estate databases | Per-app schemas for the 30+ Living systems (app_relay, app_nexus, …) | ◻ per programme record |
| Local PostgreSQL 16 | Verification instance for migrations/tests (this environment) | ✅ ephemeral, not operational |

## Railway
| Project | Status |
|---|---|
| maison-platform service | **Not yet created** — deployment prepared this date (Dockerfile, railway.toml, boot-time migrations); creation is a principal/Cursor action |
| Estate services (~30+ across Maison Doclar OS + Urbanac-excluded) | ◻ per programme record; not enumerable from this environment (no Railway API egress/token) |

## External integrations (◻ from programme record; keys live in Railway env / memory record)
Twilio (WhatsApp/SMS), Termii, Resend, Backblaze B2, ElevenLabs, HeyGen, Kling, ImagineArt, Anthropic (15 registered-pending call sites per GOV-001 migration), Transistor (rotation pending).

## CI
| Workflow | Status |
|---|---|
| maison-platform `.github/workflows/ci.yml` (typecheck + tests vs Postgres 16 service) | ✅ added this date; first run status to be verified post-push |

## Tokens
Admin fine-grained PAT (repo create + contents, estate-wide admin) — active; **rotation recommended post-stabilisation** (standing note since DI-017 execution).
