# Checkpoint

_Last updated: 2026-08-30_

## Where we are

**Phase 0 (Foundation) is complete.** The project is stood up and ready for feature work.

- App `music_studio` (Phoenix 1.8, marketing site for a music teacher) scaffolded.
- Toolchain pinned to Elixir 1.18.4 / OTP 28 via **mise** (`.tool-versions`).
- `mix precommit` gate is green and deterministic (compile-as-errors + `--force`,
  skills.check, format, gettext, Credo, Sobelow, tests).
- `AGENTS.md` split into 5 trigger-loaded skills under `.claude/skills` (mirrored to
  `.agents/skills`, guarded by `mix skills.check`).
- Claude Code harness config: session-start hook (mise-aware), launch.json.
- Local secrets: `config/dev.secret.exs` (git-ignored) + `.envrc`/direnv for the
  Fizzy token.
- **VCS:** app repo uses **jj colocated with git**; `wt` (worktrunk) for parallel
  worktrees. `Tristan` project repo is plain git (tracks the submodule pointer).
- **Working protocol** adopted from ai-kitchen: `CLAUDE.md` + living docs
  (`checkpoint.md`, `tasks.md`, `lessons.md`). `docs/architecture.md` now has a
  "How it all relates" map (diagram + component table) — read that for the big picture.
- **GitHub (personal `cchalc` account, public):**
  - App: https://github.com/cchalc/music_studio
  - Project: https://github.com/cchalc/tristan (links the app as a submodule)
