# WideLens — Current Status

**Last updated:** 2026-08-08
**Maintained by:** Claude sessions. Update at the END of every session.

---

## 1. What WideLens is

WideLens, LLC (Baton Rouge, LA) — AI-native short-form video creation. Idea in, publishable
video out: angle → script → production (teleprompter or faceless w/ cloned voice) → captions/
music/trim → publish to TikTok, YouTube, Instagram. A **mobile iOS app** plus a marketing site.

Raising **$1M pre-seed on a SAFE**. Pricing $49 Starter / $99 Pro / $499 Premium, plus a
500-seat Founder cohort (100 @ $29, 400 @ $39, 36-month lock). Blended ARPU ≈ $79.

**The one remaining launch gate is third-party app-review approval (Meta, TikTok, YouTube)
for one-tap publishing — not engineering.**

Moat: the **Viral Script Engine** — proposes angles, generates scripts, critiques each on hook/
structure/retention, auto-regenerates weak ones, and tags every generation for per-creator
learning. Live in production.

---

## 2. Governance — READ BEFORE WRITING ANY CODE

WideLens is governed by **PSP (Poppell Software Principles)**, repo `OpusDraft/PSP`, v1.0.
`No project begins without PSP.` PSP is the single source of truth; `AGENTS.md` is a
**generated artifact — never hand-edit it**, edit the standards and regenerate.

**Governance hierarchy** (`standards/00-governance-hierarchy.md`) — higher always wins:

1. Product Constitution
2. Owner Instruction
3. Engineering Standard
4. Product Design Standard
5. Implementation Philosophy

**The WideLens Constitution** — `psp/templates/constitutions/widelens.md`, also
`PSP_MASTER_SPEC.md` §7. Ten articles. The load-bearing ones for day-to-day work:

- **Art. I** — the creator is sovereign; the system is director/advisor, never author.
- **Art. II** — propose → approve → execute. Nothing consequential runs unapproved.
- **Art. V** — never speak beyond the evidence; "not yet known" is valid and required.
- **Art. IX** — every automated choice has a visible, cheap override; irreversible or costly
  acts (publishing, spending money or reach) require deliberate consent. Friction there is a
  feature.

**Friction vs. Authority** (`standards/02-product-design-standard.md`) — WideLens variant:

- AI removes aggressively (FRICTION): defaults, formatting, routing, production choices, stock
  selection, B-roll, camera movement, scheduling, categorization, setup, mechanical decisions.
- AI must NEVER remove (AUTHORITY): identity, voice, taste, intent, creative direction,
  publication, audience, money, consent, permissions, governance, irreversible actions.

**AI Creative Director rule** (`PSP_MASTER_SPEC.md` §15.1): *WideLens is not a video editor.*
AI decides camera, captions, b-roll, motion, callouts, production style. **Human decides
script, identity, publish.**

**Planner amendment, Law 0** (`standards/06`): as uncertainty rises, authority falls. Approval
is per-plan and never blanket; the approved plan is immutable; UNKNOWN halts.

**STOP rules** (`standards/05`): STOP means stop. Only review/analysis/answers/surfacing
blockers after it. No "one last thing."

### The gatekeeper — and the exact truth about it

The gatekeeper is a **Claude Code subagent + hard commit gate**, and it currently exists in
**`OpsDirect` only**:

- `opsdirect/.claude/agents/gatekeeper.md` — adversarial reviewer, **defaults to REJECT**.
- `opsdirect/.claude/hooks/gate.mjs` — fails CLOSED. Fingerprints all uncommitted work as a
  git tree OID; a PASS receipt is bound to that exact hash and expires after 24h. Change the
  code → hash changes → the pass is stale → re-review.
- `opsdirect/.githooks/pre-commit` — aborts any commit without a fresh PASS. Requires
  `git config core.hooksPath .githooks` once per clone.
- **Caveman Pass** (`opsdirect/Caveman_Pass.md`) — a PSP governing document, peer of the
  Product Design Standard, enforced by the gatekeeper. Mandatory for user-facing features.
  Backend/infra/migration/test/docs-only changes are exempt (state the exemption explicitly).
  Definition of done: passes Engineering + Product Design + Caveman Pass + Gatekeeper.

**Two facts that matter:**

1. **WideLens has no gatekeeper.** No `.claude/`, no hooks, no `Caveman_Pass.md` in this repo.
2. The hooks are written as `cmd /c …` — **Windows-only**. They cannot fire in a Linux
   container session. Only the native `.githooks/pre-commit` is portable, and only after
   `core.hooksPath` is set.

### PSP compliance — measured 2026-08-08 with `verification/verify.py`

| Repo | Score |
| --- | --- |
| `psp` (self-check) | **10/10 PASS** |
| `opusdraft` | **1/10 FAIL** — only "CLAUDE imports AGENTS"; its `AGENTS.md` lacks the generated banner (hand-written/stale) |
| `widelens-mockup` | **0/10 FAIL** — no `AGENTS.md`, no `CLAUDE.md`, nothing |

**Verified working:** the generator produces a compliant WideLens charter. Tested into a
scratch dir, not the repo:

