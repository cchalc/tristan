# How to work in this project

This file is the working protocol (adapted from the ai-kitchen conventions).
`AGENTS.md` is the project overview; `music_studio/AGENTS.md` + its
`.claude/skills/` cover the app. Read those for *what* things are; this covers *how
to work*.

## Session protocol

**At session start, read these living docs in order:**
1. `checkpoint.md` — where we left off.
2. `tasks.md` — in-progress + next up.
3. `lessons.md` — append-only learnings; consult when stuck.
4. `docs/architecture.md` — why things are shaped this way (project vs app level).

**At session end:** overwrite `checkpoint.md` with the current state.
**On surprises / hard-won fixes:** append a dated entry to `lessons.md`.

## VCS — jj + git + worktrunk

The app repo (`music_studio/`) uses **[jj (Jujutsu)](https://jj-vcs.github.io/)** as
the primary local VCS, **colocated** with git:

- `jj` is primary for local work; **git stays authoritative for GitHub interop**.
- Publish with `jj git push` (or plain `git push` — colocated, either works).
- Fetch with `jj git fetch`; `main` is tracked (`main@origin`).
- `.jj/` is git-ignored. Don't commit it.
- Quick reference: `jj st` (status), `jj log`, `jj new` (start a change),
  `jj describe -m "…"` (set message), `jj bookmark set main -r @-` then
  `jj git push` (publish).

**Parallel work uses `wt` ([worktrunk](https://github.com/…))** — default config,
**no committed `.config/wt.toml`**. Each worktree is an isolated checkout; the app
honors `PORT` (e.g. `PORT=4001 mix phx.server`) so multiple worktrees run at once.
`.worktreeinclude` copies `config/dev.secret.exs` into each new worktree.

**The `Tristan` project repo stays on plain git** — jj's git-submodule support is
limited, and this repo mainly tracks the `music_studio` submodule pointer. When the
app advances, commit the new pointer here (`git add music_studio && git commit`).

## Commits

- Subject: imperative mood, ≤72 chars. Body explains the *why*, not the diff.
- End agent-written commit bodies with a `Co-Authored-By:` trailer naming the model.
- (Full detail in `music_studio/AGENTS.md`.)

## Safety

- **Never commit credentials.** Local secrets live in `.envrc` (loaded by
  **direnv** — `direnv allow` once) and in `music_studio/config/dev.secret.exs`.
  Both are git-ignored; double-check before committing.
- Production secrets come from env vars (`config/runtime.exs`), never files.
- CI should use **GitHub Actions secrets**, not committed files.

## Skills (app repo)

Framework guidance is split into trigger-loaded skills under
`music_studio/.claude/skills/` (mirrored to `.agents/skills/`, guarded by
`mix skills.check`). Each skill's `description` names the concrete symbols that
should load it. Read the matching skill before writing code in that area.
