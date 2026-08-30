# Checkpoint

_Last updated: 2026-08-29_

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

### Beacon: DEFERRED — decision made 2026-08-30 ✅
**Chris's decision: adopt Beacon CMS later; wait for upstream Phoenix 1.8 support.**
The site stays **hand-built** (the shipped `HomeLive`) for now. **Do NOT re-attempt a
Beacon install until an upstream release supports Phoenix 1.8 / Elixir 1.18** — both
current versions fail (0.5.1 uses `Phoenix.Endpoint.Supervisor.config/2`, removed in
1.8; `main` won't compile on Elixir 1.18 — regex-in-attribute error). Full detail +
manual-install recipe are in `lessons.md` (2026-08-30).

**Revisit trigger:** a new Beacon release that supports Phoenix 1.8. Check with
`mix hex.info beacon` (look past 0.5.1) or Beacon's CHANGELOG / the `endpoint_config`
+ `heex_converter` fixes. When it lands, do a **fresh** install against that release
(the parked jj change `mquosplq` will be stale — follow the `lessons.md` recipe, not the
old WIP). Porting the hand-built site into Beacon then: `ms-` design tokens → a Beacon
site stylesheet; `HomeLive` sections → a Beacon page template; the inquiry form → a
Beacon page event handler calling `Leads.create_lead/1` (already built and tested).

_Note: the requested `ralph-loop` autopilot could not be armed — the auto-mode safety
classifier blocked an unattended 40-iteration loop with no per-action approval gate.
Proceeded action-by-action instead, which each pass safety review._

## Open follow-ups

- **esbuild is fragile:** `registry.npmjs.org` is blocked on this network; the binary
  was fetched via the Databricks npm proxy and placed in `_build/` (wiped by
  `rm -rf _build`). Consider pinning `config :esbuild, path:` to a stable location.
- Rotate the Fizzy token (it was pasted in chat once) and update `.envrc`.
- Optional: open GitHub issues for Phase 1/2 tasks on `cchalc/music_studio`.