```bash
python3 generator/generate.py --product widelens \
  --constitution templates/constitutions/widelens.md --target <repo-root>
python3 verification/verify.py --target <repo-root>     # → 10/10 PASS
```

---

## 3. Infra IDs (verified live 2026-08-08)

| System | Identifier |
| --- | --- |
| Supabase project | "WideLens Project" — ref `swooocpvrsoyiklxydbm` |
| Vercel team | **WideLens** — slug `quiet-signal`, `team_yBLH5jTe0joIIvnOeqv9SQIi` |
| Vercel project | `marketing` — `prj_7YUaKZbEnnuBRlUw5NSmNpkmMJ5Z`, **nextjs**. The ONLY project in the team. |
| Domains | widelens.app, www.widelens.app, quietsignalapp.com, www.quietsignalapp.com |
| Last prod deploy | Jul 26 — `dpl_5bdNf2xCPiVNZPemx7k4akhynLJw`, READY, project reports `live: false` |
| Other Vercel team | `opusdrafts-projects` / `team_byCKV3wNsWdXe2iNAtmyVZUs` — quietsignal-marketing, opsdirect, opusdraft-app, wildmark-outdoors. **No WideLens app.** |
| PostHog | project "Default project" id `414585`, org OpusDraft |
| Vendors (from OpsDirect sender buckets) | ElevenLabs (voice), Mercury (banking), Stripe (shared with OpusDraft) |

widelens.app publishes a `GET /api/status` feed — built and deployed, **intentionally
returning 503 until launch** (`opusdraft/src/app/api/status/route.ts:229-231`). OpsDirect has
it wired but disabled: `opsdirect/src/config/statusFeeds.ts:27` (empty URL,
`WIDELENS_STATUS_TOKEN`), `src/config/systems.ts:34-36` (healthUrl `https://widelens.app`).

---

## 4. Live product state, from the Supabase DB

**71 migrations**, `20260512000000_foundational_tables` → `20260712191736_brand_positioning_mode`.
Nothing after Jul 12. Arc: schema → Stripe → analytics → affiliate → founder program → Meta
OAuth → voice clone → viral script engine → posting cards → moderation/cost/billing → photo
labels → RLS performance.

**In active use through Aug 3, 2026** — a week after the last deploy:
`content_agent_runs` Aug 3 13:02 UTC · `approval_events` Aug 3 00:46 · `reel_assets` /
`reel_scripts` Aug 3 00:45 · `photo_labels` Jul 26 · `brands` / `profiles` Jun 22.

**Row counts — pre-revenue confirmed:** `approval_events` 186 · `photo_labels` 125 ·
`reel_scripts` 97 · `script_events` 94 · `reel_photo_usage` 82 ·
`content_agent_suggestions` 68 · `reel_assets` 56 · `content_agent_runs` 25 ·
`billing_events` 12 · **`profiles` 6** · `brands` 7 · `affiliates` 1 · `connections` 1 ·
**`subscriptions` 0** · `referrals` / `voice_consents` / `reel_analytics` / `link_clicks` /
`card_analytics` 0.

Six profiles with ~100 scripts is founder-and-tester usage. Zero subscriptions. The empty
analytics/referral tables have schema but no production data — consistent with "the gate is
app review."

---

## 5. Access — what a session actually has

**Have:**

| Resource | Path / ID |
| --- | --- |
| `widelens-mockup` | `/home/user/widelens-mockup` — static marketing mockup, 1 commit |
| `psp` | `/workspace/psp` — governance, generator, verifier |
| `opusdraft` | `/workspace/opusdraft` — separate product (opusdraft.com) |
| `opsdirect` | `/workspace/opsdirect` — separate product; **source of the gatekeeper** |
| Supabase | full read + `apply_migration` on the WideLens project |
| Vercel | both teams, deployments, logs |
| PostHog, Intercom, Google Drive | connected |

**Do NOT have — the one real blocker:**

- **The WideLens iOS app source.** Not on GitHub. The authenticated identity is `OpusDraft`
  (1 public repo); a GitHub-wide search for "widelens" returns only `widelens-mockup` and four
  unrelated third-party repos. It is not on either Vercel team. `create_repository` returns
  **403 Resource not accessible by integration**, so a session cannot create the repo either.
- **The `marketing` Vercel project's source.** Deployed, but in no reachable repo.
- **OneDrive** — Microsoft 365 connector installed (`1b59f6a2-d948-4a55-a436-418b36e411c4`)
  but `enabledInChat: false`. Toggle it on in the conversation and it works immediately.
- **Notion** — upstream OAuth token returns `401 API token is invalid`.
- **Gmail** — installed, `enabledInChat: false`.
- **widelens.app** — blocked by this environment's network egress proxy.

`opsdirect/src/config/businesses.ts:102` carries the standing TODO:
`// widelens: '<owner/repo>',  // ← add once the WideLens app repo + nightly run exist`.

---

## 6. Outstanding work

1. **Push the WideLens app to GitHub.** Everything else is downstream of this. Owner action:
   create `OpusDraft/widelens` (private) in the UI, push, then `add_repo` here.
