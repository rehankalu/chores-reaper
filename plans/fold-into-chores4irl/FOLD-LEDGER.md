# Fold ledger — chores-reaper features → chores4irl (Cloudflare)

> **Temporary reference ledger.** Kept in `chores-reaper` only, as the record of how the Pi-kiosk
> app's post-divergence features map onto the Cloudflare app. Its canonical successor is the
> chores4irl GitHub Wiki META-PLAN and one GitHub issue per feature in `4IRL/chores4irl`.
> Planning documents are not committed into the 4IRL org repos.
>
> **Created:** 2026-10-16 · **SOURCE:** `chores-reaper` @ `479b729` · **TARGET:** `chores4irl` @ `fddd58e`
>
> **ID namespaces.** `F#` below are **chores4irl** IDs, new and numbered in recommended
> implementation order. `CR-F#` / `CR-#nn` are **chores-reaper** F-IDs and PR numbers, cited as
> provenance only. The two schemes are unrelated, so never mix them.
>
> **Architecture rule.** TARGET's architecture is kept: Workers + Hono, D1, Pages, Access,
> multi-household scoping, offline outbox, and document (window) scroll on phones. Every port is
> adapted to it, never copied.

## Divergence

- TARGET's first commit `37cb116` (2026-07-03) was seeded from SOURCE `42040cc` (CR-#21, SSE
  sync, 2026-06-30). Normalised diffs of the shared frontend files are smallest at that commit.
  CR-#21's SSE frontend was deliberately dropped at the seed: Worker isolates have no shared
  in-process emitter.
- SOURCE app-code merges since the seed are:
  - CR-#23 swipe rework
  - CR-#24 room datalist
  - CR-#25 search filter
  - CR-F1 #27 screen-blank
  - CR-F2 #28 touch lock
  - CR-#32 bar colour
  - CR-F14 #34 clear-✕
  - CR-F4 #38 field removal
  - CR-F5 #39 frosted deck
  - CR-F6 #43 c4i hostname
  - CR-F21 #46 form/date/toast
  - CR-F16 #50 sort
  - CR-F17 #52 strip
  - CR-F18 #54 scroll-to-top
  - CR-F19 #56 lock reset
  - CR-F22 #57 overlay scrollbar
  - CR-F20 #60 permissive lock
- Unbuilt SOURCE backlog items considered here: CR-F11 undo, CR-F12 redo, CR-F15 kiosk-shell, and
  CR-F23 Pi 5 hosting.

## Feature list

Effort: S=1 · M=2 · L=3 · XL=5. Order = recommended implementation order = F-ID. ★ = focus.

| F | Feature | Source | Track | Effort | Depends on | Gate |
|---|---|---|---|---|---|---|
| ★F1 | Form date math in household timezone + Last Completed defaults to today | CR-F21 (part) | A · Correctness | S | — | — |
| F2 | Bar colour: restore 50 % fill translucency; red only when overdue | CR-#32 | A · Correctness | S | — | — |
| F3 | Apply D1 migrations in CI before Worker deploy | (gap vs CR boot migration) | A · Ops | S | — | — |
| F4 | Live refresh: re-pull on visible/focus + slow poll while visible | CR-#21 (stage 1) | D · Sync | S | — | — |
| F5 | Chore-name search filter | CR-#25 | C · List UX | S | — | — |
| F6 | Clear-✕ on free-text inputs | CR-F14 | C · List UX | S | F5 (soft) | — |
| F7 | Feedback toast + surface non-409 mutation errors | CR-F21 (part) | A · Correctness | M | — | — |
| F8 | Remove Details and Long-term task fields (D1 migration) | CR-F4 | B · Status & sort | M | F3 | — |
| F9 | Status-bucketed quota sort + Urgency weighting (`classifyStatus`) | CR-F16 (algorithm) | B · Status & sort | M | F2, F8 | — |
| F10 | Status-count strip (done today / due soon / overdue) | CR-F17 | B · Status & sort | S | F9; F5 (soft) | — |
| F11 | Stable list order: re-sort only on load / midnight / household switch | CR-F16 (behaviour) | B · Status & sort | M | F9 | owner decision |
| F12 | Undo last complete / edit / delete | CR-F11 (unbuilt) | D · Sync & recovery | M | F7 | — |
| F13 | Frosted sticky bottom Add-Chore deck | CR-F5 | C · List UX | M | — | — |
| F14 | Floating scroll-to-top button (window scroll) | CR-F18 | C · List UX | S | F13 (offset) | — |
| F15 | `scrollbar-none` utility + hide native bars on modal cards | CR-F22 (subset) | C · List UX | S | — | — |
| F16 | Swipe threshold as % of bar width, clamped | CR-#23 (part) | C · List UX | S | — | optional |
| F17 | Off-Cloudflare D1 backup export (scheduled) | (gap vs CR backups) | A · Ops | S | — | — |
| F18 | Realtime household doorbell (Durable Object + hibernatable WebSocket) | CR-#21 (stage 2) | D · Sync | L | F4 | — |
| F19 | Redo | CR-F12 (unbuilt) | D · Sync & recovery | S | F12 | optional |
| F20 | Kiosk embeddability: `frame-ancestors` + Access iframe login | CR-F15 (adapted) | E · Kiosk | M | F18 (soft) | owner decision: wall Pi frames TARGET vs CR-F23 |
| F21 | Kiosk-mode lock guard + lock-time view reset (app side of `kiosk/v1`) | CR-F2 / F20 / F19 | E · Kiosk | M | F20 | pi-kiosk Phase 4 |

