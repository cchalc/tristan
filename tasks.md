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
- [x] **Phase A** — Beacon CMS **ADOPTED on Phoenix 1.7, backed by Neon** (2026-08-30).
  Downgraded Phoenix to ~1.7 (kept LiveView 1.2); admin at `/cms`, `beacon_site "/"`
  catch-all; tables + default content migrated/populated on Neon; precommit green. See
  `checkpoint.md` "Beacon: ADOPTED" + `lessons.md`.
- [x] **Beacon-page styling** — `MusicStudioWeb.BeaconRuntimeCSS` compiles Beacon content
  with Tailwind **v4** (via `@source`) + `ms-` tokens (~50 KB CSS). Pushed (eec44f73).
- [ ] **Follow-ups**: port the marketing content into Beacon + flip `/` from HomeLive;
  self-host Inter/Space Grotesk woff2; SEO meta/OG; portrait image.
- [x] **Phase B** — Radix "Modern Minimal" design system (indigo/slate/small-radius
  tokens + semantic `ms-` classes) in `assets/css/app.css`. Portable to Beacon. Pushed.
- [x] **Phase C (interim)** — `HomeLive` single page (Hero/About/Lessons&Rates/Contact)
  from `docs/info/`, wired to the Leads form. Live-verified, precommit green. Pushed
  (f6a40ade). Ports to Beacon page templates when/if that path is chosen.
- [ ] **Follow-ups**: self-host Inter/Space Grotesk woff2 (currently system fallback);
  decide CMS path (see checkpoint "Beacon decision"); SEO meta/OG/sitemap; portrait image.

## Data model (2026-08-31) ✅
- [x] Analytics-ready domain model on Neon — Catalog/Teaching/Billing/CRM/Analytics
  contexts, UUIDv7 keys, soft-delete/timestamps/enums/cents, append-only `events`, two
  reporting views, idempotent reference seeds. 31 tests, precommit green. music_studio
  6e89f06f. Docs: `docs/data-model.md`.
- [ ] **Follow-ups**: wire `Analytics.record_event/1` into context writes (lead created,
  lesson completed, payment received); richer marts; the actual Delta/lakehouse pipeline;
  admin CRUD screens for the new entities.

## Then — Phase 2+: Buzz + EssenceUI (deferred)
- [ ] Buzz integration (`wss://musicstudio.communities.buzz.xyz`) — leads on private
  channels + student community.
- [ ] EssenceUI for app-side LiveView UI (e.g. a leads/admin dashboard).

## Later (Fizzy #25–#28)
- Phase 3 — scheduling/calendar · Phase 4 — billing · Phase 5 — marketing · Phase 6 — analytics.

## Done
- [x] Phase 0 — foundation (scaffold, toolchain, precommit gate, skills, harness
  config, secrets, git/GitHub, jj/worktrunk, Fizzy cards). Fizzy #22 (closed).
