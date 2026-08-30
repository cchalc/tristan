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
- [⏸] **Phase A** — Beacon CMS — **DEFERRED by decision (2026-08-30): wait for upstream
  Phoenix 1.8 support, then adopt.** Incompatible today (see `checkpoint.md` "Beacon:
  DEFERRED" + `lessons.md`). Revisit trigger: a new Beacon release supporting Phoenix
  1.8 (`mix hex.info beacon`). Until then the hand-built `HomeLive` is the site.
- [x] **Phase B** — Radix "Modern Minimal" design system (indigo/slate/small-radius
  tokens + semantic `ms-` classes) in `assets/css/app.css`. Portable to Beacon. Pushed.
- [x] **Phase C (interim)** — `HomeLive` single page (Hero/About/Lessons&Rates/Contact)
  from `docs/info/`, wired to the Leads form. Live-verified, precommit green. Pushed
  (f6a40ade). Ports to Beacon page templates when/if that path is chosen.
- [ ] **Follow-ups**: self-host Inter/Space Grotesk woff2 (currently system fallback);
  decide CMS path (see checkpoint "Beacon decision"); SEO meta/OG/sitemap; portrait image.

## Then — Phase 2+: Buzz + EssenceUI (deferred)
- [ ] Buzz integration (`wss://musicstudio.communities.buzz.xyz`) — leads on private
  channels + student community.
- [ ] EssenceUI for app-side LiveView UI (e.g. a leads/admin dashboard).

## Later (Fizzy #25–#28)
- Phase 3 — scheduling/calendar · Phase 4 — billing · Phase 5 — marketing · Phase 6 — analytics.

## Done
- [x] Phase 0 — foundation (scaffold, toolchain, precommit gate, skills, harness
  config, secrets, git/GitHub, jj/worktrunk, Fizzy cards). Fizzy #22 (closed).
