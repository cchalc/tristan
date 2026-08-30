# Tristan

Project workspace for a music teacher's web presence. Planning and cross-cutting
concerns live here; the applications live in their own isolated repos, linked as
submodules.

## Structure

- `music_studio/` — the Phoenix marketing site (submodule → `cchalc/music_studio`).
- `docs/phases.md` — the phased roadmap.
- `docs/architecture.md` — why project vs app level, and how it expands.
- `AGENTS.md` — overview for AI coding agents.

## Getting started

```sh
git clone <this repo>
cd Tristan
git submodule update --init --recursive   # pull the app repo

cd music_studio
mise install                              # Elixir 1.18.4 / OTP 28 per .tool-versions
mix setup                                 # deps, DB, assets
mix phx.server                            # http://localhost:4000
```

See `music_studio/README.md` for app-specific details (local secrets, precommit, etc.).

## Tracking

- Phases and planning: `docs/phases.md` → Fizzy board.
- Dev/debug work: GitHub issues on `cchalc/music_studio`.
