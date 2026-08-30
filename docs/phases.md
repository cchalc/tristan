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

## Phase 1 — Core marketing site 🔜
The public face. Content and design; no external services yet.

- ⬜ Site layout, nav, footer, responsive shell.
- ⬜ Pages: Home, About, Lessons/Services, Contact.
- ⬜ Design pass (typography, spacing, imagery) per the `ui-and-assets` skill.
- ⬜ SEO basics (title/meta, sitemap, robots), favicon, Open Graph.

## Phase 2 — Leads / contact 🔜
Capture prospective students.

- ⬜ `Leads` context + schema + migration (name, email, message, source).
- ⬜ Contact form (LiveView) with validation via `to_form`.
- ⬜ Store inquiries; basic admin/list view.
- ⬜ (Optional) email notification to the teacher on new inquiry.

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
