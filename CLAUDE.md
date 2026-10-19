# chores-reaper — Project Instructions

## What this repo is

A **frozen-in-time snapshot** of the household chore tracker that runs on the wall-mounted
Raspberry Pi touchscreen: Express + `better-sqlite3` (WAL) API and a React/Vite/Tailwind
frontend behind Nginx, deployed as two Docker Compose containers built natively on the Pi.
`README.md` is the architecture, behaviour and deployment reference; read it first.

Until the 2026-10-16 folder swap this repo lived at `~/Code/chores4irl`. Its README, package
name, systemd units, backup script and plans still say **chores4irl**. That is this app's old
name. Don't rename it in code, units or scripts: the deployed Pi depends on those names.

## Relationship to `chores4irl` (the go-forward repo)

| | `chores-reaper` (this repo) | `chores4irl` (`~/Code/chores4irl`) |
|---|---|---|
| Remote | `rehankalu/chores-reaper` | `4IRL/chores4irl` |
| Stack | Express + SQLite + Docker on a Pi, LAN-only kiosk | Cloudflare Workers + Hono, D1, Pages, Access; multi-household PWA at `chores.4irl.app` |
| Age | Newer app features (through CR-#60) | Older snapshot: seeded from this repo at `42040cc` (CR-#21, 2026-06-30) and re-architected for Cloudflare |
| Role | **Maintained until harvested**, then retired | Where new feature work happens |

The two names have effectively swapped: the Cloudflare app titles itself "Chore Reaper", while
this repo's docs call this app "chores4irl". Say which repo you mean whenever it's ambiguous.

`~/Code/chores4irl` is a separate repo and is **read-only from this session**. Read its
`CLAUDE.md` and `ARCHITECTURE.md` when you need to know how TARGET works. Make changes there
only from a session opened in it.

## What work belongs here

- **Harvesting.** `plans/fold-into-chores4irl/FOLD-LEDGER.md` is the authoritative map of
  which features here get ported to chores4irl, in what order, and how each is adapted to its
  architecture, plus which ones are deliberately not ported. Ports are **implemented in
  chores4irl**. This repo supplies the reference implementation, tests and push-review
  findings (`plans/PUSH-REVIEW-FINDINGS.md`).
  - **ID namespaces never mix.**
    - In the ledger, `B#` (bugs) and `F#` (features) are **chores4irl** IDs. They are numbered in
      implementation order, and B1–B4 are fixed in turn before any feature.
    - `CR-F#` and `CR-#nn` are this repo's F-IDs and PR numbers.
    - In this repo's `META-PLAN.md`, bare `F#` means chores-reaper IDs.
  - **Where things live.** The ledger is a temporary reference copy, kept only here. The
    canonical planning lives in two places:
    - The [chores4irl wiki META-PLAN](https://github.com/4IRL/chores4irl/wiki/META-PLAN) holds
      the table, order, dependencies and decisions.
    - One `4IRL/chores4irl` GitHub issue per item, labelled `fold`, holds the spec. Once an
      issue exists, it is canonical over the ledger's *Item specs* section.

    Planning documents are never committed into 4IRL org repos.
  - When a port lands in chores4irl, update its ledger row here in the same piece of work.
  - **Owner decisions (2026-10-16)** are recorded in the ledger's *Owner decisions* section.
    Don't re-litigate them. In short:
    - The list stays put until midnight on phones (F10).
    - The swipe threshold applies to the Android swipe style only (F16).
    - Undo and redo are folded in (F11/F12).
    - The wall Pi will show the Cloudflare app.
- **Maintenance.** Keep the wall Pi running: bug fixes, dependency and security updates,
  deploy/backup fixes. A fix that also affects a feature already ported should be noted in
  the ledger so the port picks it up.
- **Not here by default: new features.** Don't add backlog items with `/new-feature` or start
  new `/run-feature` work unless the user asks. New feature ideas go to chores4irl. The
  remaining backlog in `plans/META-PLAN.md` is superseded or handed off:
  - F11 (undo) and F12 (redo) are folded into chores4irl as ledger F11 and F12. Don't build
    them here.
  - **Kiosk work belongs to `rehankalu/pi-kiosk`, not here and not in the chores4irl
    wiki/issues.** That covers F15 (adopt kiosk-shell), the F1 screen-blank, the F2/F20 touch
    lock, the F19 lock-time view reset, and how the wall Pi displays the app.
    - The ledger's *Handed to pi-kiosk* section records the display-method options: full-screen
      Chromium locked to `chores.4irl.app`, versus the iframe shell.
  - F23 (Pi 5 hosting) is probably made obsolete by the decision that the wall Pi shows the
    Cloudflare app. It is left out of the fold. Don't start it unless the user revives it.

**Retirement.** Once every ledger row has been ported or explicitly declined, and the wall Pi
no longer depends on this app, the repo is a candidate for archiving through
`/stronghold-sweep` Phase 6. Archiving needs the user's approval, from a session rooted at
`~/Code`.

## Working in this repo

- **Commands** (npm workspaces; see README § Local development):
  - `npm ci`
  - `npm run dev --workspace backend` (:3000) / `npm run dev --workspace frontend` (:5174)
  - `npm run test --workspace backend` / `npm run test --workspace frontend` (vitest)
  - `npx playwright test` (e2e)
  - `npm run lint`
  - `docker compose build && docker compose up -d` (local smoke test)
- **Data safety.** The Pi's `chores-data` volume is real household history. Never suggest
  `docker compose down -v` against it. Schema changes are boot-time migrations in
  `backend/src/db.ts`, guarded by `pragma table_info`. Take a backup before deploying a
  migration (README § Updating an existing Pi deployment).
- **Git.** `main` is protected by `.github/rulesets/main-protection.json`: PR plus one review,
  and the CI checks Backend Tests, Frontend Unit Tests and E2E Smoke Tests. Work on a branch
  and land changes through a PR. Push with `/git-push` or the stronghold `gh-git.sh` helper.
- **Plans.** Plans live in `plans/`, and `plans/META-PLAN.md` is the feature manifest.
  `/compact-plans` archives finished plan folders into `plans/completed/`. The
  `plans/ledger/` files are frozen history.
- **Project skills** in `.claude/skills/` (`run-feature`, `worktree`, `compact-plans`) are
  tracked so worktrees inherit them.