2. **Bring WideLens under PSP** — currently 0/10. Generator verified working (§2).
3. **No gatekeeper on WideLens** — port `.claude/agents/gatekeeper.md`, `hooks/gate.mjs`,
   `.githooks/pre-commit`, `Caveman_Pass.md` from OpsDirect, rewritten for the WideLens
   Constitution/creator (not the OpsDirect subscriber), and with the `cmd /c` Windows
   assumption removed.
4. **App-review approval (Meta/TikTok/YouTube)** — the actual launch gate.
   `index.html:599-600` has `href="#"` for Terms and Privacy, and there is no data-deletion
   link. Meta requires live Privacy Policy + Data Deletion URLs. Unknown whether the live site
   already fixes this — widelens.app is egress-blocked from here.
5. **Vercel `live: false`** on a READY Jul 26 production deploy. Unverifiable from this
   environment; check in a browser.
6. **`opusdraft` is 1/10 on PSP** — its `AGENTS.md` is stale/hand-written. Regenerate.
7. **34 `auth_allow_anonymous_sign_ins` advisories** — RLS policies granting the `anon` role
   on `profiles`, `subscriptions`, `connections`, `push_tokens`, `voice_consents`,
   `storage.objects` + 28 more. Inert if anonymous sign-ins are disabled in Auth; real
   exposure if not. **Check that setting.**
8. **Repo hygiene** — `index.html:7` says "Mockup v3", commit says v6. Two 1.9 MB unoptimized
   images.

### Done 2026-08-08

Migration `harden_function_search_path_and_revoke_public_rpc`, verified:

- `search_path = public, pg_temp` pinned on all 5 flagged trigger functions
  (`_founder_program_config_set_closes_at`, `enforce_premium_script_request`,
  `enforce_scheduled_queue_cap`, `guard_stills_no_voice_pipeline`, `touch_updated_at`).
  The `function_search_path_mutable` lint is gone.
- `REVOKE EXECUTE … FROM PUBLIC` on `handle_new_user()` and `is_admin()` — the implicit
  PUBLIC grant was how `anon` reached them. Advisory now reports `authenticated`, not `anon`.

### Deliberately NOT "fixed" — do not flip these blindly

- `founder_program_public_status` (**ERROR**, SECURITY DEFINER view, `anon=r`). Returns only
  `tier_open` and `closes_at`, counting cohort rows. No PII. It is the public endpoint the
  marketing site uses for founder pricing. `ALTER VIEW … SET (security_invoker = on)` **would
  break founder pricing**, because `anon` has no SELECT on `profiles` or
  `founder_program_config`.
- `billing_anomalies`, `connection_secrets` — RLS on, 0 policies = deny-all to anon and
  authenticated; `service_role` bypasses RLS. If the backend uses them server-side they are
  **currently secure, and adding policies would loosen them.** Reported at INFO.

---

## 7. Lost-session history (closed — do not re-investigate)

`session_01PPbMKLwCfA14UTenCztSnZ` "Widelens session recovery" (2026-08-08 12:26 UTC) produced
"handoff text + status memo". Its outcome branch `claude/widelens-session-recovery-13sigh` was
**never pushed** — GitHub has only `main` and `claude/widelens-status-recovery-7amgso`. The
content exists only in that transcript, unreadable from another session (`ListAgents` returns
none; no `send_message`/`list_events` tool). Open it in claude.ai to retrieve.

No YC-submission artifact (reported Jul 28) exists in any repo, on GitHub, or in Google Drive.
Drive's WideLens material stops 2026-06-15: `WideLens_Business_Plan.pdf`
(`1J5ydRIe6H2i4cbKXR8m4gvTsgFXCG4N7`), `WideLens_PitchDeck.pdf`
(`16zJg_b7ltTVzSZ5pW5ByBRlveHN7DUGU`), `WideLens_Executive_Summary.pdf`
(`1dfk2nqdHc8nIYhMfAGVAbly0uSFcKFSO`), `WideLens_Financial_Projections.pdf`
(`16KeT4D79vGuNRA-k8TS4h5sAmc7oSa-q`), folder `WideLens-Investor Materials`
(`1s2NBVqSdInCuPa0omvzTu5CN00dFX8IP`). OneDrive and Gmail remain unsearched (connectors off
for the chat).

**Session-start checklist:** attach all four repos; read this file; commit the memo *before*
the session ends — an outcome branch that is never pushed is not a record.

---

## Session log

| Date | What happened |
| --- | --- |
| 2026-08-08 | Recovery attempt 1 — searched widelens-mockup only, found nothing. |
| 2026-08-08 | Recovery attempt 2 — located the lost session; searched opusdraft + Drive; no YC artifact; Notion 401. |
| 2026-08-08 | Applied `harden_function_search_path_and_revoke_public_rpc`, verified. Found 34 unlisted anon RLS advisories. |
| 2026-08-08 | Full read: PSP (all 8 standards, master spec, both constitutions), gatekeeper + Caveman Pass in opsdirect, opusdraft, Supabase. Verified PSP generator produces a 10/10 WideLens charter. Confirmed the app source exists in no reachable location. |
