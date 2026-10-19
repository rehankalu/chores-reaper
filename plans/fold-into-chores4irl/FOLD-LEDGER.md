# Fold ledger — chores-reaper features → chores4irl (Cloudflare)

> **Temporary reference ledger.** Kept in `chores-reaper` only, as the record of how the Pi-kiosk
> app's post-divergence features map onto the Cloudflare app. The canonical copies are:
> - **The chores4irl GitHub Wiki `META-PLAN` page** for the table, order, dependencies and decisions.
> - **One GitHub issue per item in `4IRL/chores4irl`** for the functional spec.
>
> Planning documents are not committed into the 4IRL org repos.
>
> **Created:** 2026-10-16 · **Revised:** 2026-10-16 (owner decisions; bugs split out; Track E handed to pi-kiosk)
> **SOURCE:** `chores-reaper` @ `479b729` · **TARGET:** `chores4irl` @ `fddd58e`
>
> **ID namespaces.**
> - **`B#` and `F#`** are **chores4irl** IDs for bugs and features, numbered in implementation order.
>   Bugs come first and are addressed in turn.
> - **`CR-F#` and `CR-#nn`** are **chores-reaper** F-IDs and PR numbers. They are cited only as
>   provenance; the two schemes are unrelated.
>
> **Architecture rule.** TARGET's architecture is kept: Workers + Hono, D1, Pages, Access,
> multi-household scoping, offline outbox, and document (window) scroll on phones. Every port is
> adapted to it, never copied.

## Divergence

TARGET's first commit `37cb116` (2026-07-03) was seeded from SOURCE `42040cc` (CR-#21, SSE sync,
2026-06-30). Normalised diffs of the shared frontend files are smallest at that commit. CR-#21's
SSE frontend was deliberately dropped at the seed, because Worker isolates have no shared
in-process emitter.

SOURCE app-code merges since the seed:

| Ref | Change |
|---|---|
| CR-#23 | Swipe rework |
| CR-#24 | Room datalist |
| CR-#25 | Search filter |
| CR-F1 #27 | Screen-blank |
| CR-F2 #28 | Touch lock |
| CR-#32 | Bar colour |
| CR-F14 #34 | Clear-✕ |
| CR-F4 #38 | Field removal |
| CR-F5 #39 | Frosted deck |
| CR-F6 #43 | c4i hostname |
| CR-F21 #46 | Form/date/toast |
| CR-F16 #50 | Sort |
| CR-F17 #52 | Strip |
| CR-F18 #54 | Scroll-to-top |
| CR-F19 #56 | Lock reset |
| CR-F22 #57 | Overlay scrollbar |
| CR-F20 #60 | Permissive lock |

Unbuilt SOURCE backlog considered: CR-F11 undo, CR-F12 redo, CR-F15 kiosk-shell, CR-F23 Pi 5
hosting.

## Owner decisions (2026-10-16)

- **F10 stable order:** retained. On phones the list stays put until midnight, as on the kiosk.
- **F16 swipe threshold:** folded in for the `Settings › Chore swipe style › Android` mode only.
  iOS mode is unchanged.
