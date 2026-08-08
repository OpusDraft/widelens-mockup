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
(Jul 26) is `READY` and `target: production`. Unexplained. Worth confirming widelens.app
actually serves traffic before assuming the site is up.

### C. Repo hygiene (this repo only, low priority)

- `index.html:7` — `<title>WideLens — Mockup v3</title>` but the sole commit is titled
  "WideLens mockup v6". Title tag is stale.
- `assets/creator-demo.png` (1.9 MB) and `assets/creator-hero.png` (1.9 MB) are unoptimized;
  repo is 12 MB, mostly images.

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

## Recovery attempt (2026-08-08)

Searched, all empty:

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

**Hypothesis, not a finding:** "~7 outstanding items" may be a fuzzy memory of the 7 open DB
advisories in section A, which do number exactly seven. Treat as a coincidence to confirm,
not as the recovered list.

---

## Session log

| Date | What happened |
| --- | --- |
| 2026-08-08 | Attempted recovery of lost session list; found repo has no docs and no history. Created this file. No code changed. |
