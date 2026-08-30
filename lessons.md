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

### 2026-08-30 — Beacon CMS is incompatible with our Phoenix 1.8 / Elixir 1.18 toolchain
Tried to add Beacon CMS (the chosen content-management foundation). **Both viable
versions are blocked on this stack** — a genuine decision point, not a quick fix:

- **Beacon 0.5.1 (latest hex release):** calls the private `Phoenix.Endpoint.Supervisor.config/2`,
  which was **removed in Phoenix 1.8**. At boot Beacon's reachability check
  (`Beacon.Router.reachable/1`) rescues the resulting `UndefinedFunctionError` and
  logs "site … is not reachable or is invalid, it will not be started". Result: the
  admin at `/cms` loads (200) but the public site at `/` returns 500 (site never
  starts). Beacon's `mix.exs` allows `phoenix ~> 1.7` so resolution succeeds — the
  break is a runtime private-API call, not a version constraint.
- **Beacon main (0.6.0-dev):** fixes the above (`endpoint_config` now uses
  `Application.get_env/3`), and its `elixir "~> 1.14.1 or ~> 1.15"` **does include
  1.18** (`~> 1.15` = >= 1.15 < 2.0). BUT it **won't compile on Elixir 1.18.4**:
  `lib/beacon/template/heex_converter.ex` stores compiled `~r/.../` regexes in the
  `@function_replacements` **module attribute** → `cannot inject attribute … cannot
  escape #Reference` (the Elixir 1.18 change that forbids escaping compiled Regexes
  into attributes). Likely more such spots exist.

**Dependency-tree friction resolved along the way** (all in the parked WIP):
- Beacon needs `gettext ~> 0.26`; our Phoenix 1.8 scaffold pinned `~> 1.0`. gettext
  0.26 already has the `Gettext.Backend` API 1.8 uses, so `~> 0.26` is compatible.
- `mix deps.unlock --all` was needed so the resolver could re-solve with Beacon
  (hackney lock conflict otherwise).
- `ex_aws 2.4.4` (Beacon media dep) fails on 1.18 with the same `@partitions`/
  `#Reference` escape bug → pin `{:ex_aws, "~> 2.5", override: true}` (2.6.1 compiles).
- **igniter**: `~> 0.6` resolves to 0.8.3, whose generators crash on Beacon 0.5.1
  (`Igniter.Project.Module.find_module` Enumerable error) and whose deps compile only
  if `sourceror`/`rewrite`/`spitfire` are compiled first (build-order bug). Beacon
  wants igniter `>= 0.5.24` / `~> 0.5`, but `live_svelte 0.18.0` (a Beacon dep)
  forces `igniter ~> 0.6`, so 0.5.x isn't reachable. igniter isn't needed for a
  manual install anyway.

**Manual install recipe** (from `deps/beacon/lib/mix/tasks/beacon.gen.site.ex`, works
once Beacon compiles): migration `def up, do: Beacon.Migration.up(); def down, do:
Beacon.Migration.down()`; router `use Beacon.Router` + `use Beacon.LiveAdmin.Router`,
pipelines `:beacon` (`plug Beacon.Plug`) and `:beacon_admin` (`plug
Beacon.LiveAdmin.Plug`), mount `beacon_live_admin "/cms"` then `beacon_site "/",
site: :music_studio` **last** (it's a catch-all); `config :beacon, music_studio:
[site:, repo:, endpoint:, router:]` (a single site can use the app's **own**
endpoint — no proxy/per-site endpoint needed); supervise `{Beacon, sites:
[Application.fetch_env!(:beacon, :music_studio)]}` after Repo, before Endpoint.
Beacon's `beacon.gen.site` additionally builds a proxy + per-site endpoint (for
multi-site/host setups) — not required for one site at `/`.

The attempt is parked as jj change **`mquosplq`** ("WIP: Beacon CMS install —
BLOCKED …") in `music_studio`, off `main`. `main` holds the green Leads context.
**Decision needed from Chris** — see `checkpoint.md` "Beacon decision".
