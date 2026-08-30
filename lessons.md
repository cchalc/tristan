# Lessons

Append-only. Dated entries of surprises and hard-won fixes. Consult when stuck.

---

### 2026-08-29 — GitHub EMU can't create public repos
The work account `christopher-chalcraft_data` is an Enterprise Managed User; `gh repo
create --public` fails with "Public repositories are not permitted for Enterprise
Managed Users." Public repos live under the personal `cchalc` account. Use
`gh auth switch --user cchalc` before creating public repos.

### 2026-08-29 — registry.npmjs.org is blocked; esbuild needs the proxy
This network blocks `registry.npmjs.org` (corporate). Tailwind installs fine (GitHub),
but the Elixir `esbuild` package downloads its binary directly from npmjs and fails.
Fix: `npm install esbuild@<ver>` (npm is configured for the Databricks proxy) in a temp
dir, then copy `node_modules/@esbuild/darwin-arm64/bin/esbuild` to
`_build/esbuild-darwin-arm64`. Fragile — `_build` is wiped by `rm -rf _build`.

### 2026-08-29 — no `postgres` role in the Nix Postgres
The Nix Postgres initialized with the OS user as superuser, so the stock Phoenix
`postgres`/`postgres` config failed with "role postgres does not exist." Created a
`postgres` superuser role (password `postgres`). Undo with `DROP ROLE postgres`.

### 2026-08-29 — `mix precommit` flaked with "redefining module" under warnings-as-errors
Running `mix precommit` twice failed: the compile step (run in `:test` via
`preferred_envs`, with `--warnings-as-errors`) hit spurious "redefining module
(current version loaded from _build/test beam)" warnings. Two source fixes: (1) scope
the `Phoenix.CodeReloader` compile listener in `mix.exs` to `:dev` only; (2) add
`--force` to the precommit compile so it never redefines stale-loaded beams. Gate is
now deterministic across repeated runs.

### 2026-08-29 — mise isn't auto-activated in non-interactive shells here
Bare `mix` resolves to the Nix profile, not the mise-pinned toolchain. Run app
commands as `mise exec -- mix …` from the app dir, or `eval "$(mise env -s bash)"`
first (the session-start hook does the latter).

### 2026-08-29 — jj + git submodules is rough
Colocated jj works great in the app repo (`music_studio`), but the `Tristan`
superproject has a submodule and jj's submodule support is limited — kept Tristan on
plain git. jj colocate is non-destructive; git stays authoritative for GitHub.

### 2026-08-29 — Fizzy API (Basecamp open-source) access pattern
Fizzy is open-source (`github.com/basecamp/fizzy`, API docs under `docs/api/`). Base
`https://app.fizzy.do/:account_slug/...`; `Authorization: Bearer <token>`, `Accept:
application/json`. Our account slug `6112896`, board `03grwl6spdsdwf8pj986fp5vh`
("music-studio"). Create a card: `POST /:acct/boards/:board/cards` with
`{"card":{"title","description"}}` → lands in **triage** (no column). Close/reopen:
`POST|DELETE /:acct/cards/:number/closure`. Token is in `Tristan/.envrc` (direnv) —
run scripts with `direnv exec . …` so the token never lands in logs. Verify a token:
`GET https://app.fizzy.do/my/identity`.
