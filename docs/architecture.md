# Architecture: project level vs app level

## The question this answers

Where should configuration and code live — at the "project level" or the "repo
level"? In a plain Phoenix app those are the same thing (one repo == one project).
This workspace deliberately separates them so the application stays **isolated** and
**portable**, with room to add sibling projects (e.g. analytics) without interference.

## Two altitudes

### Project level — the `Tristan` repo
Cross-cutting concerns that are *about the project*, not any one application:

- Roadmap and phasing (`docs/phases.md`)
- Architecture and decisions (`docs/architecture.md`)
- Work tracking (Fizzy board, GitHub issues)
- Pointers to each application (as submodules)

No application code lives here.

### App level — the `music_studio` submodule (its own repo)
Everything tied to a running application lives inside that application's own repo:

- Phoenix contexts / business logic (`lib/music_studio/…`)
- Config split: committed `config/*.exs` vs local `config/dev.secret.exs`
  (git-ignored) vs production env vars in `config/runtime.exs`
- Its own agent config: `.claude/skills`, `AGENTS.md`, `mix precommit` gate
- Its own toolchain pin (`.tool-versions`, mise)

Because the app is its own repo, the standard Phoenix conventions apply unchanged
*inside* it, and it can be cloned, deployed, or handed off on its own.

## Why a submodule (not a monorepo folder or loose siblings)

- **Isolation:** separate git history and CI per application; the app doesn't inherit
  or pollute project-level history.
- **Linked, not detached:** the project repo records a specific app commit, so the
  workspace always points at a known-good revision — unlike loosely-referenced
  sibling repos.
- **Clean expansion:** a future analytics project becomes a second submodule beside
  `music_studio`. They share the workspace but never interfere.

Trade-off: submodules add a little ceremony (`git submodule update`, committing the
pointer when the app advances). See `../AGENTS.md` → "Submodule basics".

## Config layering inside an app (reference)

| Layer | Where | Committed? | Use |
|---|---|---|---|
| Compile-time, all envs | `config/config.exs` | yes | non-secret defaults |
| Per-env | `config/{dev,test,prod}.exs` | yes | env-specific, non-secret |
| Local dev secrets | `config/dev.secret.exs` | **no** (git-ignored) | local API keys |
| Local dev template | `config/dev.secret.example.exs` | yes | shows the shape |
| Runtime / prod secrets | `config/runtime.exs` via `System.get_env/1` | yes (reads env) | production secrets |

External SaaS integrations (billing, email, analytics — later phases) are wired as
secrets in these layers plus a thin in-app context, never hard-coded.
