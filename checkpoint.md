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

## Next

**Phase 1 — Design + CMS foundation (in progress, autonomous run started 2026-08-30).**
Design approved and spec written to `docs/superpowers/specs/2026-08-30-music-studio-design.md`.
Approach: **Beacon CMS** owns the public site; **Radix Modern-Minimal** aesthetic
(indigo/slate/small-radius) delivered via Beacon's Tailwind + **Capsize** + self-hosted
fonts; **single scrolling page** (Hero/About/Lessons&Rates/Contact); Voice/Piano/Guitar,
in-person, all ages; **Leads** context (DB + Swoosh email) built now; **EssenceUI** and
**Buzz** deferred. Being driven autonomously via a **ralph-loop** (jj per-unit commits,
`wt` worktree for the independent Leads context). Phases A→E and the definition of done
are in `tasks.md` and the plan file. Re-read those + `jj log` each iteration to orient.

## Open follow-ups

- **esbuild is fragile:** `registry.npmjs.org` is blocked on this network; the binary
  was fetched via the Databricks npm proxy and placed in `_build/` (wiped by
  `rm -rf _build`). Consider pinning `config :esbuild, path:` to a stable location.
- Rotate the Fizzy token (it was pasted in chat once) and update `.envrc`.
- Optional: open GitHub issues for Phase 1/2 tasks on `cchalc/music_studio`.
