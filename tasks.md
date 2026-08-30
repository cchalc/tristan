# Tasks

Mirrors the Fizzy board (`docs/phases.md` is the full roadmap). Keep this in sync as
work moves.

## In progress — Phase 1: Design + CMS foundation (approved 2026-08-30)
Design decided (Beacon CMS + Radix Modern-Minimal + Capsize; single scrolling page;
Voice/Piano/Guitar, in-person, all ages; Leads DB+email now; EssenceUI + Buzz deferred).
Spec: `docs/superpowers/specs/2026-08-30-music-studio-design.md`. Driven autonomously
via ralph-loop. Definition of done in the plan / spec.

- [x] **Phase D (Leads core)** — `MusicStudio.Leads` + `Lead` schema + migration +
  tested changeset + `Leads.Notifier` (Swoosh). 7 tests, precommit green, pushed
  (main 128594e7). Beacon-independent; form wiring pending the site build.
- [!] **Phase A** — install Beacon — **BLOCKED**: Beacon incompatible with Phoenix
  1.8 / Elixir 1.18 (see `checkpoint.md` "Beacon decision" + `lessons.md`). Parked as
  jj change `mquosplq`. Needs Chris to choose fork-patch / downgrade / hand-built / wait.
- [~] **Phase B** — design system (Radix indigo/slate/small-radius tokens + Capsize
  type scale). Building now as portable CSS (reusable under any Beacon decision).
- [~] **Phase C** — section content/markup (Hero/About/Lessons&Rates/Contact) from
  `docs/info/`. Building as portable HEEx (ports to Beacon page templates later).
- [ ] **Phase E** — wire inquiry form to Leads + verify; revisit once the CMS path is chosen.

## Then — Phase 2+: Buzz + EssenceUI (deferred)
- [ ] Buzz integration (`wss://musicstudio.communities.buzz.xyz`) — leads on private
  channels + student community.
- [ ] EssenceUI for app-side LiveView UI (e.g. a leads/admin dashboard).

## Later (Fizzy #25–#28)
- Phase 3 — scheduling/calendar · Phase 4 — billing · Phase 5 — marketing · Phase 6 — analytics.

## Done
- [x] Phase 0 — foundation (scaffold, toolchain, precommit gate, skills, harness
  config, secrets, git/GitHub, jj/worktrunk, Fizzy cards). Fizzy #22 (closed).
