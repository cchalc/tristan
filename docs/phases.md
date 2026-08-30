# Roadmap — phases

Source of truth for the phased plan. Each phase becomes a card/list on the Fizzy
board; day-to-day dev/debug items become GitHub issues on `cchalc/music_studio`.

Legend: ✅ done · 🔜 next · ⬜ later

---

## Phase 0 — Foundation ✅
Stand up the app and make it agent-friendly and reproducible.

- ✅ Generate `music_studio` Phoenix 1.8 app (Postgres, LiveView).
- ✅ Pin toolchain to Elixir 1.18.4 / OTP 28 via mise (`.tool-versions`).
- ✅ `mix precommit` gate (compile-as-errors, skills.check, format, gettext, Credo,
  Sobelow, tests) — deterministic and green.
- ✅ Split framework guidance into `.claude/skills` + guarded `.agents` mirror.
- ✅ Claude Code harness config (session-start hook, launch.json) + local-secrets
  convention (`config/dev.secret.exs`).
- ✅ Git repos: app (`cchalc/music_studio`) + this workspace, linked via submodule.

## Phase 1 — Core marketing site ✅ (interim; CMS deferred)
The public face. Single scrolling page, "Modern Minimal" (Radix-inspired) design.
Design decisions in `docs/superpowers/specs/2026-08-30-music-studio-design.md`.

- ✅ Radix design system (indigo/slate/small-radius tokens + `ms-` classes, `app.css`).
- ✅ `HomeLive` single page: Hero, About, Lessons & Rates, Contact. Sticky nav + footer.
- ⏸ **Content management via Beacon CMS — DEFERRED (decided 2026-08-30): wait for
  upstream Phoenix 1.8 support, then adopt** (site is incompatible with our Phoenix 1.8
  / Elixir 1.18 today — see `checkpoint.md` + `lessons.md`). Interim = hand-built HEEx;
  it ports into Beacon later. Until then Tristan can't self-edit content.
- ⬜ Self-host Inter/Space Grotesk woff2 (currently system fallback).
- ⬜ SEO basics (title/meta, sitemap, robots), favicon, Open Graph; portrait image.

## Phase 2 — Leads / contact ✅ (core)
Capture prospective students.

- ✅ `MusicStudio.Leads` context + `Lead` schema + migration + validated changeset.
- ✅ Contact form (in `HomeLive`) with `to_form` validation, wired to `create_lead/1`.
- ✅ Email notification to the teacher on new inquiry (`Leads.Notifier`, Swoosh).
- ⬜ Admin/list view of inquiries (deferred; Buzz may own leads later).

## Phase 3 — Scheduling / calendar ⬜
Lesson booking and availability.

- ⬜ Choose approach (in-app slots vs external calendar integration).
- ⬜ Availability model + booking flow.
- ⬜ Confirmations / reminders.

## Phase 4 — Billing ⬜
Payments for lessons / packages.

- ⬜ Integrate a payment provider (e.g. Stripe) — keys as secrets, `Billing` context.
- ⬜ One-off payments and/or subscriptions.
- ⬜ Receipts / invoice records.

## Phase 5 — Marketing ⬜
Grow and retain.

- ⬜ Newsletter / email capture (transactional + list provider).
- ⬜ Announcements / blog or updates section.

## Phase 6 — Analytics ⬜
Understand traffic and conversion.

- ⬜ Privacy-friendly web analytics (e.g. Plausible/Fathom) or GA.
- ⬜ Conversion tracking on the leads form.

---

Notes:
- External SaaS integrations (Phases 4–6) are wired as secrets + thin in-app
  contexts; nothing is hard-coded. See `architecture.md`.
- Keep `config/dev.secret.example.exs` updated as each integration lands.
