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
| PostHog — **WideLens** | org **"QuietSignal"**, project **`421406`**. Keys: `WIDELENS_POSTHOG_API_KEY`, `WIDELENS_POSTHOG_PROJECT_ID`. Signup event is **`Application Installed`** (it's a mobile app — no `signup_completed`); `$pageview` fires from the marketing site. Verified live 2026-06-05 per `opsdirect/src/lib/connectors/productAnalytics.ts:28-52`. |
| PostHog — OpusDraft (NOT WideLens) | project `414585`, org OpusDraft. Different org, different key. |
| Stripe | WideLens has its **own Stripe account**, separate from OpusDraft's — `STRIPE_WIDELENS_API_KEY`, provider key `stripe-widelens` (`opsdirect/src/lib/accounts.ts:151-155,234-236,314-322`) |
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

- **The WideLens app source lives under a DIFFERENT GitHub org: `widelensapp`.**
  This was found 2026-08-08 in OpsDirect, which routes CI mail from that org:
  - `opsdirect/src/config/businesses.ts:135` — `{ match: 'widelensapp/', bucket: 'widelens' }`
  - CI fixtures naming **`widelensapp/widelens`** and **`widelensapp/marketing`**:
    `tests/ci-collapse.test.ts:15,18,25-27,33,49-50`, `tests/bucket-read.test.ts:62-63`,
    `tests/repo-map.test.ts:11`, `tests/fixtures/inbox-golden.ts:85`

  Earlier sessions searched owner `OpusDraft` and concluded "the app was never pushed."
  **That conclusion was wrong** — it was searching the wrong org.

  **It still cannot be reached from a session rooted at an `opusdraft` repo.** `add_repo`
  refuses: *"cross-tier adds are not supported in v1: requested widelensapp/widelens but
  session already has repos from owner(s) [opusdraft]."* The GitHub MCP likewise denies it:
  *"not configured for this session."* `org:widelensapp` search returns 422 (private or
  unauthorized to this token).

  > **THE FIX: start a session with `widelensapp/widelens` as the INITIAL source.** Not an
  > add-on to an opusdraft session — the initial source. Same for `widelensapp/marketing`.
- **The `marketing` Vercel project's source** is presumably `widelensapp/marketing`. It is
  Next.js + Tailwind — `opsdirect/src/app/globals.css:9` cites "Palette source: WideLens
  marketing **tailwind.config.ts**".
- **OneDrive** — Microsoft 365 connector installed (`1b59f6a2-d948-4a55-a436-418b36e411c4`)
  but `enabledInChat: false`. Toggle it on in the conversation and it works immediately.
- **Notion** — upstream OAuth token returns `401 API token is invalid`.
- **Gmail** — installed, `enabledInChat: false`.
- **widelens.app** — blocked by this environment's network egress proxy.

`opsdirect/src/config/businesses.ts:102` carries the standing TODO:
`// widelens: '<owner/repo>',  // ← add once the WideLens app repo + nightly run exist`.

---

## 6. Outstanding work

1. **Open a session rooted at `widelensapp/widelens`.** The app repo exists; it is simply in
   another org and unreachable from an `opusdraft`-rooted session (see §5). Nothing is
   missing — this is a session-scoping problem, not a lost-code problem.
0. **The marketing site collects ZERO emails.** `index.html` has **no `<form>` elements and
   no `<script>` tags at all**. Both capture boxes are bare `<input type="email">` + `<button>`
   with no `name`, no `id`, no handler, no action: the lead magnet (`:467-477`, "Send me the
   PDF" `:474`) and the waitlist (`:580-590`, "Notify me" `:587`). **Every CTA on the page
   funnels to `#waitlist`, which drops the address on the floor.** The billing Monthly/Annual
   toggle (`:506-507`) and the sticky-bar dismiss (`:264`) are equally inert. If the live site
   shares this markup, every signup since launch has been lost — verify against the live site
   first, since this repo is the May 19 mockup.
0. **Broken links in `index.html`:** `href="#"` at `:598` (Affiliate), `:599` (Terms),
   `:600` (Privacy). `#about` referenced at `:273`, `:286`, `:596` but **no element has
   `id="about"`**. `/affiliate` at `:434` does not exist. No data-deletion link anywhere.
0. **Pricing contradicts itself in three places.** The page says first **100 @ $29** then
   **250 @ $39** = 350 seats (`:262`, `:485`); the investor materials say a **500-seat**
   cohort (100 @ $29, **400** @ $39); the Premium card says "**38 of 40 left**" (`:545`), a
   third unrelated number. Premium is also "$499/mo, locked for life, **$749/mo after
   launch**" (`:549-550`) — a claim that appears nowhere else. Pick one and make it true
   everywhere before app review or investor diligence.
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

## 4b. Supabase deep inventory (2026-08-08) — what is built vs. never exercised

35 tables, 1 view, **2 edge functions**, 3 storage buckets, 75 migrations, **0 custom enum
types** (every state machine is a `text` column + CHECK — invisible to
`generate_typescript_types`, must be hand-synced). Postgres 17.6.1.121, us-east-1.

**Most backend logic is NOT in Supabase — it is in Trigger.dev.** The `stripe-webhook` edge
function verifies the signature then forwards to `api.trigger.dev/api/v1/tasks/stripe-webhook/trigger`.
Trigger.dev is therefore a fourth infra dependency alongside Supabase/Vercel/PostHog.

### 🔴 Three things to fix before any real user touches this

1. **`stripe-webhook` can be forged.** The function runs with `verify_jwt=false` and contains a
   **stub mode: if `STRIPE_WEBHOOK_SECRET` is unset it skips signature verification entirely**
   and trusts any POSTed JSON. Unset secret ⇒ anyone on the internet can forge
   `customer.subscription.created`. **Confirm that secret is set in production.**
2. **`voice_consents` is EMPTY (0 rows) while voice cloning is already live** — 1 profile has a
   `voice_id`, and there are 4 ElevenLabs `cost_events`. Cloned voice is being generated with
   **no consent record captured**. Legal exposure, and squarely against Constitution Art. VII
   (experimentation is consented) and Art. X (memory serves the creator).
3. **No admin exists.** `is_admin = false` on all 6 profiles. Nine RLS policies branch on
   `is_admin()`, and `founder_program_config_admin_read`, `script_requests_delete_admin`, and
   `affiliates_update_admin` are admin-only. **Right now no human can read the founder config
   or approve an affiliate through the client** — everything admin-shaped requires service-role.

Also: **`delete-account` degrades silently** — if `STRIPE_SECRET_KEY` is unset it logs
"subscription NOT canceled" and deletes the user anyway. A deleted user keeps getting billed.

### 🔴 The core product finding: the loop is open at both ends

Reels get made and approved — then stop.

| Feature | Rows | Last write |
| --- | --- | --- |
| Reel assets / scripts | 56 / 97 (27 approved, 42 with a final video) | 2026-08-03 |
| Content agent | 25 runs, 68 suggestions (**only 1 of 68 accepted**) | 2026-08-03 |
| **`scheduled_posts`** | **0** | **never** |
| **`reel_analytics`** / `card_analytics` | **0** | **never** |
| `moderation_events` | 0 | never |
| `tracked_links` / `link_clicks` | 0 | never |

**Publishing has never run once.** No analytics have ever been fetched. Which means
`content_agent_runs.phase` can never progress `general → blended → personal`, because
"personal" needs performance data that has never been collected. **The learning engine — the
stated moat — cannot start learning until something publishes and metrics come back.**

### The founder program is built and fully unlaunched — ONE switch turns it on

`founder_program_config` (singleton): `program_opened_at` **NULL**, `program_closes_at` NULL,
`internal_cap_29` **100**, `internal_cap_39` **250**, `internal_cap_total` 350.
The public view currently returns `tier_open = 'closed'`.

**Zero profiles in `founder_29`. Zero in `founder_39`. Zero founder locks.**

Setting `program_opened_at` fires `_founder_program_config_set_closes_at()`, which hardcodes a
**90-day window** (not configurable), and the view immediately starts returning `founder_29`.

> **This resolves the cohort contradiction.** The database says **100 @ $29 + 250 @ $39 = 350**,
> and `index.html:262,485` says the same. **The investor deck's "500-seat (100 + 400)" is the
> outlier and is wrong.** Fix the deck, not the code. (`internal_cap_total` is decorative — the
> view never reads it.)

### Built but never exercised (12 empty tables)

`scheduled_posts` · `reel_analytics` · `card_analytics` · `tracked_links` · `link_clicks` ·
`referrals` (1 affiliate, 0 commissions) · `subscriptions` (**dead — redundant with
`profiles.stripe_*`, which is what's actually populated**) · `billing_anomalies` ·
`moderation_events` · `voice_consents` · `script_requests` (**Premium "Paul writes it" queue
has never received a request despite 2 premium profiles**; 0 scripts with `source='paul'`) ·
`reel_series`.

Columns built but never populated: `reel_assets.production_plan` (0/56),
`reel_scripts.script_features` (33/97), `profiles.referral_code` (**0/6 — the entire
friend-referral system cannot function without codes**), `profiles.stripe_subscription_id` (0/6).

### Storage

| Bucket | Public | Limit | Objects | Size |
| --- | --- | --- | --- | --- |
| `reel-assets` | no | 2 GB, MIME allowlist | 335 | **1.38 GB** |
| `app-installs` | **yes** | none, no allowlist | 4 | 19.6 MB |
| `music-previews` | **yes** | none, no allowlist | 8 | 2.6 MB |

`reel-assets` has 4 correct per-user policies keyed on `(storage.foldername(name))[1] =
auth.uid()::text`. The two public buckets have no size limit and no MIME allowlist — a footgun
if a client write path is ever opened.

### RLS — why 40 anon advisories are noise, and the one real grant

**All 35 tables have RLS on**; 33 have policies. **Every policy targets role `{public}`, not
`{authenticated}`** — and `public` includes `anon`, which is the entire cause of the 40
`auth_allow_anonymous_sign_ins` advisories. In practice every predicate reduces to
`customer_id = auth.uid()`, and `auth.uid()` is NULL for anon, so **no rows ever match.**
Re-scope to `TO authenticated` for defense in depth and to clear the advisor — but this is
**not** live exposure.

**`anon` has exactly one grant in the entire public schema:** SELECT on
`founder_program_public_status`. No table grants `anon` anything.

Well-designed details worth preserving: `moderation_events` lets users file `appeal` only
(CHECK forbids self-issued `block`/`override`); `script_requests_premium_only` enforces the
Premium entitlement **in the database**, not just the UI; `photo_labels` has the strongest
constraint in the schema — status `assessed` requires `observations` be an object AND `model`,
`objective_rubric_version`, `assessed_at` all non-null.

### Residue and inconsistencies

- **Abandoned "overlook" POC** (3 migrations on 2026-05-29, incl. one named `_no_auth`, dropped
  same day). Residue: **`vector` (pgvector 0.8.0) still installed in `public`** with ~100
  functions granted to anon/authenticated, and **no table anywhere uses a vector column.**
  Safe to drop outright.
- `subscriptions.tier` omits `trial` but `profiles.tier` includes it — vocabularies disagree.
- `card_analytics.platform` allows only `instagram|facebook`, but `scheduled_posts.platforms`
  allows all four — a card posted to TikTok/YouTube could never record analytics.
- `photo_labels` and `reel_series` have `updated_at` but **no `touch_updated_at` trigger**.
- `billing_anomalies` grants `authenticated` full CRUD at the GRANT layer while
  `connection_secrets` grants nothing — inconsistent; tighten `billing_anomalies`.
- `guard_stills_no_voice_pipeline()` **mutates status rather than raising** — a caller setting
  `awaiting_voice` on a cinematic-stills reel silently gets a different status back, no error.

---

## 4c. 🔴 SECURITY — Supabase tokens are leaking into PostHog (found 2026-08-08)

**Supabase `access_token` AND `refresh_token` values are being captured verbatim** in the
`$current_url` property of `$pageview` / `$pageleave` events in PostHog project **421406**.

Cause: the auth callback returns tokens in the **URL fragment**, and the PostHog web SDK
records the full URL. Decoded JWT payloads expose **`pcpoppell@live.com`**,
**`chanpoppell57@gmail.com`**, Supabase user IDs, session IDs, and the project ref
`swooocpvrsoyiklxydbm`.

The observed **access** tokens have expired. **Refresh tokens do not expire on the same
schedule and may still be redeemable.**

**Fix, in order:**
1. Revoke/rotate the affected Supabase sessions.
2. Strip URL fragments before capture — PostHog `sanitize_properties` or
   `mask_personal_data_properties`.
3. Change the auth callback to consume the fragment then `history.replaceState` **before**
   analytics fires.

Related, same source: **24 auth-callback failures** with
`error=access_denied&error_code=otp_expired` ("Email link is invalid or has expired") across
`/auth/callback` and `/dashboard/auth/callback`. **Magic-link expiry is a live UX problem on
the login path.**

---

## 4d. PostHog reality check — the moat emits no telemetry

Project **421406** (org QuietSignal) is live as of 2026-08-07 and reachable with existing
credentials — **no `WIDELENS_POSTHOG_API_KEY` needed**, the MCP connection already has
owner-level access to both orgs.

**~2,925 events all-time, 13 event names.** ~90% (2,620) come from three accounts:
`pcpoppell@live.com` (2,402 events, 82%), `golden-path@widelens.test` (a synthetic QA rig, 110),
`chanpoppell57@gmail.com` (108). The 26 `Application Installed` events are **2 people
reinstalling**, not 26 users. **No non-founder account has ever signed in.** One
`checkout_started`, ever (2026-07-01).

**There is no product instrumentation at all.** A property scan for
`reel|script|creator|video|render|publish|plan|tier` across all non-web events returned
**one** match. Zero events for script generation, angle selection, hook ranking, critic
scoring, render, caption, voice clone, teleprompter, publish, trial start, or subscription.
Only mobile lifecycle (open/background/active/install/update) plus auth.

> **The Viral Script Engine — the stated moat — emits no telemetry whatsoever.** PostHog can
> neither corroborate nor refute "live and proven in production," and cannot produce the
> cohort-retention data the Reality doc says investors will demand.

**Live marketing-site routes** (from real pageviews — this is the best available map of the
unreachable `marketing` repo): `/` (134 hits), `/dashboard` (22), `/dashboard/login` (14),
`/features` (12), `/affiliate` (9), `/#waitlist` (7), `/pricing` (6), `/subscribe?from=app` (4),
`/credits` (3), `/dashboard/analytics`, `/#beta`, **`/terms`**, **`/privacy`**, and
`/install.html.` — note the **trailing-dot typo, a broken install link.**

> **Correction to §8:** `/terms` and `/privacy` **do exist on the live site.** The dead
> `href="#"` links are only in this repo's May 19 mockup. Verify their *content* meets the
> platform requirements rather than assuming they're missing.

Three **live** Stripe checkout-success URLs are present (`cs_live_b1qbeoW3…`,
`cs_live_b1GYFYyfK…`, `cs_live_b1oy8kq7Y…`) — founder tests, consistent with "Stripe live and
end-to-end tested."

---

## 4e. Investor materials — integrity gaps found 2026-08-08

Drive holds 4 PDFs (all June 2026, plus duplicate copies in My Drive root) and one internal
`WideLens_Financials_Reality_2026-05-31.md`. **No WideLens file has been touched since
2026-06-15.** No YC material of any kind exists in Drive.

**Referenced by the Reality doc but absent from Drive:** `WideLens Scale Plan.docx`,
`WideLens Build Plan.docx`, `WideLens_Financials_2026-05-20.md`,
`WideLens_Financials_Reality_2026-05-20.md`. The documents holding the *source* GTM and phasing
logic are not in Drive — likely OneDrive.

The headline ARR/subscriber tables in the PDFs match the Reality doc exactly. The problems are
omissions and one arithmetic conflict:

1. **The moat claim overstates what exists.** Business Plan: tagging "is the **foundation for**
   an engine that learns." Executive Summary: "**building toward** an engine that learns." The
   live component is the *critic* (scores a script in isolation). The **per-creator performance
   loop is not built** — `adley-viral-playbook.md` (2026-06-10) lists "publish → pull real
   analytics → tune per niche" as work **to build**, and says "**the loop is the moat.**" The
   PDF line *"Live and proven in production today — not a roadmap promise"* is true of the
   critic and **misleading about the loop.** §4d confirms: zero telemetry. §4b confirms: zero
   analytics rows.
2. **ARPU framing.** PDFs say "blended standard ARPU ≈ $79." *Standard* is load-bearing and
   unexplained — actual Y1 blended is **$55 / $64 / $66**, because the founder cohort drags it
   down. An investor reads $79 and overestimates Y1–Y2 revenue per sub by 20–30%.
3. **The 36-month founder lock never actually expires.** Per `founder-lock-sweep.ts` the lock
   pays "lower of current standard or signup price," so founders stay at $29/$39
   **indefinitely** — a permanent ~$252K/yr drag decaying only by churn (~$11K/mo at Y5). Every
   investor doc says "36-month lock," implying expiry.
4. **Premium is capacity-gated in code at 40 seats** (seat 41+ sees a waitlist until a
   writer-hire flag flips). The PDFs present $499 Premium as an open tier — and Premium carries
   $24.95 of the $79 blended ARPU.
5. **Two discount programs are absent from all investor material:** Cohort 2 (25% off annual
   for the first 1,000 post-founding Starter buyers, ~$122.5K Y2) and a 50%-off-first-month
   bio-link promo.
6. **The ask undershoots its own milestone.** All docs say the raise funds "the first 1,000
   paying subscribers." The Reality ladder puts 1,000 subs at **$696K ARR**; $1M needs
   **~1,320**.
7. **Stretch Y1 contradicts itself inside one document** — Reality §3 says $1.64M, §5 says
   $1.58M; the PDF published $1.58M.
8. **Collected revenue ≈ half of exit ARR** (Y1 ~$288K / ~$540K / ~$822K). PDFs quote exit ARR
   only.
9. **Founder-bio conflicts across the set:** Business Plan and Exec Summary say *"A year ago,
   at 67"*; the Pitch Deck says *"**Two years ago**, at 67."* Business Plan and Exec Summary
   anonymize the employer to "a global energy-services company"; the **Pitch Deck names TD
   Williamson.**
10. **No cost model exists anywhere** — no burn, opex, COGS, gross margin, AI/inference cost
    per user, render cost, or runway. Revenue only. Hiring triggers are defined but **no salary
    figures for any role.**
11. **Reality's own verdict on itself:** *"This document is internal planning. It is not
    investor-ready."* Its numbers shipped into four investor PDFs three weeks later.

### ⚠️ Structural issue for the raise

**WideLens, LLC is a Louisiana LLC. A SAFE is a convertible instrument designed for
C-corporations.** No document mentions a planned Delaware conversion or reincorporation — which
most institutional pre-seed investors require before wiring. **No SAFE terms are stated
anywhere**: no valuation cap, no discount, no MFN, no pre-/post-money designation, no target
close, no minimum check. The word "SAFE" appears only in "raising $1M pre-seed on a SAFE."

Also absent from Drive entirely: investor pipeline, target list, cap table, founder ownership,
existing investors, data room beyond the four PDFs, diligence responses.

**Commingling note for diligence:** `OpsDirect_Agent_Build_Sketch.md` is headed "Prepared for
Paul · **WideLens, LLC (Claude for Startups)**" and instructs billing OpsDirect API calls
"through the WideLens console key." WideLens, LLC is the entity of record for a second product
line and holds Claude for Startups credits — a non-dilutive resource mentioned in no investor
document.

### Founder cohort — the contradiction is now fully resolved

**Drive/investor PDFs say 500 seats (100 @ $29 + 400 @ $39).** The **database says 350**
(`internal_cap_29` 100, `internal_cap_39` 250) and **`index.html:262,485` says 350.**
Two independent live sources agree on 350. **The investor materials are the outlier — fix the
deck.**

---

## 8. App-review playbook — the actual launch gate (researched 2026-08-08)

### ⚠️ Tier 0 — one problem blocks all three platforms at once

**`widelens.app` has no public footprint.** The domain returns **nothing in search results** —
unindexed, noindexed, or not serving. Independently, Vercel reports the project `live: false`
despite a READY Jul 26 production deploy. **Two unrelated signals pointing the same way.**

All three reviewers independently verify that a real, publicly reachable product exists.
TikTok explicitly rejects submissions that "look like internal tools, side projects, or
demos." A reviewer googling WideLens today finds a Dubai marketing agency and some fisheye
camera apps. **This is a first-order rejection risk on all three platforms simultaneously,
and nothing else on this list matters until it is fixed.**

Also confirmed absent: no App Store listing, no Product Hunt, no Crunchbase, no LinkedIn
company page, no YC directory entry (applications are private, so that last one is
uninformative).

**Name collision worth a trademark check:** an App Store developer account named
**"WIDELENS FOR MARKETING SERVICES VIA SOCIAL MEDIA CO."** (Dubai) already operates in
social-media marketing, plus `widelens.partners` (advisory) and `widelens.info` (Dubai
agency). This also explains why organic search will stay muddy.

### The universal prerequisites (build once, unblocks all three)

1. Live, publicly reachable, indexable marketing site at `widelens.app` — real product
   description, screenshots, English. Not a waitlist. Renders without JS for a crawler.
2. `https://widelens.app/privacy` — same root domain. Must **explicitly name** Meta/Instagram,
   TikTok, and Google/YouTube data; what is collected, why, retention, deletion.
   **Write it AFTER the integrations exist and date it accordingly** — a policy predating the
   integration is a named YouTube rejection reason.
3. `https://widelens.app/terms`.
4. A data-deletion path (form differs per platform).
5. A signup flow a reviewer can complete unaided + a pre-provisioned reviewer test account.
6. **Three separate screen recordings**, one per platform: sign up → connect → OAuth consent
   with scopes visible → compose → publish → confirmation.

Current state: `index.html:598-600` has `href="#"` for Affiliate/Terms/Privacy, and no
data-deletion link exists. Items 1–4 are unmet in this repo.

### Meta / Instagram — hardest, longest lead. Start today.

**Pick the auth path first; picking wrong costs weeks.** Recommendation: **Instagram Login**
(`instagram_business_content_publish` + `instagram_business_basic`, host `graph.instagram.com`)
— the creator does **not** need a linked Facebook Page. The Facebook-Login path
(`instagram_content_publish` + `instagram_basic` + `pages_show_list` + `pages_read_engagement`)
requires one, which is onboarding friction and support tickets. Only take it if you also need
to publish to Facebook Pages or need Business Discovery.
*(Unverified: one source lists the FB-Login permission as `instagram_publish_content`; likely
transposed. Confirm in the App Dashboard permission picker.)*

Advanced Access is required the moment a stranger connects an account, and requires **App
Review AND Business Verification**.

**Business Verification is a separate queue and is the long pole — start it now.** Documents
must match the LLC name **character-for-character** against the Louisiana filing
("WideLens LLC" vs "Widelens, L.L.C." bounces). Legal-name proof: **IRS CP 575 or 147C**
(ordering a 147C alone can take weeks), or LA Articles of Organization. Address proof:
business bank statement (Mercury accepted) or a utility bill in the business name under 12
months — **mobile phone bills are typically rejected**.

**Data deletion:** prefer the **Callback URL** (HTTPS endpoint accepting Meta's signed POST,
returning a confirmation code + status URL) over the Instructions URL — Meta re-tests it after
launch and a silent failure can restrict a live app. **The privacy policy, the deletion
method, and the requested permissions must all tell the same story** — inconsistency is a
named rejection trigger.

**One screencast per permission.** Reviewers do not explore the app. Every permission must be
backed by shipped, working functionality — requesting one for an unbuilt feature is an
explicit rejection reason.

Operational limits once approved: target account must be Professional (Business/Creator);
~50–100 posts per rolling 24h per account (check `/{ig-user-id}/content_publishing_limit`);
**media must be at a public HTTPS URL — no direct file upload**; no native scheduling, no
editing after publish, no licensed music on Reels. Flow: `POST /{ig-user-id}/media` → poll
`status_code` → `POST /{ig-user-id}/media_publish`.

Timeline: budget **2–6 weeks**, assume at least one rejection.

### TikTok — highest UX-compliance burden. No viable pre-audit launch.

**Pre-audit the client is crippled:** all posts forced to **`SELF_ONLY`**, **max 5 users per
24h**, and every posting account must be private at post time. There is no partial unlock.
**Do not plan to launch on TikTok before the audit clears.** Post-audit, the 24h creator cap
derives from the usage estimates on your own audit form — estimate generously but defensibly.

Use `video.publish` (Direct Post) for one-tap. `video.upload` only drops to drafts. Request
**only** the scopes actually used — extras are a red flag.

**UX requirements are contractual and verified visually:** call `Query Creator Info`
immediately before every post (no cache); display creator username + avatar; privacy selector
populated from `privacy_level_options` with **NO default** — user must actively pick;
Comment/Duet/Stitch checkboxes **disabled and greyed** (not hidden) when the creator disabled
them; the line "By posting, you agree to TikTok's Music Usage Confirmation." with a live link
directly above the publish button; Content Disclosure toggle **off by default**, revealing
"Your brand"/"Branded content", and the declaration text changes when Branded content is
checked. **No WideLens watermark, logo, or promo text burned into TikTok output** — that is a
contractual violation.

**Highest-leverage implementation choice:** use **`FILE_UPLOAD`, not `PULL_FROM_URL`**, and
TikTok domain verification becomes moot. With `PULL_FROM_URL` you must verify the exact host
serving the file — your S3/R2/CDN hostname, not `widelens.app`. Note this is the **opposite**
of Instagram, which forces a public URL. Plan the media pipeline for both.

Timeline: 2–4 weeks, usually multiple rounds.

### YouTube — lowest bar, but TWO separate approvals people conflate

**Gate 1 — Google OAuth verification.** `youtube.upload` is a *sensitive* (not restricted)
scope, so no CASA assessment. Unverified apps cap at 100 users and show the "app isn't
verified" interstitial. Requires: `widelens.app` verified in **Google Search Console** and
listed as an Authorized domain; homepage on that domain; privacy policy on the **same domain**,
linked from the homepage, URL matching the consent-screen config exactly; consent-screen name/
logo/support email matching the real product (branding mismatch is a top rejection trigger);
demo video; request `youtube.upload` **alone**, never the broad `youtube` scope.

**Gate 2 — YouTube API Services compliance audit.** Separate team, separate form. **Any
project created after 28 July 2020 that has not passed it has all `videos.insert` uploads
locked to private, permanently.** Passing Gate 1 does not pass Gate 2. Submit the **correct
variant** of the [Audit and Quota Extension Form](https://support.google.com/youtube/contact/yt_api_form)
— wrong variant is a named rejection reason. Answer follow-up questions fast; slow replies are
an explicit rejection contributor.

*(Unverified, worth checking: multiple 2026 sources report Google cut `videos.insert` from
~1,600 units to ~100 on 2025-12-04, raising the practical ceiling from ~6 to ~100 uploads/day.
If true, **no quota extension is needed at launch** — only the compliance audit. Confirm at
the API [Revision History](https://developers.google.com/youtube/v3/revision_history) before
planning around it; most guides still quote 1,600.)*

Timeline: 2–4 weeks.

### Submission order

1. **YouTube first** — lowest bar, fastest signal, teaches the rhythm cheaply.
2. **TikTok second** — once the UX surface is pixel-compliant.
3. **Meta last** — after Business Verification clears.

**Interim shipping story to decide now:** launch YouTube-only one-tap while the others are in
review, or ship share-sheet/manual export for IG and TikTok. Assume one rejection per
platform; realistic worst case is Meta at 6–8 weeks. Do not set a launch date that assumes
first-pass approval anywhere.

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
| 2026-08-08 | Parallel agent sweep. **Found the app org: `widelensapp`** (widelens + marketing), referenced in opsdirect CI routing — prior "never pushed" conclusion was wrong. Corrected PostHog to org QuietSignal / project 421406. Found the marketing page collects zero emails (no forms, no JS) and three conflicting founder-cohort numbers. Staged + tested a WideLens gatekeeper. |
| 2026-08-08 | App-review research: widelens.app has zero search footprint (corroborates Vercel live:false) — a first-order rejection risk on all three platforms. Full Meta/TikTok/YouTube playbook recorded in §8. Found an App Store name collision (Dubai agency "WIDELENS"). |
| 2026-08-08 | Supabase deep inventory: found forgeable stripe-webhook stub mode, empty voice_consents while voice cloning is live, zero admins, and that publishing/analytics have NEVER run (the learning loop cannot start). Founder program is one switch from open; DB caps (350) confirm the deck's 500 is wrong. |
| 2026-08-08 | Drive + PostHog sweep: found Supabase refresh/access tokens leaking into PostHog $current_url (live credential exposure), zero product telemetry (the moat emits nothing), 11 investor-material integrity gaps incl. a Louisiana LLC raising on a SAFE, and confirmed the live site does serve /terms and /privacy. |