- **F11 undo and F12 redo:** both are folded in as features, no longer optional.
- **Realtime push dropped (former F18, closed as not planned as 4IRL/chores4irl#92):** a per-household
  Durable Object WebSocket doorbell is more machinery than needed. F3's re-pull on
  focus/visibility plus a slow poll is sufficient.
- **Four bugs found in TARGET get their own issues (B1–B4)**, addressed in turn before features.
- **Wall display:** the wall Pi shows the **Cloudflare app**. All kiosk work belongs to
  `rehankalu/pi-kiosk`, not to this fold, wiki or issues. It covers kiosk-shell, screen-blank,
  the touch lock with its view reset, and embedding/display. See *Handed to pi-kiosk* below.
- **CR-F23 Pi 5 hosting:** probably made obsolete by this reconciliation, since the wall Pi shows
  the Cloudflare app. It is left out.
- **Issue bodies hold the spec.** The wiki holds the table, order, dependencies and decisions.

## Item list

Effort: S=1 · M=2 · L=3. The ID order is the implementation order. ★ marks the focus.

| ID | Item | Source | Track | Effort | Depends on |
|---|---|---|---|---|---|
| ★B1 | Add/edit form dates are off by one day for households west of UTC | CR-F21 (date fix) | A · Bugs | S | — |
| B2 | Chore bar fills are opaque (dead Tailwind v3 `bg-opacity-50`) | CR-#32 | A · Bugs | S | — |
| B3 | Chore bars turn red on the due date instead of after it | CR-#32 | A · Bugs | S | — |
| B4 | Failed chore edits (non-409) are swallowed silently | (TARGET-only) | A · Bugs | S | — |
| F1 | Last Completed defaults to today in add mode | CR-F21 (defaults) | B · Form & feedback | S | B1 |
| F2 | Apply D1 migrations in CI before Worker deploy | (gap vs CR boot migration) | E · Ops | S | — |
| F3 | Live refresh: re-pull on visible/focus + slow poll while visible | CR-#21 (stage 1) | D · Sync & recovery | S | — |
| F4 | Chore-name search filter | CR-#25 | C · List UX | S | — |
| F5 | Clear-✕ on free-text inputs | CR-F14 | C · List UX | S | F4 (soft) |
| F6 | Feedback toast (success / error, one at a time) | CR-F21 (toast) | B · Form & feedback | M | B4 |
| F7 | Remove Details and Long-term task fields (D1 migration) | CR-F4 | S · Status & sort | M | F2 |
| F8 | Status-bucketed quota sort + Urgency weighting | CR-F16 (algorithm) | S · Status & sort | M | B2, B3, F7 |
| F9 | Status-count strip (done today / due soon / overdue) | CR-F17 | S · Status & sort | S | F8; F4 (soft) |
| F10 | Stable list order: re-sort only on load / midnight / household switch | CR-F16 (behaviour) | S · Status & sort | M | F8 |
| F11 | Undo last complete / edit / delete | CR-F11 (unbuilt) | D · Sync & recovery | M | F6 |
| F12 | Redo | CR-F12 (unbuilt) | D · Sync & recovery | S | F11 |
| F13 | Frosted sticky bottom Add-Chore deck | CR-F5 | C · List UX | M | — |
| F14 | Floating scroll-to-top button (window scroll) | CR-F18 | C · List UX | S | F13 (offset) |
| F15 | `scrollbar-none` utility + hide native bars on modal cards | CR-F22 (subset) | C · List UX | S | — |
| F16 | Android swipe style: threshold as a fraction of bar width | CR-#23 (part) | C · List UX | S | — |
| F17 | Off-Cloudflare D1 backup export (scheduled) | (gap vs CR backups) | E · Ops | S | — |

**Tally:** bugs 4 · features 23 · total **27 points**.

**Focus path:** B1 → B2 → B3 → B4, then F2 → F7 → F8 → F9 → F10. The bugs come first, then the
migration safety net, then the field removal and the sort it unblocks. F1 and F3–F6 are
independent quick wins and can be interleaved after the bugs.

### Tracks
- **A · Bugs:** defects TARGET ships today. Fixed in turn, before features.
- **B · Form & feedback:** add-mode default and the toast.
- **S · Status & sort:** CR-#32 → CR-F4 → CR-F16 → CR-F17 lineage.
- **C · List UX:** search, clear-✕, deck, scroll chrome, swipe.
- **D · Sync & recovery:** cross-device freshness (F3), undo and redo.
- **E · Ops:** deploy safety and backups.

### Not ported (decided)

| Source | Why |
|---|---|
| CR-#24 room datalist (+ its mobile follow-up) | TARGET rooms are managed D1 entities chosen by native `<select>`, which is already correct on phones. |
| CR-F22 overlay thumb + full-bleed | Phones already draw fading overlay scrollbars. Only the CSS subset (F15) is worth taking. |
| CR-#23 direction swap + progressive reveal | TARGET's per-user `android` swipe style already does this. Only the threshold (F16) remains. |
| CR-#21 SSE realtime push (stage 2: Durable Object doorbell, former F18) | Dropped 2026-10-16. F3's focus/visibility re-pull plus polling is sufficient. |
| CR-F6 `c4i.local` hostname | Pi host config. TARGET is on `chores.4irl.app`. |
| CR-F23 Pi 5 hosting | Probably obsoleted: the wall Pi shows the Cloudflare app. |

### Handed to pi-kiosk (not in the chores4irl wiki or issues)

The wall Pi shows `chores.4irl.app`. `rehankalu/pi-kiosk` owns everything below; log it there from a
pi-kiosk session.

**Covered sources:**
- **CR-F1** screen-blank
- **CR-F2/F20** touch lock
- **CR-F19** lock-time view reset
- **CR-F15** kiosk-shell adoption / embeddability

**Display method — owner direction.** Embedding in an iframe needs Cloudflare Access to work in a
cross-site frame: `SameSite=None`, plus the Access/IdP login pages refuse to be framed. The owner
asked whether a simpler method exists: Chromium full-screen directly on `chores.4irl.app`, with
users unable to navigate away, minimise or close it. Options to evaluate in pi-kiosk:
- **Chromium `--kiosk` on the URL directly.**
  - Login is top-level, so there is no iframe or `SameSite` problem.
  - Use a persistent profile so the Access cookie survives reboots, and a long Access session.
  - A managed policy locks navigation:
    - `URLBlocklist: ["*"]`
    - `URLAllowlist`: `chores.4irl.app`, the team's `*.cloudflareaccess.com`, and the IdP's login
      host
    - `DeveloperToolsAvailability: 2`
    - `IncognitoModeAvailability: 1`
    - disable the `chrome://` pages
  - A systemd user unit restarts Chromium on exit. With no keyboard attached, touch alone can't
    leave kiosk mode.
  - **Cost:** shell-drawn overlays can no longer sit above the app.
    - Screen-blank moves to the agent (DPMS / `wlr-randr` / backlight).
    - The lock either needs the app's cooperation (a URL flag or a postMessage-free signal) or
      becomes an agent-level input grab.
- **Keep the iframe shell.** It keeps the overlay model but must solve the Access-in-iframe
  login.
- **A hybrid.** `--kiosk` on the app URL for login and normal use. The shell is used only for
  console/overlay screens.

---

## Item specs

Each section below is the source text for that item's GitHub issue: what it does, what changes,
and how to know it's done. The issue body is canonical once created.

### B1 — Add/edit form dates are off by one day for households west of UTC · S · ★
**Bug.** A chore added or edited with Last Completed = today shows *1 day ago* in every household
west of UTC (all of the Americas). Editing an evening completion can also shift its date back a
day.

**Cause.** `frontend/src/components/form/ChoreForm.tsx` has two UTC conversions:
- It formats with `toISOString().slice(0,10)` (~l.34).
- It parses with `new Date('yyyy-mm-dd')` (~l.74), which yields UTC midnight.

`ChoreTimerBar.tsx` and `choreSort.ts` floor days in the household timezone, so the UTC midnight
lands on the previous local day.

**Fix.** Add a `formDate` helper on date-fns-tz that works in the **household** timezone (not the
device's):
- Parse with ``fromZonedTime(`${v}T00:00`, tz)``.
- Format with `formatInTimeZone(d, tz, 'yyyy-MM-dd')`.

Thread `householdTimezone` into `ChoreFormModal`/`ChoreForm`.

**Done when:**
- Unit tests for UTC−8, UTC and UTC+10 households show today → 0 days.
- An edit round-trip leaves the date unchanged.

### B2 — Chore bar fills are opaque · S
**Bug.** Bar fills should be 50 % translucent but render fully opaque.

**Cause.** `utils/choreBarMath.ts` appends the Tailwind v3 `bg-opacity-50`. That class doesn't
exist in Tailwind v4 (TARGET is on `^4.3.2`). `text-opacity-80` in `ChoreTimerBar.tsx` is
likewise dead.

**Fix.**
- Return bare colours from `choreBarMath.ts`.
- Add `opacity-50` (or `/50` colour syntax) on `ProgressBar.tsx`.
- Replace `text-opacity-80`.

**Done when:** bars are visibly translucent and tests assert the class.

### B3 — Chore bars turn red on the due date instead of after it · S
**Bug.** A chore exactly at its due date (`daysSince === frequency`, remaining ratio 0) already
shows red. Red should mean overdue only (`daysSince > frequency`).

**Cause.** `assets/constants.ts` uses a 4-step ramp that reaches red at ratio 0: green > 0.5,
yellow > 0.2, orange > 0, red at −∞.

**Fix.** Adopt the two-threshold scheme:
- green while remaining ratio > 0.375
- orange otherwise
- red only when overdue

Decide whether to keep a yellow tier; chores-reaper dropped it. The shared classifier comes later
in F8.

**Done when:** a chore on its due date is orange and turns red the next day, with tests.

### B4 — Failed chore edits are swallowed silently · S
**Bug.** When a create/edit/complete/delete fails with anything other than a 409 conflict (for
example 400 invalid room, or 500), the user sees nothing. The optimistic change also stays on
screen, so the user believes it saved.

**Cause.** `mutate()` in `ChoresView.tsx` (~l.79–100) handles only the 409 conflict path.
`handleFlushResults` handles only outbox successes.

**Fix.**
- Roll back the optimistic change on a non-409 failure.
- Show an error using the existing `StatusBanner` (or a minimal inline message). F6 later moves
  this into the toast.
- Report outbox flush failures the same way.

**Done when:** tests for 400, 500 and a flush failure each show the rollback and an error message.

### F1 — Last Completed defaults to today in add mode · S · needs B1
**Function.** In add mode, Last Completed defaults to the household's today and is re-applied
after each submit. Room already defaults to the active tab.

**Port.** `buildInitialFormState` in `ChoreForm.tsx` uses B1's `formatInTimeZone(new Date(), tz, …)`.

**Done when:** the add form opens with today and resets to today after submit.

### F2 — Apply D1 migrations in CI before Worker deploy · S
**Function.** A deploy can never put Worker code live ahead of the schema it needs.

**Today.** `deploy-backend` in `.github/workflows/ci.yml` runs `wrangler deploy` only.
`make migrate-remote` is a manual step.

**Port.**
- Add `wrangler d1 migrations apply chores4irl --remote` before the deploy.
- Document Time Travel bookmark rollback in `ARCHITECTURE.md`.
- Document the two-phase pattern for destructive migrations: code first, migration later (needed
  by F7).

**Done when:** CI runs the migration step on `main` and a no-op run passes.

### F3 — Live refresh (multi-device sync, stage 1) · S
**Function.** A phone or second screen shows other members' changes without a reload. The app
re-pulls chores when it:
- becomes visible or focused
- reaches each 30–60 s poll while visible

A re-pull never disturbs an open form or dialog. It is held and flushed on close.

**Today.** `ChoresView.tsx` fetches only on mount and on `online`.

**Port.**
- Add a `useChoreRefresh` hook that reuses `load()`'s generation guard.
- Gate re-pulls with a ref-counted gate. This follows the chores-reaper push-review finding on
  multi-device sync.
- Skip the poll while the outbox is flushing.

**Done when:** a change on device A appears on device B within one poll or on refocus, and an
open edit form is undisturbed.

### F4 — Chore-name search filter · S
**Function.** A persistent search box above the list filters chores by name.
- **Matching:** case-insensitive, trimmed substring, ANDed with the room tab.
- **View-only:** data and sort order never change.
- **Persistence:** the query survives room switches, re-pulls and admin navigation.
- **Empty state:** "No chores match" is distinct from "No chores yet".

**Port.**
- New `components/chore/ChoreSearchInput.tsx` with:
  - `text-base sm:text-sm`, to avoid iOS zoom
  - `type="search"`
  - `enterKeyHint="search"`
- Lift the query into `Layout` (`App.tsx` `LayoutContext`).
- Add a `useMemo` filter between `useRoomFilter` and the sort.

**Done when:** filter and room-AND tests pass, persistence is tested, and the empty-match state
renders.

### F5 — Clear-✕ on free-text inputs · S
**Function.** A non-empty free-text field shows a ✕ with a 44 px hit target. Tapping it clears
only that field (it never submits or closes) and refocuses the input.

**Port.**
- Shared `components/common/ClearButton.tsx` (lucide `X`) plus an opt-in `clearable` prop on
  `FormField`.
- Enable it on chore Name, on Details (until F7) and on the F4 search box. Admin search inputs are
  optional.
- Room is a `<select>`, so it gets no ✕.
- Hide the native `::-webkit-search-cancel-button`.

**Done when:** the ✕ shows only when non-empty and clears and refocuses, with tests.

### F6 — Feedback toast · M · needs B4
**Function.** One bottom-centre toast at a time (keyed remount).
- **Success:** green, auto-dismisses, and fires only after the server confirms.
- **Error:** red and stays until dismissed. A later success clears a standing error. B4's error
  surface moves here.
- **Offline-queued writes:** show nothing beyond the offline banner, or a neutral "saved offline".
  Decide at planning.

**Port.**
- `components/common/Toast.tsx`.
- `onSuccess`/`onError` plumbing in `mutate()` and `handleFlushResults`.
- The z-index must fit below TARGET's modals (`z-50`).
- The bottom offset accounts for `env(safe-area-inset-bottom)`.

**Done when:** tests cover success, 4xx, 5xx, offline-queued and flush failure.

### F7 — Remove Details and Long-term task fields · M · needs F2
**Function.**
- The add/edit form drops *Details* and *Long-term task*.
- Long-term chores stop being pinned to the bottom.
- Old clients and queued outbox entries that still send these keys are accepted with the keys
  ignored, never rejected.

**Port.**
- **Two-phase deploy:**
  1. Ship code that no longer reads or writes the columns and ignores the keys.
  2. Then add the migration `ALTER TABLE chores DROP COLUMN details` and
     `ALTER TABLE chores DROP COLUMN long_term_task`.
- Check that no index or view references either column.
- **Touches:**
  - backend: `backend/src/chores.ts`, `seed-mock-chores.ts`
  - shared types: `types/SharedTypes.d.ts`
  - form: `ChoreForm.tsx`
  - sort: `choreSort.ts` (the long-term partition)
  - outbox: `outbox.ts` payload type (made tolerant)
  - mock API: `mockApi.ts`
  - docs: `ARCHITECTURE.md`
  - backend and e2e tests

**Done when:**
- The migration is applied by CI.
- A replayed legacy outbox payload succeeds.
- Neither field appears in the UI.

### F8 — Status-bucketed quota sort + Urgency weighting · M · needs B2, B3, F7
**Function.**
- **Classification:** one shared `classifyStatus(daysSince, frequency)` puts each chore in a
  bucket, and bar colour uses the same function:
  - red: overdue
  - orange: due soon, remaining ratio ≤ 0.375
  - green: everything else
- **Order within each bucket:**
  - red: by urgency-weighted overdue ratio, with duration breaking ties. The weights are low 0.75,
    medium or unset 1, high 1.5.
  - orange: by remaining ratio, ascending.
  - green: by days since, ascending.
- **Top 8 slots:** filled by quota (red 4, orange 2, green 2).
  - Each red chore at a weighted ratio ≥ 1 takes an extra slot, taken from orange first.
  - Unused slots cascade.
- **Urgency:** affects the sort only, never colour.

**Today.**
- `utils/choreSort.ts` scores by `duration × daysSince/frequency`, plus the long-term partition.
- `urgency` already exists end to end: D1, API and form.

**Port.**
- Port the algorithm, keeping `daysSinceInZone`.
- Add the `SORT_*` constants.
- Port chores-reaper's `choreSort.test.ts`, including the quota-sum invariant.
- No backend change.
- Tune the constants on real data.

**Done when:** bucket, quota and escalation tests pass, and colour and sort agree.

### F9 — Status-count strip · S · needs F8
**Function.**
- **Layout:** a thin strip under the room tabs with three proportional, animated segments: done
  today (green), due soon (orange) and overdue (red).
- **Counts:** taken over the visible list (the room, plus F4's search) and live on every change.
- **Accessibility:** a single `role="img"` label, "N done today · N due soon · N overdue".
- **Empty case:** when every count is zero, it shows a full green "0".

**Port.**
- `utils/choreStatusCounts.ts`, a pure function of `(chores, today, tz)` using `classifyStatus`.
- `components/nav/StatusCountStrip.tsx`, mounted at the top of `ChoresView`.
- Consider making it sticky on phones.

**Done when:** count tests pass, including the visible-list scoping.

### F10 — Stable list order between midnights · M · needs F8
**Function.**
- **When the list re-sorts:** only on first load, at household-local midnight
  (`useMidnightClock`) and on a household switch.
- **What doesn't move it:** completing or editing a chore, or a refresh (F3). Nothing moves
  under the user's finger.
- **New chores:** appended until the next re-sort.

**Decision (owner, 2026-10-16):** on phones the list stays put until midnight, as on the kiosk.

**Port.** A `sortedIds` snapshot in `ChoresView`. It must absorb outbox temp-id → real-id swaps in
`handleFlushResults`.

**Done when:** completing a chore leaves its position unchanged, and the order updates after a
midnight tick and on household switch.

### F11 — Undo last complete / edit / delete · M · needs F6
**Function.** After a complete, edit or delete, the toast offers **Undo** for about 8 s.
- **Complete or edit:** undo restores the prior values.
- **Delete:** undo restores the chore.
- **Offline:** cancels the still-queued outbox entry.
- **Conflict:** a 409 shows "can't undo — someone changed it".

**Constraints.**
- `/complete` only moves the date forward (`backend/src/chores.ts` ~l.177). Undo therefore uses a
  versioned `PUT` with the snapshot and the version returned by complete.
- Delete is a hard `DELETE` (~l.200). Choose between:
  - re-create via `client_id`, which gives a new id
  - soft-delete: a `deleted_at` migration, with every query filtered (raises effort to L)

**Done when:** each undo path is tested online, offline-queued and on conflict.

### F12 — Redo · S · needs F11
**Function.** After an undo, the toast offers **Redo**, which re-applies the undone action. It uses
the same one-level snapshot and the same conflict handling as F11.

**Done when:** an undo → redo round-trip is tested for complete, edit and delete.

### F13 — Frosted sticky bottom Add-Chore deck · M
**Function.**
- The primary "+ Add Chore" action moves to a sticky bottom deck, in the thumb zone.
- Bars scroll visibly under a frosted backing (`backdrop-blur`, gradient mask,
  `pointer-events-none`). The button stays opaque.
- Focus never parks a bar under the deck.

**Port.**
- **Positioning:** `position: sticky; bottom: 0` inside document scroll. **Don't** copy a
  `100dvh`/`overflow:hidden` single-scroller shell, which is fragile on iOS.
- **Spacing:**
  - `env(safe-area-inset-bottom)` padding (check `viewport-fit=cover`)
  - `scroll-padding-bottom` on `html`
- **Refs and controls:**
  - Move `addChoreRef`, the offline-banner focus hand-off.
  - Rehome the Settings gear.
- **Tests:** update `ChoresView.test.tsx`.

**Done when:** the deck shows over a scrolled list on iOS Safari, iOS standalone and Android
Chrome, and no bar is hidden at the list end.

### F14 — Floating scroll-to-top button · S · offset from F13
**Function.**
- **Appearance:** once the user scrolls past 80 px, a round ↑ button appears at the bottom
  centre.
- **Tap:** smooth-scrolls to the top, or jumps instantly under reduced motion.
- **Fade:** about 500 ms. The button is non-interactive until fully shown and after fade-out, so a
  mid-fade tap can't complete the bar beneath it.

**Port.**
- `useScrollPastThreshold` reading `window.scrollY`.
- A `fixed` button placed above the F13 deck.
- Stack it below modal `z-50`.

**Done when:** tests cover the threshold, visibility and fade-gating.

### F15 — `scrollbar-none` utility + modal scrollbar hiding · S
**Function.** Restores the hidden scrollbar on the horizontal room-tab strip. `NavBar.tsx` uses
`scrollbar-none`, but `index.css` never defines it under Tailwind v4. Optionally hides the native
bar on modal cards on desktop.

**Done when:** the utility is defined, and the room strip shows no scrollbar in desktop Chrome
and Firefox.

### F16 — Android swipe style: threshold as a fraction of bar width · S
**Function.** Applies only when the user's *Settings › Chore swipe style* is **Android**. The swipe
action fires past `min(25 % of bar width, 120 px)` instead of a fixed 120 px, so it's easier on
narrow phones and unchanged on wide screens. iOS mode is untouched.

**Port.**
- `ChoreTimerBar.tsx`, Android branch only: measure the bar width at swipe start, and use it in
  the progress math and in `onSwipedLeft`/`onSwipedRight`.
- Keep `delta: 10` and `preventScrollOnSwipe`.
- Keep delete-confirm.
- Stub the width in tests.

**Done when:** Android mode is tested at 320 px and 1024 px widths, and iOS mode is unchanged.

### F17 — Off-Cloudflare D1 backup export · S
**Function.** A weekly scheduled `wrangler d1 export` writes a copy outside D1 (R2 or a private
artifact store) and keeps the newest N copies. D1 Time Travel remains the primary rollback.

**Done when:**
- The schedule runs.
- An export restores to a local D1.
- Retention is enforced.
