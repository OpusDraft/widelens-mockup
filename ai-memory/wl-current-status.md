# WideLens — Current Status

**Last updated:** 2026-08-08
**Maintained by:** Claude sessions. Update at the END of every session.

---

## ⚠️ Read this first: the prior session's list was NOT recovered

A session on 2026-08-08 was asked to recover "~7 outstanding items" written down by an
earlier session. **They are not in this repo.** See [Recovery attempt](#recovery-attempt-2026-08-08)
for exactly what was searched. Nothing below is reconstructed from that lost list — every
item here is independently verified against live infra or the repo. Do not treat this file
as a restoration of that list.

---

## Infra IDs (verified live 2026-08-08)

| System | Identifier |
| --- | --- |
| Supabase project | "WideLens Project" — ref `swooocpvrsoyiklxydbm` |
| Supabase state | 35 tables; latest migration `20260712191736_brand_positioning_mode` (Jul 12) |
| Vercel team | `quiet-signal` (`team_yBLH5jTe0joIIvnOeqv9SQIi`) |
| Vercel project | `marketing` — `prj_7YUaKZbEnnuBRlUw5NSmNpkmMJ5Z`, framework **nextjs** |
| Production domain | widelens.app (also www.widelens.app, quietsignalapp.com) |
| Last prod deploy | Jul 26 — `dpl_5bdNf2xCPiVNZPemx7k4akhynLJw`, READY, but project reports `live: false` |
| Repo (this one) | github.com/OpusDraft/widelens-mockup — public, static mockup |
| PostHog | project "Default project" id `414585`, org OpusDraft |

---

## Outstanding work items

### A. Database / security advisories (7 open, Supabase advisor)

Confirmed open as of 2026-08-08. This is the only concrete, written-down backlog that exists.

1. **ERROR** — `founder_program_public_status` is a `SECURITY DEFINER` view. Only ERROR-level
   advisory; everything else is WARN.
2. `handle_new_user()` is callable by `anon` via `/rest/v1/rpc`.
3. `is_admin()` is callable by `anon` via `/rest/v1/rpc`.
4. `billing_anomalies` — RLS enabled with **zero policies** (table is fully inaccessible, or
   fully exposed if RLS is later disabled; either way unintended).
5. `connection_secrets` — RLS enabled with **zero policies**. Same problem, higher stakes given
   the table name.
6. Five functions with mutable `search_path`.
7. `vector` extension installed in the `public` schema.

Plus, in Auth config (not a table advisory): **leaked-password protection is disabled**.

> None of these are fixable from this repo — no migrations, no SQL, no functions here. They
> need either the Supabase MCP (`apply_migration`) or the real app repo.

### B. Vercel `live: false` on a READY production deployment

`marketing` project reports `live: false` while its latest production deployment
(Jul 26) is `READY` and `target: production`. **Still unresolved.** Attempted verification on
2026-08-08 and could not complete it: `widelens.app` is blocked by this environment's network
egress proxy (`EGRESS_BLOCKED`), and `web_fetch_vercel_url` could not mint a shareable URL for
a custom domain. Check from a normal browser, or add `widelens.app` to the environment's
allowed domains.

### C. Repo hygiene (this repo only, low priority)

- `index.html:7` — `<title>WideLens — Mockup v3</title>` but the sole commit is titled
  "WideLens mockup v6". Title tag is stale.
- `assets/creator-demo.png` (1.9 MB) and `assets/creator-hero.png` (1.9 MB) are unoptimized;
  repo is 12 MB, mostly images.

---

## Live product state, read from the Supabase DB (2026-08-08)

Pulled directly from `swooocpvrsoyiklxydbm`. This is the most reliable picture of where the
product actually stands, since the app repo isn't reachable.

**71 migrations**, `20260512000000_foundational_tables` → `20260712191736_brand_positioning_mode`.
Nothing after Jul 12. The build arc is legible from the migration names: schema → Stripe →
analytics → affiliate → founder program → Meta OAuth → voice clone → viral script engine →
posting cards → moderation/cost/billing controls → photo labels → RLS performance work.

**The app was in active use through Aug 3, 2026** — three weeks after the last migration, and
a week after the Jul 26 deploy. Most recent row per table:

| Table | Last row |
| --- | --- |
| `content_agent_runs` | **2026-08-03 13:02 UTC** |
| `approval_events` | 2026-08-03 00:46 |
| `reel_assets` | 2026-08-03 00:45 |
| `reel_scripts` | 2026-08-03 00:45 |
| `photo_labels` | 2026-07-26 02:07 |
| `cost_events` | 2026-07-12 22:42 |
| `brands` / `profiles` | 2026-06-22 13:18 |

**Row counts — pre-revenue, pre-launch, consistent with the investor materials:**

| Table | Rows | | Table | Rows |
| --- | --- | --- | --- | --- |
| `approval_events` | 186 | | `profiles` | **6** |
| `photo_labels` | 125 | | `brands` | 7 |
| `reel_scripts` | 97 | | `affiliates` | 1 |
| `script_events` | 94 | | `connections` | 1 |
| `reel_photo_usage` | 82 | | **`subscriptions`** | **0** |
| `content_agent_suggestions` | 68 | | `referrals` | 0 |
| `reel_assets` | 56 | | `voice_consents` | 0 |
| `content_agent_runs` | 25 | | `reel_analytics` | 0 |
| `billing_events` | 12 | | `link_clicks` | 0 |

Reading: 6 profiles / 7 brands with ~100 scripts and ~190 approval events is founder-and-
tester usage, not customers. **`subscriptions` = 0 confirms no paying users yet.**
`reel_analytics`, `link_clicks`, `card_analytics`, `referrals` all empty — those features
have schema but no production data, matching "the remaining gate is app-review approval."

---

## The big structural gap: the app codebase is not reachable

This repo is **not** what runs widelens.app.

- This repo: one commit (`844e9ff`, "WideLens mockup v6", **May 19 2026**), a single
  43 KB static `index.html` + 11 images. No package.json, no framework, no CI, no docs.
- widelens.app: a **Next.js** app on Vercel, last deployed **Jul 26**, backed by a Supabase
  database whose migrations run through **Jul 12**.

So the live product is ~2 months ahead of this repo and built on a different stack. The
source for it is in none of the four repos this account can reach:

- `OpusDraft/opsdirect` (private, pushed Jul 10)
- `OpusDraft/opusdraft` (private, pushed Jul 5)
- `OpusDraft/PSP` (private, pushed Jul 5)
- `OpusDraft/widelens-mockup` (public, pushed May 19) ← this one

**Action needed from Paul:** identify where the WideLens Next.js app lives and attach it, or
confirm it is deliberately outside GitHub. Until then, sessions scoped to this repo can only
touch the static mockup and the live Supabase/Vercel/PostHog services via MCP.

---

## THE LOST SESSION — found, but not readable from here

`session_01PPbMKLwCfA14UTenCztSnZ` — **"Widelens session recovery"**, created 2026-08-08
12:26 UTC (16 min before the session that wrote this file), status IDLE, still in the account.

Its own post-turn summary: **"handoff text for WideLens session recovery + status memo"**.
That is the lost morning's work.

Two reasons it was invisible:

1. **It had four repos attached** — `widelens-mockup`, `opsdirect`, `opusdraft`, `psp`. The
   follow-up session was scoped to `widelens-mockup` alone, so `ai-memory/` and `docs/`
   (which exist in `opusdraft`, not here) were nowhere to be found.
2. **Its branch was never pushed.** It declared outcome branch
   `claude/widelens-session-recovery-13sigh` on `widelens-mockup`, but GitHub shows only
   `main` and `claude/widelens-status-recovery-7amgso`. Nothing was committed — the handoff
   text and status memo exist **only in that session's transcript**.

**It cannot be read programmatically from this session:** `ListAgents` returns no reachable
agents, and no `send_message` / `list_events` tool is exposed here.

> **To recover it: open `session_01PPbMKLwCfA14UTenCztSnZ` in claude.ai and copy the handoff
> text out.** That is the only path. Paste it here and it goes into this file permanently.

### Lesson (this is why this file exists)

Attach every relevant repo at session start, and **commit the memo before the session ends** —
an outcome branch that is never pushed is not a record.

---

## YC submission — July 28

Paul reports WideLens was submitted to YC on 2026-07-28. **No artifact of it is reachable
from this session.** Searched: both repos, all GitHub branches/PRs/issues, and Google Drive
(full-text `WideLens`, full-text `Y Combinator`, title `YC`, everything modified after
2026-07-20). Drive's July window contains only unrelated FWI documents.

What Drive *does* hold — the pre-seed materials that most likely fed the application, all
created 2026-06-15 by `chanpoppell57@gmail.com`:

| Document | ID |
| --- | --- |
| `WideLens_Business_Plan.pdf` | `1J5ydRIe6H2i4cbKXR8m4gvTsgFXCG4N7` |
| `WideLens_PitchDeck.pdf` | `16zJg_b7ltTVzSZ5pW5ByBRlveHN7DUGU` |
| `WideLens_Executive_Summary.pdf` | `1dfk2nqdHc8nIYhMfAGVAbly0uSFcKFSO` |
| `WideLens_Financial_Projections.pdf` | `16KeT4D79vGuNRA-k8TS4h5sAmc7oSa-q` |
| Folder: `WideLens-Investor Materials` | `1s2NBVqSdInCuPa0omvzTu5CN00dFX8IP` |

Key claims in those materials, for continuity: raising **$1M pre-seed on a SAFE**; pricing
$49 / $99 / $499 with a 500-seat Founder cohort at $29–39 locked 36 months; blended ARPU
≈ $79; **the one remaining launch gate is third-party app-review approval (Meta, TikTok,
YouTube) for one-tap publishing** — not engineering.

### Blocked sources — exact state, and the exact fix

Diagnosed 2026-08-08 via `ListConnectors` + `SearchMcpRegistry`. **OneDrive access exists on
the account; it is not switched on for the chat.** Neither of these is fixable from inside a
session — both are toggles in the claude.ai connector UI.

| Connector | Org state | In this chat | Problem | Fix |
| --- | --- | --- | --- | --- |
| **Microsoft 365** (SharePoint/**OneDrive**/Outlook/Teams) — `installedServerId 1b59f6a2-d948-4a55-a436-418b36e411c4` | installed | **`enabledInChat: false`** | Tools (`sharepoint_search`, `sharepoint_folder_search`, `outlook_email_search`, `read_resource`) were never loaded into the session — `ToolSearch` cannot reach them | **Enable Microsoft 365 in this conversation's connector settings** |
| **Notion** — `installedServerId e7d5c32f-5b9e-4b58-8d7f-c10cbeeb1225` | `connected: true` | `enabledInChat: true` | Upstream **Notion OAuth token returns 401 `API token is invalid`** on every call (`notion-search`, `notion-get-users`). No MCP tool exposes a refresh/re-auth action | **Reconnect Notion in claude.ai connector settings** |
| **Gmail** | installed | `enabledInChat: false` | Not loaded — a Jul 28 YC confirmation email would be here | Enable in this conversation if the email matters |

Once Microsoft 365 is enabled in the chat, OneDrive is searchable in the same session — no
need to start over.

**Also blocked:** `widelens.app` is denied by the environment's network egress proxy, so site
liveness cannot be checked from a session in this environment.

---

## Recovery attempt (2026-08-08)

Searched in `widelens-mockup`, all empty:

- `CLAUDE.md`, `AGENTS.md` — do not exist.
- Any `*.md` anywhere in the working tree — **zero markdown files existed** before this one.
- `ai-memory/`, `docs/` — did not exist.
- `git log --all` — exactly **1 commit** total across all refs.
- `git log --all --diff-filter=A --name-only` — only the 12 files still present were ever
  added; nothing was ever committed and later deleted.
- `git stash list`, `git reflog --all`, `git fsck --lost-found` — empty, no dangling objects.
- GitHub: **0 pull requests** (any state), **0 issues** (any state), 2 branches (`main` and
  `claude/widelens-status-recovery-7amgso`) pointing at the same commit.
- `search_code org:OpusDraft widelens filename:*.md` — 0 results.

Also searched, outside this repo:

- `OpusDraft/opusdraft` (cloned to `/workspace/opusdraft`) — a **separate product**
  (opusdraft.com). Its `ai-memory/od-current-status.md` is the naming convention this file
  follows. Only WideLens reference is a comment at `src/app/api/status/route.ts:229-231`
  noting the widelens.app status feed is built and deployed elsewhere, returning 503 until
  launch. No WideLens backlog, no YC material.
- Google Drive — see the YC section above.
- Notion / OneDrive / SharePoint — **401, connector token invalid.** Not searched.
- `ListAgents` — no reachable agents, so sibling sessions cannot be queried.

**Hypothesis, not a finding:** "~7 outstanding items" may be a fuzzy memory of the 7 open DB
advisories in section A, which do number exactly seven. Treat as a coincidence to confirm,
not as the recovered list — the real list is in the transcript of
`session_01PPbMKLwCfA14UTenCztSnZ`.

---

## Session log

| Date | What happened |
| --- | --- |
| 2026-08-08 | Recovery attempt 1: searched widelens-mockup only, found nothing. |
| 2026-08-08 | Recovery attempt 2: located the lost session (session_01PPbMKLwCfA14UTenCztSnZ, transcript-only, unreadable from here); searched opusdraft + Google Drive; found no YC artifact; Notion/OneDrive connector 401. |
| 2026-08-08 | Recovery attempt 3: diagnosed connectors (M365 installed but off-for-chat; Notion OAuth 401); pulled live product state from Supabase — 71 migrations, app in use through Aug 3, subscriptions=0. |
