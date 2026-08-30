# Specifications review — music_studio

_Last reviewed: 2026-08-30._

A review of what was **specified** (the approved design + roadmap) against what is
**delivered**, the technology choices and their rationale, and where reality deviated
from the plan and why.

- Source design spec: [`superpowers/specs/2026-08-30-music-studio-design.md`](superpowers/specs/2026-08-30-music-studio-design.md)
- Roadmap: [`phases.md`](phases.md) · Architecture: [`architecture.md`](architecture.md)
- Hard-won details behind every deviation: `../lessons.md` (dated 2026-08-30)

## 1. What this project is

A marketing website for **Tristan**, an independent classically-trained music teacher
(voice, piano, guitar; in-person, all ages). It presents who he is, what he teaches, and
his rates, and captures prospective-student inquiries. Content is intended to be editable
through a CMS so the teacher can update it without a developer.

## 2. Technology choices

| Choice | Version / form | Why | Status |
|---|---|---|---|
| Elixir / OTP | 1.18.4 / 28 (mise-pinned) | Reproducible toolchain | ✅ |
| Phoenix | **`~> 1.7`** (1.7.24) | Beacon 0.5.1 uses a Phoenix API removed in 1.8; downgraded to accommodate | ✅ (deviation — see §4) |
| Phoenix LiveView | 1.2 | Interactive UI + Beacon; supports Phoenix 1.7 so kept as-is | ✅ |
| HTTP server | Bandit | Phoenix default | ✅ |
| Database | PostgreSQL via Ecto; **Neon** (hosted) in dev | Managed Postgres; dev URL in `.envrc`, prod via env | ✅ |
| CMS | **Beacon** 0.5.1 + BeaconLiveAdmin 0.4.3 | Client wants to self-edit content; admin at `/cms` | ✅ (with styling caveat — §4) |
| CSS | Tailwind **v4** + daisyUI 5; Radix "Modern Minimal" token layer (`ms-` classes) | Clean modern aesthetic; hand-authored tokens | ✅ (Beacon-page styling pending — §4) |
| Typography | Inter (body) + Space Grotesk (display) | Grotesk, cap-height rhythm | ⚠️ system-font fallback (self-hosted woff2 not added yet) |
| Email | Swoosh | Inquiry notifications to the teacher | ✅ |
| Leads | `MusicStudio.Leads` (Ecto context) | Durable inquiry capture, independent of the CMS | ✅ |
| VCS | jj (colocated) for the app; plain git for the workspace; submodule link | Isolation + fast local iteration | ✅ |
| Deferred | EssenceUI (app-side UI), Buzz (leads/community) | Out of scope for now | ⏸ deferred by decision |

## 3. Specified vs. delivered

| Specified | Delivered | Notes |
|---|---|---|
| Beacon CMS owns the public site | Beacon installed, admin `/cms`, DB-backed pages; **but `/` is still the hand-built `HomeLive`**, Beacon serves other paths | Content not yet ported into Beacon; flip `/` is a follow-up |
| Modern-Minimal / Radix aesthetic | Token layer + `ms-` classes in `app.css`; applied to the hand-built page | Beacon-rendered pages only lightly styled (Tailwind v3/v4) |
| Single scrolling page (Hero/About/Lessons&Rates/Contact) | Built as `HomeLive`; live-verified | Section markup is portable into a Beacon page template |
| Voice / Piano / Guitar; in-person; all ages | Reflected in content + the inquiry form's instrument options | — |
| Leads context (DB) + Swoosh email | `MusicStudio.Leads` + `Lead` + migration + `Leads.Notifier`; 7 unit tests | Wired to the `HomeLive` inquiry form (3 LiveView tests) |
| Self-hosted fonts | Not yet — system grotesk fallback | Follow-up |
| Capsize type scale | Approximated via the token type scale | Real Capsize metrics a follow-up |

## 4. Key deviations and why

1. **Phoenix downgraded to 1.7 (from the 1.8 scaffold).** Beacon 0.5.1 calls
   `Phoenix.Endpoint.Supervisor.config/2`, removed in Phoenix 1.8; Beacon `main` fixes
   that but won't compile on Elixir 1.18. Chris chose to downgrade Phoenix rather than
   wait for an upstream 1.8-compatible Beacon. LiveView 1.2 supports Phoenix 1.7, so it
   stayed. The 1.8-only `Phoenix.CodeReloader` compile listener was removed.
2. **Interim hand-built site at `/`.** Rather than block on porting all content into
   Beacon, the marketing page ships as `HomeLive`; Beacon runs alongside (admin + site
   catch-all) and is DB-backed on Neon. Porting the sections into a Beacon page and
   flipping `/` is a clean follow-up.
3. **Beacon-page styling is minimal.** Beacon 0.5.1's runtime CSS assumes Tailwind v3
   (`@import "tailwindcss/base"`); the app is on Tailwind v4 + daisyUI 5. Worked around
   with a no-op `css_compiler` (`MusicStudioWeb.BeaconRuntimeCSS`) that serves a small
   base stylesheet, so Beacon boots/serves. A real v4 CSS path for Beacon pages is the
   next styling task.
4. **Beacon boot population.** Beacon seeds default content at boot via a hardcoded 15s
   call; the per-component HEEx compile exceeds it, so population was completed by looped
   boots (idempotent). Under test env Beacon runs in `:testing` mode to skip this.
5. **EssenceUI and Buzz deferred** as specified — not started.

## 5. Status by phase

- **Phase 0 — Foundation:** ✅ done.
- **Phase 1 — Design + CMS:** ✅ substantially done — design system, `HomeLive`, Beacon
  installed + Neon-backed. Follow-ups: Beacon-page styling, port content + flip `/`,
  self-hosted fonts, SEO/OG, portrait.
- **Phase 2 — Leads:** ✅ core done (context + form + email). Admin inbox later (Buzz may own it).
- **Phases 3–6** (scheduling, billing, marketing, analytics): ⬜ later.

## 6. How it's verified

- `mix precommit` (compile-as-errors, `skills.check`, format, gettext, Credo, Sobelow,
  tests) — green, 14 tests.
- Live smoke checks: `/` 200 (marketing), `/cms` 200 (admin), Beacon serves catch-all
  paths from Neon; inquiry form persists a `Lead` + sends the Swoosh email.

## 7. Open items / risks

- Beacon 0.5.1 is pre-1.0 and pins us to Phoenix 1.7 — revisit if/when a Beacon release
  targets Phoenix 1.8 (then Phoenix can move forward again).
- Tailwind v3/v4 mismatch limits Beacon-page styling until addressed.
- Neon password and Fizzy token were pasted in chat — **rotate both** (noted in `../checkpoint.md`).
- Leads may later be superseded by Buzz — built durably regardless.
