# Tasks

Mirrors the Fizzy board (`docs/phases.md` is the full roadmap). Keep this in sync as
work moves.

## In progress — Phase 1: Design + CMS foundation (approved 2026-08-30)
Design decided (Beacon CMS + Radix Modern-Minimal + Capsize; single scrolling page;
Voice/Piano/Guitar, in-person, all ages; Leads DB+email now; EssenceUI + Buzz deferred).
Spec: `docs/superpowers/specs/2026-08-30-music-studio-design.md`. Driven autonomously
via ralph-loop. Definition of done in the plan / spec.

- [ ] **Phase A** — install Beacon + BeaconLiveAdmin (spike CSS/Tailwind first);
  router (`/` site, `/cms` admin); migrations; retire default Phoenix home + daisyUI.
- [ ] **Phase B** — design system: Radix token layer (indigo/slate/small-radius) +
  Capsize type scale + self-hosted Inter/Space Grotesk in the Beacon site stylesheet;
  Beacon layout (sticky nav, footer).
- [ ] **Phase C** — Beacon components (section, card, rate_table, nav, cta) + the
  single page (Hero/About/Lessons&Rates/Contact) + seed content from `docs/info/`.
- [ ] **Phase D** — `MusicStudio.Leads` context + `Lead` schema + migration + tested
  changeset; `Leads.Notifier` (Swoosh); wire the inquiry form (Beacon event handler,
  fallback mounted LiveView).
- [ ] **Phase E** — verify (precommit; render; admin edit; form persists + emails);
  update living docs; bump submodule pointer; push.

## Then — Phase 2+: Buzz + EssenceUI (deferred)
- [ ] Buzz integration (`wss://musicstudio.communities.buzz.xyz`) — leads on private
  channels + student community.
- [ ] EssenceUI for app-side LiveView UI (e.g. a leads/admin dashboard).

## Later (Fizzy #25–#28)
- Phase 3 — scheduling/calendar · Phase 4 — billing · Phase 5 — marketing · Phase 6 — analytics.

## Done
- [x] Phase 0 — foundation (scaffold, toolchain, precommit gate, skills, harness
  config, secrets, git/GitHub, jj/worktrunk, Fizzy cards). Fizzy #22 (closed).
