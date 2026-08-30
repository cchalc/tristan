# Tristan — project workspace

This is the **project/workspace repo** for a music teacher's web presence. It holds
cross-cutting concerns — planning, phased roadmap, and links to the actual
applications — while each application lives in its own **isolated** git repo,
included here as a submodule.

## Layout

```
Tristan/                 # this repo — project workspace
├─ AGENTS.md             # you are here: project-level overview
├─ CLAUDE.md             # working protocol: session ritual, VCS (jj/wt), safety
├─ checkpoint.md         # where we left off (read first each session)
├─ tasks.md              # in-progress + next up
├─ lessons.md            # append-only learnings (consult when stuck)
├─ README.md             # human onboarding
├─ docs/
│  ├─ phases.md          # phased roadmap (source for Fizzy cards)
│  └─ architecture.md    # why project vs app level; expansion path
└─ music_studio/         # SUBMODULE -> github.com/cchalc/music_studio
```

## Working here (read `CLAUDE.md`)

**Start each session** by reading `checkpoint.md` → `tasks.md` → `lessons.md` →
`docs/architecture.md`. Update `checkpoint.md` at session end; append to `lessons.md`
on surprises. VCS: the app repo uses **jj colocated with git** + **`wt`** (worktrunk)
for parallel worktrees; this project repo stays plain git. Full protocol in `CLAUDE.md`.

- **Project level (this repo):** roadmap, decisions, tracking, and pointers. No
  application code. Agent guidance here is about *running the project*.
- **App level (`music_studio/` submodule):** the Phoenix app and everything tied to
  it — contexts, config, secrets handling, and its own `.claude/skills`, `AGENTS.md`,
  and `mix precommit` gate. Agent guidance there is about *the code*.
- A future **analytics project** will be added as a second isolated submodule
  alongside `music_studio`, so the two never interfere.

See `docs/architecture.md` for the full rationale.

## Working in the app

Application work happens **inside the submodule**, which is a self-contained Phoenix
project with its own tooling and agent skills:

```sh
cd music_studio        # then follow music_studio/AGENTS.md
```

`music_studio` pins its toolchain (Elixir 1.18.4 / OTP 28) via mise and gates every
change with `mix precommit`. Read `music_studio/AGENTS.md` before writing app code.

## Submodule basics

```sh
git submodule update --init --recursive   # after cloning this repo
git submodule update --remote music_studio # pull the latest app commit
```

When you advance the app, commit the new submodule pointer here so the project repo
tracks a known-good app revision.

## Tracking

- `docs/phases.md` is the roadmap and the source for **Fizzy** cards.
- Day-to-day dev/debug work is tracked as **GitHub issues** on the app repo
  (`cchalc/music_studio`).
