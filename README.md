# Tristan

Project workspace for **Tristan**, an independent music teacher's web presence. The
planning and cross-cutting docs live here; the application lives in its own isolated
repo, linked as a submodule.

The app (`music_studio/`) is a marketing website — who Tristan is, what he teaches
(voice, piano, guitar; in-person, all ages), his rates, and an inquiry form — with a
CMS so the content can be edited without a developer.

## Technology

- **Elixir 1.18.4 / OTP 28** (pinned via [mise](https://mise.jdx.dev/)).
- **Phoenix 1.7 + LiveView 1.2** on Bandit. _(Phoenix is held at 1.7 because the CMS,
  Beacon 0.5.1, needs a pre-1.8 Phoenix API — see `music_studio/` and `../lessons.md`.)_
- **Beacon CMS** (`beacon` + `beacon_live_admin`) — DB-backed pages/layouts/components,
  admin at `/cms`.
- **PostgreSQL via Ecto**, hosted on **Neon** in development (URL in `.envrc`).
- **Tailwind CSS v4 + daisyUI**, with a Radix-inspired "Modern Minimal" token layer.
- **Swoosh** for inquiry-notification email.
- VCS: **jj** (colocated with git) for the app, plain **git** for this workspace.

## Structure

- `music_studio/` — the Phoenix marketing site (submodule → `cchalc/music_studio`).
- `docs/architecture.md` — topology + runtime architecture diagrams.
- `docs/specifications-review.md` — specified vs. delivered, tech choices, deviations.
- `docs/phases.md` — the phased roadmap.
- `checkpoint.md` / `tasks.md` / `lessons.md` — living docs (read `CLAUDE.md` for the protocol).
- `AGENTS.md` — overview for AI coding agents.

## Getting started

```sh
git clone <this repo>
cd Tristan
git submodule update --init --recursive     # pull the app repo

# Dev database: set DATABASE_URL (a Neon/Postgres connection string, a postgresql://
# URL ending in ?sslmode=require) in .envrc (git-ignored), then `direnv allow`.
# Without it, the app falls back to a local Postgres.

cd music_studio
mise install                                 # Elixir 1.18.4 / OTP 28 per .tool-versions
mix setup                                    # deps, DB, assets
mix phx.server                               # http://localhost:4000  (admin: /cms)
```

See `music_studio/README.md` for app-specific details.

## Tracking

- Phases and planning: `docs/phases.md` → Fizzy board.
- Dev/debug work: GitHub issues on `cchalc/music_studio`.
