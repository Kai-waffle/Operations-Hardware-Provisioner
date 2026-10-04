# Hardware Provisioner — project state

> **26 Sep 2026, rechecked 4 Oct against operating structure v0.6 — who uses it:** CS runs the Provisioner **before the quote**, with the site checklist (rule 2); the AM, who sells, owns the quote. The Kiosk gap (PRIORITIES item 25) still stands.

*Last updated: 18 Sep 2026 (file created — repo brought onto the workspace
standard; no code changed)*

## What is live

- **Version 4.1**, last code change 2 Dec 2025 (`6ab40eb`, router model fix:
  ER706W vs ER706W4G-V2).
- A four-step wizard: stations → printers and peripherals → printer
  placement and cabling → infrastructure → the order.
- Two outputs: copy the order text, or send it to a Notion orders database.
- Install grade: LOW 1 h / MEDIUM 1.5 h / HIGH 2 h. Rule in `CLAUDE.md`.
- Hosted on Vercel, auto-deploys from `main`. **Live URL:
  https://hardware-provisioner.vercel.app** (confirmed loading 18 Sep 2026,
  after the docs push).

## What is owed

1. **The app does not know the Kiosk** (Kai, 16 Sep 2026; workspace
   `Journal/PRIORITIES.md` item 25). It must size Kiosk sets and print the
   install tier — Standard / Large — next to the LOW / MEDIUM / HIGH grade on
   the order text. The tier rule is in the job catalogue §2. Agreed rule:
   Standard = POS only at LOW or MEDIUM, or a Kiosk added alone to a live
   site; Large = POS + Kiosk in one trip, or HIGH. A Kiosk never launches
   alone.
2. **Provisioner ↔ Proof of Delivery link** — same priorities item. No shared
   identifier is defined yet. Name the join key before planning it.
3. **Two known issues from the v4.1 spec, never fixed:** spell out ISP
   (Internet Service Provider) on the LAN-port question; warn the AM when a
   label printer (ZD411) has no wired path.

## Gaps against the workspace standard

- **No tests.** Add them with the first code change — the hardware rules in
  `pages/index.tsx` are pure logic and easy to test once pulled out.
- **Code is not under `src/`.** Kai's call, 18 Sep 2026: leave it. Moving it
  is safe (Next.js supports `src/pages`) but not worth a release on its own.
  Do it alongside the Kiosk work if at all.
- **`README.md` is the original setup guide.** Still correct for setup; its
  "Project Structure" block predates `docs/`.
- **The live Notion orders database has not been checked against the
  property list in `CLAUDE.md`.** Do that before touching the endpoint.

## Decisions

| Date | Decision |
|---|---|
| 18 Sep 2026 | Repo adopts the workspace standard layout (`CLAUDE.md`, `docs/PROJECT_STATE.md`, `docs/plans`, `docs/handovers`, `docs/reference`). Docs only; `pages/` and `types/` stay at the root. |
| 18 Sep 2026 | The spec moved to `docs/reference/SPEC-v4.1.md`. The root `.txt` copy was the older v3.9 text and was removed; it stays in git history. |

## Where the history is

- This repo's git log (Dec 2025).
- `~/Waffle-Operations/Projects/Hardware-Provisioner/Archive` —
  15 HTML prototypes and the old handovers. Frozen.
