# Waffle Hardware Provisioner — Agent Guide

Internal web wizard. An Account Manager (AM) enters a seller's site — stations,
printers, cabling, internet — and the app works out the hardware order: router,
switch, cables, printer models, and an install grade (LOW / MEDIUM / HIGH). The
AM copies the order text or sends it to Notion for the operations team.

**Kai is non-technical.** The agent writes all code, runs all builds, and
commits and pushes. Kai only does guided manual steps (clicking in a screen,
pasting a value into Vercel). Never ask Kai to run a terminal command.

Read `docs/PROJECT_STATE.md` next — it says what is live and what is owed.

---

## Stack and deployment

- **Next.js 14** (Pages Router) · **React 18** · **TypeScript** ·
  **@notionhq/client** · hosted on **Vercel**.
- Repo: `Kai-waffle/operations-hardware-provisioner`. Vercel auto-deploys on
  every push to `main` — **a push is a release.**
- Vercel blocks deploys when the commit author email is not a verified GitHub
  account. Always commit as (already set in this repo's local git config):
  ```
  git config user.email "246651816+Kai-waffle@users.noreply.github.com"
  git config user.name  "Waffle Provisioner Build"
  ```
- No tests exist yet. The only gate is the build.

## Build gate (run before every push)

```
npm install
npm run build      # must exit 0
```

## Where the code is

The app is four files. The folder name `pages/` is required by Next.js — do
not rename it.

| File | What it holds |
|---|---|
| `pages/index.tsx` | The whole wizard: state, all four steps, the hardware rules, the order text, and the styles (~2,100 lines, one component) |
| `pages/api/notion/create-order.ts` | Server-side endpoint. Takes the finished order and creates one page in the Notion orders database |
| `types/provisioner.ts` | Every data shape: `Station`, `PrinterPlacement`, `Infrastructure`, `NetworkEquipment`, `Complexity`, `NotionPageData` |
| `pages/_app.tsx` | Next.js wrapper, nothing custom |

The rules that matter, all in `pages/index.tsx`:

| Function | Decides |
|---|---|
| `getPrinterModel` / `getPrinterConnection` | Which printer model, and wired vs WiFi, from the cabling answers |
| `calculateNetworkEquipment` | Router model (ER706W vs ER706W4G-V2 for SIM sites), whether a switch is needed, cable list |
| `calculateComplexity` | The install grade — see below |
| `generateOrderText` | The plain-text order the operations team reads |

### The install grade (as the code stands)

- **HIGH, 2 hours** — more than 5 printers, or a label printer with no cable.
- **MEDIUM, 1.5 hours** — more than 2 wired printers, or a switch.
- **LOW, 1 hour** — everything else.

Stations play no part in the grade. **The app does not know the Kiosk** — see
`docs/PROJECT_STATE.md`.

## Environment variables

Names live in `.env.example`. Real values live only in `.env.local` (this Mac,
never in git) and in Vercel → Settings → Environment Variables. Never print a
value in chat — see the workspace rule on secrets.

| Name | What it is | Where the value comes from |
|---|---|---|
| `NOTION_API_KEY` | Token for the Notion integration that writes orders | notion.so/my-integrations → the Provisioner integration |
| `NOTION_DATABASE_ID` | The Notion database that receives orders | The database's URL, the part before `?v=` |

Both are read server-side only, in `create-order.ts`. They never reach the
browser.

## Notion contract

`create-order.ts` writes these properties by exact name: `Customer Name`
(title), `Total Stations`, `POS iPads`, `CDS iPads`, `Total Printers`,
`POS Stands`, `Cash Drawers`, `Card Readers` (numbers), `Router`, `Switch`,
`Estimated Time` (text), `Complexity` (select: LOW / MEDIUM / HIGH). The order
text goes in the page body as a code block.

Kai edits live Notion by hand. **Check the live database's property names
before any work that touches this endpoint** — a silent rename breaks the
write. If live Notion differs from this list, ask Kai why before coding
around it.

## Rules

- No Notion schema change without Kai's yes.
- Customer-facing copy never says "seller".
- Docs follow the workspace standard: state in `docs/PROJECT_STATE.md`, plans
  in `docs/plans/PLAN-YYYY-MM-DD-topic.md`, handovers in `docs/handovers/`,
  long-lived explainers in `docs/reference/`.
- History from before this repo (15 HTML prototypes, old handovers) is frozen
  in `~/Desktop/Waffle-Operations/Projects/Hardware-Provisioner/Archive`.