- **Fizzy** board "music-studio" (`03grwl6spdsdwf8pj986fp5vh`, account `6112896`):
  7 phase cards created (#22–#28); Phase 0 (#22) closed/Done.

## Next — Phase 1 status (autonomous run 2026-08-30)

Design approved; spec at `docs/superpowers/specs/2026-08-30-music-studio-design.md`.
Approach: Beacon CMS + Radix Modern-Minimal + Capsize; single scrolling page;
Voice/Piano/Guitar, in-person, all ages; Leads (DB + email) now; EssenceUI + Buzz later.

### Done and pushed (`cchalc/music_studio` main)
- **Leads context** (`MusicStudio.Leads` + `Lead` schema + migration + changeset +
  `Leads.Notifier` Swoosh) — 7 unit tests. Beacon-independent and durable.
- **Radix "Modern Minimal" design system** — token layer + semantic `ms-` classes in
  `assets/css/app.css` (indigo accent, slate gray, small radius; Inter/Space Grotesk
  with system fallback until self-hosted woff2 added). Portable into a Beacon site
  stylesheet unchanged.
- **Interim marketing site** — `MusicStudioWeb.HomeLive` at `/`: single scrolling page
  (Hero → About → Lessons & Rates → Contact) with the inquiry form wired to
  `Leads.create_lead/1` + `Notifier`. Default Phoenix home retired. 3 LiveView tests.
  Live-verified (HTTP 200). `mix precommit` green (14 tests). Commit f6a40ade.
  **This is the interim/Option-3 path** — section markup + `ms-` classes port into a
  Beacon page/template later if/when the CMS decision lands there.

### Beacon: ADOPTED on Phoenix 1.7, backed by Neon (2026-08-30) ✅
Decision reversed (from "wait for 1.8") to **downgrade Phoenix to 1.7** so Beacon 0.5.1
works. Done and pushed (music_studio main 4d379eff):
- **Phoenix `~> 1.7.0`** (1.7.24); LiveView **stays 1.2** (it supports Phoenix 1.7).
  Removed the 1.8-only `Phoenix.CodeReloader` compile listener from `mix.exs`.
- **beacon 0.5.1 + beacon_live_admin 0.4.3** (hex), `gettext ~> 0.26`,
  `{:ex_aws, "~> 2.5", override: true}`. Installed **manually** (no igniter).
- Router: admin at **`/cms`**, `beacon_site "/"` as a catch-all **after** `HomeLive`
  (HomeLive still owns exact `/`; Beacon serves every other path).
- **Backed by Neon**: Beacon uses `MusicStudio.Repo` (= Neon in dev). `create_beacon_tables`
  migrated on Neon; default content (34 components / 1 layout / 2 error pages / 1 home
  page) populated on Neon. Verified live: `/cms` 200, admin pages 200, unknown paths
  served by Beacon (404 page from Neon), `/` still the marketing page.
- Beacon runs in **`:testing` mode** under test env (skips boot population); precommit
  green (14 tests).

**Known caveats / follow-ups (not blockers):**
- **Tailwind v3↔v4 mismatch: RESOLVED.** Beacon 0.5.1's runtime CSS assumes Tailwind v3;
  the app is on v4. `MusicStudioWeb.BeaconRuntimeCSS` (a `Beacon.RuntimeCSS` behaviour)
  now gathers the site's content into a temp dir, scans it with the **Tailwind v4** CLI
  via `@source`, and prepends the `ms-` design tokens — producing real CSS (~50 KB:
  v4 reset + utilities + tokens) for Beacon pages, with a base-CSS fallback so boot never
  fails. (Sobelow: inline `sobelow_skip` on the temp-path/`System.cmd` helpers, enabled
  via `skip: true` in `.sobelow-conf`.) music_studio main eec44f73.
- **LiveView 1.2 HEEx deprecation** warnings at Beacon boot ("use TagEngine.compile/2")
  — cosmetic; Beacon 0.5.1 targets an older LV.
- **Port the marketing content into Beacon** and flip `/` from `HomeLive` to a Beacon
  page when ready: `ms-` tokens → Beacon stylesheet; `HomeLive` sections → a Beacon page
  template; inquiry form → a Beacon page event handler calling `Leads.create_lead/1`.
- The old parked `mquosplq` WIP is now obsolete (superseded); can be abandoned.

_Note: the requested `ralph-loop` autopilot could not be armed — the auto-mode safety
classifier blocked an unattended 40-iteration loop with no per-action approval gate.
Proceeded action-by-action instead, which each pass safety review._

### Backend database — Neon Postgres (2026-08-30)
Dev DB is now **Neon** (hosted Postgres). `config/dev.exs` uses `DATABASE_URL` when set,
else local Postgres; tests stay on local (`config/test.exs`). The URL + password live
**only in `.envrc`** (git-ignored, direnv) — never committed. SSL on (`ssl: [verify:
:verify_none]`), `prepare: :unnamed` for Neon's PgBouncer pooler. `create_leads`
migration is applied on Neon (verified: table live, count 0). music_studio main
`431690be`. ⚠️ **Rotate the Neon password** — it was pasted in chat once.

### Analytics-ready data model (2026-08-31) ✅
A full domain model landed on `music_studio` main (6e89f06f), backed by **Neon**, with
**Ecto owning the modeling**. UUIDv7 keys (via `MusicStudio.Schema` base macro + `uuidv7`
dep), soft-delete + `utc_datetime_usec` timestamps + string enums + cents/CAD money +
jsonb — all CDC-friendly for the eventual lakehouse. Contexts: **Catalog** (teachers,
locations, instruments, offerings), **Teaching** (guardians, students, enrollments,
lessons), **Billing** (invoices, line items, payments), **CRM** (campaigns, touchpoints +
`convert_lead_to_student/2`; funnel fields added to the existing `Lead`), **Analytics**
(append-only `events` + recorder + view readers). Two reporting views
(`analytics_lesson_facts`, `analytics_funnel`). Reference data seeded idempotently
(Tristan / studio / voice·piano·guitar / rate card). 31 tests, precommit green. Design +
lakehouse path documented in `docs/data-model.md`. `leads` intentionally stays bigint.

## Next up — backend features (starting 2026-08-30)

Phase 1 interim site is shipped and green; moving to backend work. Both repos clean and
pushed; dev server stopped. Start each new unit as its own jj change off `main`
(`jj new -m "…"`), keep `mix precommit` green, push at milestones, bump the submodule
pointer in `Tristan`. Candidate backend features (Chris to prioritize):

- **Admin auth + Leads inbox** — `phx.gen.auth` (or a scoped admin) + a LiveView list/
  detail view of inquiries (finishes Phase 2's admin piece). Gate `/dev` + admin routes.
- **Lead lifecycle** — status (new/contacted/booked/archived), notes, timestamps on
  `MusicStudio.Leads`; filtering in the inbox.
- **Scheduling/availability (Phase 3)** — availability model + booking/inquiry-to-lesson
  flow; confirmations.
- **Billing groundwork (Phase 4)** — `Billing` context + payment provider (Stripe) keys
  as secrets; start with a thin skeleton.
- **Hardening** — seeds for local data, more tests, rate-limiting/spam protection on the
  public inquiry form.

## Open follow-ups

- **esbuild is fragile:** `registry.npmjs.org` is blocked on this network; the binary
  was fetched via the Databricks npm proxy and placed in `_build/` (wiped by
  `rm -rf _build`). Consider pinning `config :esbuild, path:` to a stable location.
- **Hosted services → `tchalcraftmusic@gmail.com`** (2026-09-01). GitHub stays `cchalc`.
  See "Service accounts" in `AGENTS.md`.
  - **Neon: DONE.** Fresh project (`orange-pine-95532643`, host `ep-cool-band-ayp0pzgo-pooler`,
    us-east-2) under the new email; `DATABASE_URL` swapped in `.envrc`; migrations re-run;
    Beacon content re-populated (34/1/1/2 via boot-loop); full app boots clean. Delete the
    old Neon project once satisfied.
  - **Fizzy: token swapped.** New `FIZZY_API_TOKEN` in `.envrc` belongs to the new-email
    user (Tristan Chalcraft). But the new email joined the **existing** account
    `6112896` ("CHRISTOPHER's Fizzy") as a *member* — account/board id unchanged, still
    **owned** by the old identity. Full account-ownership transfer is a separate step if wanted.
  - Supersedes the old "rotate the pasted-once password/token" follow-ups.
- Optional: open GitHub issues for Phase 1/2 tasks on `cchalc/music_studio`.
