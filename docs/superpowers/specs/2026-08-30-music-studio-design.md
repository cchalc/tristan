# Music Studio — Design & CMS Foundation (Phase 1)

_Validated design spec. Date: 2026-08-30. Status: approved, in implementation._

## Problem / need

Tristan is an independent music teacher (voice, piano, guitar; classically trained —
Capilano diplomas, UVic BMus Voice; performer with Vancouver Chamber Choir, Vancouver
Opera Chorus, Laudate Singers, Vox Humana). He needs a public marketing website that
presents who he is, what he teaches, his rates, and lets prospective students reach
him — and that **he can update himself** without a developer.

Phase 0 (the Phoenix app foundation) is done. This spec covers Phase 1: the site's
design system, its content, content-management, and the inquiry flow.

## Intended outcome

A single-page marketing site — Hero → About → Lessons & Rates → Contact — with a clean,
modern "Radix Modern-Minimal" aesthetic, content managed through a CMS, and an inquiry
form that both records leads and notifies Tristan.

## Decisions

| Area | Decision | Rationale |
|---|---|---|
| Content management | **Beacon CMS** owns the public site | Tristan edits copy/sections in an admin UI; the priority per the client's request |
| Aesthetic | **Modern Minimal / Radix default** | Indigo accent, cool slate gray, grotesk type, small radius, airy whitespace — clean and professional |
| Structure | **Single scrolling page**, sticky anchored nav | Ideal for a solo teacher; everything in one scroll |
| Lessons | Voice, Piano, Guitar; in-person (local studio); all ages & levels | From the client; area named generally, not a full address |
| Styling delivery | Radix token layer in Beacon's Tailwind + **Capsize** typography; self-hosted fonts | Get the Radix look via Beacon's native path; CSP-safe fonts |
| Contact | **Leads** Ecto context (schema + migration + tested changeset) + **Swoosh** email | Client chose durability; records every inquiry and notifies Tristan |
| EssenceUI | Deferred to future app-side LiveView UI | Avoid coupling two pre-1.0 styling libraries on a client site |
| Buzz | Deferred (leads on private channels + student community) | Its own later phase |

## Design system (Modern Minimal / Radix)

- **Accent:** indigo. **Gray:** slate (cool). **Radius:** small. **Density:** generous
  whitespace, max-width container, translucent sticky nav.
- **Type:** self-hosted Inter (body) + a grotesk display face (Space Grotesk) for
  headings, with Capsize-generated `font-size`/`line-height`/trims for consistent
  cap-height rhythm.
- Encoded as CSS-variable tokens (`--accent-*`, `--gray-*`, `--radius-*`) consumed by
  Beacon's site Tailwind config + a custom site stylesheet.

## Content (single page)

- **Hero** — name, tagline (voice/piano/guitar, all ages, local area), primary CTA
  "Inquire" (anchors to Contact), portrait slot.
- **About** — bio + credentials (Capilano diplomas; UVic BMus Voice; Bea Scott
  Scholarship 2014; City of White Rock Award 2010) + ensembles.
- **Lessons & Rates** — three instrument cards (Voice, Piano, Guitar); rate table
  ($60 / 60 min, $45 / 45 min, $30 / 30 min; cash or e-transfer); all ages & levels,
  in-person local studio.
- **Contact** — inquiry form (name, email, instrument of interest, message) → persists
  a Lead + emails Tristan; success confirmation.

All copy is seeded into Beacon so it is editable in the admin.

## Architecture / units (each independently understandable & testable)

- **Beacon site (`:music_studio`)** — layout + page + reusable components + seeded
  content; served at `/`. Admin at `/cms`.
- **Design tokens + type scale** — CSS-variable token layer + Capsize scale + fonts,
  injected into the Beacon site stylesheet. No app-logic dependency.
- **`MusicStudio.Leads` context** — `Lead` schema, `create_lead/1`, changeset
  validation. Pure, Beacon-independent, fully unit-tested. Survives the future Buzz
  migration.
- **`MusicStudio.Leads.Notifier`** — Swoosh email to Tristan on new inquiry;
  destination from config secret.
- **Contact form wiring** — Beacon page event handler → `Leads.create_lead/1` +
  notifier (fallback: a mounted `ContactLive`). Well-defined seam between CMS page and
  app logic.

## Testing / verification (definition of done)

1. `mix precommit` exits 0.
2. `/` renders all four sections; responsive + theme-correct.
3. `/cms` admin loads; page/content editable; content seeded.
4. Form submit persists a `Lead` and sends the Swoosh email (dev mailbox); invalid
   input rejected with messages.
5. `Leads` context + changeset unit-tested and green.
6. Living docs updated; submodule pointer bumped in `Tristan`; work pushed.

## Risks

- Beacon is pre-1.0 (v0.5.1) — pin versions; keep Leads Beacon-independent.
- Beacon's Tailwind binary download on this restricted network (npmjs blocked; GitHub
  releases work) — validate early.
- Two CSS pipelines — keep the public site entirely in Beacon.
- Leads may be superseded by Buzz — accepted; built durably now.
- Fonts self-hosted (no CDN) for CSP.

## Out of scope (deferred)

- Buzz (`wss://musicstudio.communities.buzz.xyz`) — leads/private channels + community.
- EssenceUI — future app-side LiveView UI.
- Billing, calendar/scheduling, marketing automation, analytics (later phases).
