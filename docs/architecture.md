# Architecture: project level vs app level

## The question this answers

Where should configuration and code live — at the "project level" or the "repo
level"? In a plain Phoenix app those are the same thing (one repo == one project).
This workspace deliberately separates them so the application stays **isolated** and
**portable**, with room to add sibling projects (e.g. analytics) without interference.

## How it all relates

```mermaid
flowchart TB
  subgraph gh["GitHub · personal account cchalc"]
    ght["tristan repo"]
    ghm["music_studio repo"]
  end

  subgraph ws["Local workspace"]
    t["Tristan/ — project repo (plain git)"]
    subgraph app["music_studio/ — submodule (jj colocated + git)"]
      libc["lib/music_studio/ — contexts / business logic"]
      libw["lib/music_studio_web/ — LiveView, router, components"]
      cfg["config/ — config·dev·test·prod·runtime + dev.secret.exs (git-ignored)"]
      sk[".claude/skills/ → .agents/skills (mirror, guarded by mix skills.check)"]
      tc[".tool-versions — Elixir 1.18.4 / OTP 28 via mise"]
    end
  end

  fz["Fizzy board 'music-studio'"]

  t -- "records submodule pointer" --> app
  t -- "git push" --> ght
  app -- "jj git push / git push" --> ghm
  t -- "docs/phases.md ⇄ cards" --> fz
```

**The pieces and how they connect:**

| Piece | Where | Related to |
|---|---|---|
| Project repo | `Tristan/` (plain git) → `cchalc/tristan` | Holds planning + a **submodule pointer** to the app; tracked to Fizzy via `docs/phases.md`. |
| App repo | `music_studio/` (jj + git) → `cchalc/music_studio` | The Phoenix app; included in `Tristan` as a submodule. Push with `jj git push`. |
| Contexts | `music_studio/lib/music_studio/` | Business logic per domain (leads, later billing/etc.). |
| Web | `music_studio/lib/music_studio_web/` | LiveView, router, components; calls into contexts. |
| Config layers | `music_studio/config/` | Committed `*.exs` + git-ignored `dev.secret.exs`; prod reads env in `runtime.exs`. |
| Agent skills | `music_studio/.claude/skills/` | Mirrored to `.agents/skills`, guarded by `mix skills.check`; loaded by trigger. |
| Toolchain | `music_studio/.tool-versions` | Elixir/OTP pinned, provisioned by mise. |
| Tracking | Fizzy board + GitHub issues | Phases ↔ Fizzy cards; dev/debug ↔ issues on `cchalc/music_studio`. |

Session continuity lives in the project repo's living docs (`checkpoint.md`, `tasks.md`,
`lessons.md`) — see `../CLAUDE.md` for the working protocol.

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