**Tally:**

| Tracks | Points |
|---|---|
| A + B + C + D, ungated | 23 |
| F11, F16 and F19, needing a decision or optional | 4 |
| Kiosk track E, gated | 4 |

**Focus path:** F1 → F2 → F3 → F8 → F9 → F10. The two shipped bugs come first, then the
migration safety net, then the field removal and the sort it unblocks. F4–F7 are independent quick
wins and can be interleaved.

### Not ported (decided)

| Source | Why |
|---|---|
| CR-F1 auto screen-blank (#27) | Screen property, owned by pi-kiosk Phase 2. Meaningless on phones. |
| CR-F6 `c4i.local` hostname (#43) | Pi host config. TARGET is on `chores.4irl.app`. |
| CR-F23 Pi 5 hosting | SOURCE hosting. It is the *alternative* to F20 (see F20's gate). |
| CR-#24 room datalist (+ mobile follow-up) | TARGET rooms are managed D1 entities chosen by native `<select>`. That control is already correct on phones, so the mobile bug can't occur. |
| CR-F22 overlay thumb + full-bleed | Phones already draw fading overlay scrollbars. Only the CSS subset (F15) is worth taking. |
| CR-#23 direction swap + progressive reveal | TARGET's per-user `android` swipe style already does this. Only the threshold tweak is left (F16). |

### Tracks

- **A · Correctness & ops:** bugs TARGET ships today and deploy safety. They don't depend on
  anything else.
- **B · Status & sort:** CR-#32 → CR-F4 → CR-F16 → CR-F17 lineage.
- **C · List UX:** search, clear-✕, deck, scroll chrome, swipe.
- **D · Sync & recovery:** cross-device freshness and undo.
- **E · Kiosk:** only if the wall Pi displays the Cloudflare app. Gated.

---

## Feature contracts

Each section below is the source text for that feature's GitHub issue: what it does, what it
changes, and how to know it's done.

### F1 — Form date math in household timezone + Last Completed defaults to today · S · ★
**Function.** A chore added or edited with "Last Completed = today" shows *0 days ago* in every
household timezone. Edits round-trip without moving the date. In add mode, Last Completed
defaults to today (the household's today) and is re-applied after submit.
**Today (bug).** `frontend/src/components/form/ChoreForm.tsx` formats with
`toISOString().slice(0,10)` (~l.34) and parses with `new Date('yyyy-mm-dd')` (~l.74). That gives UTC
midnight, so every household west of UTC sees "1 day ago". The add default is `''`.
**Port.** Add a `formDate` helper built on date-fns-tz:
- parse: `fromZonedTime(\`${v}T00:00\`, tz)`
- format: `formatInTimeZone(d, tz, 'yyyy-MM-dd')`
- today: `formatInTimeZone(new Date(), tz, 'yyyy-MM-dd')`

Use the **household** timezone, not the device's (CR-F21 used device-local, which is wrong here).
Thread `householdTimezone` into `ChoreFormModal`/`ChoreForm`.
**Done when:**
- Unit tests pass for UTC−8, UTC and UTC+10 households when add-today gives 0 days.
- An edit round-trip leaves the date unchanged.
- The add-mode default is today.

### F2 — Bar colour: translucency + red only when overdue · S
**Function.** Bar fills render at 50 % opacity again. Red means only overdue
(`daysSince > frequency`). A chore *at* its due date is orange. Green is used while
`remainingRatio > 0.375`.
**Today (bug).** `utils/choreBarMath.ts` appends the Tailwind v3 `bg-opacity-50`, which does
nothing on Tailwind v4, so bars are opaque. `assets/constants.ts` has a 4-step ramp that turns
red at ratio 0. `text-opacity-80` in `ChoreTimerBar.tsx` is also dead.
**Port.** Return bare colours and put `opacity-50` on `ProgressBar`. Switch to the 2-threshold
scheme. Decide whether to drop the yellow tier (SOURCE dropped it).
**Done when:**
- Bars are visibly translucent.
- A chore on its due date is orange and turns red the next day.
- Tests are updated.

### F3 — Apply D1 migrations in CI before Worker deploy · S
**Function.** A deploy can never put Worker code live ahead of the schema it needs.
**Today.** `deploy-backend` in `.github/workflows/ci.yml` runs `wrangler deploy` only.
`make migrate-remote` is manual.
**Port.** Add `wrangler d1 migrations apply chores4irl --remote` before deploy. Document Time
Travel bookmark rollback in `ARCHITECTURE.md`. Destructive migrations (F8) still need
code-first ordering, so document the two-phase pattern.
**Done when:** CI runs the migration step on `main`, and a no-op run passes.

### F4 — Live refresh (stage 1 of multi-device sync) · S
**Function.** A phone or second screen shows other members' changes without a reload. The
app re-pulls chores when it becomes visible or focused, and polls every 30–60 s while visible.
A re-pull never yanks state while a form or dialog is open: it is held and flushed on close.
**Today.** `ChoresView.tsx` fetches only on mount and on `online`.
**Port.**
- Add a `useChoreRefresh` hook reusing `load()`'s generation guard.
- Port SOURCE's hold-while-mutating idea, but use a ref-counted gate: CR push-review finding,
  `PUSH-REVIEW-FINDINGS.md` "Multi-device sync".
- Skip the poll while the outbox is flushing.
**Done when:** a change on device A appears on device B within one poll interval or on refocus.
An open edit form is not disturbed.

### F5 — Chore-name search filter · S
**Function.** A persistent search box above the list filters chores by case-insensitive,
trimmed substring of the name. It is ANDed with the room tab. It is view-only, so data and sort
order never change. The query survives room switches and re-pulls. "No chores match" is
distinct from "No chores yet".
**Port.**
- `components/chore/ChoreSearchInput.tsx`, styled `text-base sm:text-sm` to avoid iOS zoom,
  with `type="search"` and `enterKeyHint="search"`.
- Lift the query into `Layout` (`App.tsx` `LayoutContext`) so it survives `/admin` navigation.
- Add a `useMemo` filter between `useRoomFilter` and the sort.

**Done when:** filter + room AND logic is tested, the query persists across tab and route
changes, and the empty-match state is shown.

### F6 — Clear-✕ on free-text inputs · S
**Function.** Non-empty free-text fields show a ✕ (44 px hit target). Tapping it clears only that
field, never submits or closes, and refocuses the input.
**Port.**
- Shared `components/common/ClearButton.tsx` (lucide `X`) and an opt-in `clearable` on `FormField`.
- Enable it on: chore Name, Details (until F8), the F5 search box, and optionally the admin
  search inputs. Room is a `<select>`, so it gets no ✕.
- Hide the native `::-webkit-search-cancel-button` so there aren't two ✕s.

**Done when:** the ✕ appears only when the field is non-empty, clears and refocuses, and tests
are updated.

### F7 — Feedback toast + surface mutation errors · M
**Function.** One bottom-centre toast at a time (keyed remount):
- **Success:** green, auto-dismisses, fires only after the server confirms.
- **Error:** red, stays until dismissed. A later successful mutation clears a standing error.
- **Non-409 failures** (400 invalid room, 500) roll back the optimistic change and show an
  error. Today they are swallowed silently.
- **Offline-queued writes:** show nothing beyond the existing offline banner (or a neutral
  "saved offline"; decide at planning).
- **Outbox flush failures:** surface as errors.

**Today.** `mutate()` in `ChoresView.tsx` (~l.79–100) gives no success feedback and keeps
optimistic state on errors. `StatusBanner` covers only stale/offline/switch states.
**Port.** `components/common/Toast.tsx` plus `onSuccess`/`onError` plumbing in `mutate()` and
`handleFlushResults`. The z-ladder must fit TARGET's modals (`z-50`). Bottom offset is
`env(safe-area-inset-bottom)`-aware.
**Done when:** each outcome (success, 4xx, 5xx, offline-queued, flush failure) is tested.

### F8 — Remove Details and Long-term task fields · M · needs F3
**Function.** The add/edit form drops *Details* and *Long-term task*. Long-term chores stop being
pinned to the bottom. Old clients and queued outbox entries that still send these keys are
accepted, and the keys are ignored, never rejected.
**Port.**
- **Two-phase deploy:**
  1. Ship code that no longer reads or writes the columns and ignores the keys.
  2. Then add the migration `ALTER TABLE chores DROP COLUMN details` and
     `ALTER TABLE chores DROP COLUMN long_term_task`.
- **Check first:** no index or view references either column.
- **Touches:** `backend/src/chores.ts`, `seed-mock-chores.ts`, `types/SharedTypes.d.ts`,
  `ChoreForm.tsx`, `choreSort.ts` (long-term partition), `outbox.ts` payload type (tolerant),
  `mockApi.ts`, `ARCHITECTURE.md`, backend and e2e tests.

**Done when:**
- The migration has been applied by CI.
- A replayed legacy outbox payload with both keys succeeds.
- Neither field appears in the UI.

### F9 — Status-bucketed quota sort + Urgency weighting · M · needs F2, F8
**Function.** The list is ranked by status bucket.
- **Classifier:** one shared `classifyStatus(daysSince, frequency)` classifies each chore as
  red (overdue), orange (due soon, ratio ≤ 0.375) or green. Bar colour uses the same function.
- **Ranking within buckets:**
  - red: by urgency-weighted overdue ratio (low 0.75 / medium-or-unset 1 / high 1.5), with
    duration as the tie-break
  - orange: by remaining ratio, ascending
  - green: by days since, ascending
- **Top 8 slots:** filled by quota red 4 / orange 2 / green 2. Each red at weighted ratio ≥ 1
  takes an extra slot, from orange first. Unused slots cascade.
- **Urgency:** affects sort only, never colour.

**Today.** `utils/choreSort.ts` uses `duration × daysSince/frequency` plus the long-term partition,
in the household timezone. The `urgency` field already exists end to end (D1, API, form).
**Port.**
- Port the algorithm, keeping `daysSinceInZone`, and add the `SORT_*` constants.
- Port SOURCE's `choreSort.test.ts`, including the quota-sum invariant.
- No backend change.
- CR-F16's open item: tune the constants on real data.

**Done when:** bucket, quota and escalation tests pass, and bar colour and sort agree.

### F10 — Status-count strip · S · needs F9
**Function.** A thin strip under the room tabs shows three proportional, animated segments: done
today (green), due soon (orange) and overdue (red). Counts are taken over the *visible* list (room,
plus search once F5 ships) and are live on every change. It has one `role="img"` label, e.g.
"N done today · N due soon · N overdue". All-zero shows a full green "0".
**Port.**
- `utils/choreStatusCounts.ts`, a pure function of `(chores, today, tz)` using `classifyStatus`.
- `components/nav/StatusCountStrip.tsx`, mounted at the top of `ChoresView`.
- Consider sticky positioning on phones.
- Keep SOURCE's `data-testid`s.

**Done when:** count tests pass, including the visible-list scoping.

### F11 — Stable list order between midnights · M · owner decision
**Function.** The list re-sorts only on first load, at household-local midnight
(`useMidnightClock`), on household switch and on manual refresh. Completing or editing a chore does
not move it under the user's finger. New chores are appended until the next re-sort.
**Decision needed.** On the kiosk, stable positions mattered. On a phone that's opened briefly,
live re-sort may be preferable. Possible middle ground: re-sort on visibility return (F4).
**Port.** A `sortedIds` snapshot in `ChoresView`. It must absorb outbox temp-id → real-id swaps
in `handleFlushResults`.
**Done when:** completing a chore leaves its position unchanged, and the order updates after a
midnight tick.

### F12 — Undo last complete / edit / delete · M (L with soft-delete)
**Function.** After complete, edit or delete, the toast (F7) offers **Undo** for ~8 s.
- **Undo complete or edit:** restores the prior values.
- **Undo delete:** restores the chore.
- **Offline:** cancels the still-queued outbox entry instead of queueing a compensating write.
- **Conflict:** a 409 shows "can't undo — someone changed it".

This replaces the kiosk touch lock as the phone-side answer to accidental touches.
**Constraints.**
- `/complete` only moves the date forward (`backend/src/chores.ts` ~l.177), so undo uses
  versioned `PUT` with the snapshot and the version returned by complete.
- Delete is a hard `DELETE` (~l.200). Choose between re-creating via `client_id` (new id) and
  soft-delete (new `deleted_at` migration, every query filtered).

**Done when:** each undo path is tested online, offline-queued, and on conflict.

### F13 — Frosted sticky bottom Add-Chore deck · M
**Function.**
- **Placement:** the primary "+ Add Chore" action moves to a sticky bottom deck in the thumb
  zone.
- **Frost:** bars scroll visibly under a frosted backing (`backdrop-blur`, gradient mask,
  `pointer-events-none`), while the button stays opaque.
- **Focus:** focus never parks a bar under the deck.

**Port.**
- **Layout:** use `position: sticky; bottom: 0` inside document scroll. **Do not** copy
  SOURCE's `100dvh`/`overflow:hidden` single-scroller shell, which is fragile on iOS.
- **Spacing:** `env(safe-area-inset-bottom)` padding (check `viewport-fit=cover`) and
  `scroll-padding-bottom` on `html`.
- **Refs:** move `addChoreRef` (the offline-banner focus hand-off target).
- **Settings gear:** rehome it, either staying top-right or moving into `NavBar`.
- **Tests:** update `ChoresView.test.tsx`.

**Done when:** the deck is visible over a scrolled list on iOS Safari, iOS standalone and
Android Chrome, and no bar is hidden behind it at the list end.

### F14 — Floating scroll-to-top button · S · offset depends on F13
**Function.** After the user scrolls past 80 px, a round ↑ button appears at the bottom centre.
- **Tap:** smooth-scrolls to the top (instant under reduced motion).
- **Fade:** ~500 ms. It is non-interactive until fully shown and after fade-out, so a mid-fade
  tap can't complete the bar beneath it.

This matters mainly on Android; iOS has status-bar tap.
**Port.**
- `useScrollPastThreshold` reading `window.scrollY`.
- A `fixed` button above the F13 deck (or `bottom-6` plus the safe-area inset without it).
- Below modal `z-50`.

**Done when:** threshold, visibility and fade-gating tests pass.

### F15 — `scrollbar-none` utility + modal scrollbar hiding · S
**Function.** Restores the horizontal room-tab strip's intended hidden scrollbar.
`NavBar.tsx` uses `scrollbar-none`, but TARGET's `index.css` never defines it on Tailwind v4.
Optionally hides the native bar on modal cards on desktop.
**Done when:** the utility is defined, and the room strip shows no scrollbar on desktop
Chrome/Firefox.

### F16 — Swipe threshold as a fraction of bar width · S · optional
**Function.** The swipe action fires past `min(25 % of bar width, 120 px)` instead of a fixed
120 px, so it's easier on narrow phones and unchanged on tablets.
**Port.**
- `ChoreTimerBar.tsx`: measure width at swipe start, then use it in the progress math and
  `onSwipedLeft`/`onSwipedRight`.
- Keep `delta: 10` and `preventScrollOnSwipe`; these are iOS fixes.
- Keep the per-user `swipeStyle` contract and delete-confirm.
- Stub the width in tests.

**Done when:** android and ios modes are tested at 320 px and 1024 px widths.

### F17 — Off-Cloudflare D1 backup export · S
**Function.** A scheduled (weekly) `wrangler d1 export` writes a copy to storage outside D1 (R2 or
a private artifact store) and retains N copies. This matches SOURCE's "backup must leave the
box" rule. D1 Time Travel remains the primary rollback.
**Done when:** the schedule runs, an export is restorable to a local D1, and retention is
enforced.

### F18 — Realtime household doorbell · L · needs F4
**Function.** Changes appear on other open devices within a second.
**Design.**
- **Durable Object:** one per household, keyed by `householdId`, using hibernatable WebSockets
  (no cost while idle).
- **Endpoint:** `GET /api/households/:id/events` passes through accessAuth → resolveUser →
  householdScope, then upgrades into the DO.
- **Ringing the bell:** successful chore writes call `waitUntil(stub.fetch('/notify'))`. Outbox
  replays and undo ring it too.
- **Payload:** a content-free doorbell. Clients re-pull via F4's path.
- **Fallback:** F4's visibility re-pull stays, because phones kill sockets in the background.
- **Config:** `[[durable_objects]]` binding plus a migration tag in `wrangler.toml`. Check
  free-tier limits and that the service worker doesn't intercept the upgrade.
- **Carry over** CR push-review items: connection cap, and no redundant re-fetch on open.

**Done when:** an e2e test with two pages shows A's write reflected on B without a poll, and
cross-household isolation is tested.

### F19 — Redo · S · optional, needs F12
**Function.** After an undo, the toast offers **Redo**, which re-applies the undone action. It
uses the same one-level snapshot and the same conflict handling.

### F20 — Kiosk embeddability · M · gated on owner decision
**Decision.** Which app should the wall Pi's pi-kiosk shell frame?
- **TARGET:** this feature.
- **SOURCE on the Pi 5 (CR-F23):** this feature is then not needed.

Framing TARGET gives up SOURCE's zero-internet-dependency property (`TRADEOFFS.md`).
**Function.** The pi-kiosk shell can iframe `chores.4irl.app`.
**Port.**
- **Headers:** add `frontend/public/_headers` with
  `Content-Security-Policy: frame-ancestors 'self' <shell origin>`. Nothing blocks framing
  today; the header makes it explicit.
- **Access cookie:** set Access `SameSite=None` and a long session duration in Zero Trust.
- **Login:** Access/IdP login pages refuse framing, so plan a top-level login flow in the kiosk
  Chromium profile. Third-party-cookie settings matter.
- **Docs:** document it in `ARCHITECTURE.md`.

**Done when:** the shell frames the app on the wall Pi through a session expiry and re-login.

### F21 — Kiosk-mode lock guard + view reset · M · gated on F20 + pi-kiosk Phase 4
**Function.** This is active only when the app is embedded and the pinned shell origin sends
`kiosk/v1` `kiosk-state { locked }`. It does nothing on phones.
- **While locked:** complete, edit and delete are guarded. A blocked tap shows a non-blocking
  padlock, and a second tap within 1.5 s / 60 px requests unlock (`lock-request`).
- **Lock reset:** on lock-engage and on each idle tick, the view resets to: top, room All,
  search cleared, Add form closed.
- **Ownership:** per CR-F15's contract, the kiosk owns the timer and the app owns what "locked"
  means.

**Source contracts.** `chores-reaper/plans/META-PLAN.md` § F2 (as reworked by CR-F20) and § F19.
`plans/feature/kiosk-shell-extraction/kiosk-shell-extraction.md`.
