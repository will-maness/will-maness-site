# QA pass — reading the site as a VP-of-Product hiring manager

_2026-09-27. Eighth pass in this series, following 2026-07-11
(`2026-07-11-hiring-manager-qa.md`, PR #232, merged), 2026-07-18 (PR #233,
merged), 2026-07-25 (PR #242, merged), 2026-08-01 (`2026-08-01`-equivalent
pass, PR #245, still open), 2026-08-23 (PR #248, still open), 2026-08-30
(PR #249, still open), 2026-09-06 (`2026-09-06-hiring-manager-qa.md`, PR
#250, still open — consolidates #245/#248/#249 and holds the current
19-item `docs/qa/issue-drafts/` backlog), and 2026-09-13
(`2026-09-13-hiring-manager-qa.md`, PR #251, still open). Ran a clean
`pnpm install`, `pnpm build` (37/37 routes), `pnpm exec vitest run`
(471/471 passing), `pnpm lint` (clean), and `pnpm start`, then crawled
every published route at desktop (1440px) and mobile (390px) with a
headless browser, capturing console errors, failed requests, and
screenshots, cross-checked against source.

## Headline: `main` has been frozen for eight weeks; this is the sixth open QA PR saying so

`main` is unchanged at `dd40435` (2026-08-01) — **eight weeks**, across five
prior passes that all reached the same conclusion. Nothing has shipped, so
there is no new surface to find regressions or new issues in, and this pass
adds no new files under `docs/qa/issue-drafts/` — the existing 19 (staged in
#250) still cover everything found this week too.

| PR | Pass date | State |
| -- | --------- | ----- |
| #245 | 2026-08-01 | Open 8 weeks, unreviewed |
| #248 | 2026-08-23 | Open 5 weeks, unreviewed, Vercel check failing |
| #249 | 2026-08-30 | Open 4 weeks, unreviewed |
| #250 | 2026-09-06 | Open 3 weeks, unreviewed, consolidates the above three |
| #251 | 2026-09-13 | Open 2 weeks, unreviewed |

**Unchanged recommendation, now nine weeks running: merge #250, close
#245/#248/#249/#251 without merging (fully superseded), and land the two
one-file fixes for findings 11 and 12 below.** They have been sitting ready
the whole time this queue has been accumulating.

## Re-confirmed live against a fresh production build, byte-for-byte

- **Broken homepage share image (finding 11).** `curl http://localhost:3000/opengraph-image`
  still fails; the server log shows the identical Satori error (`Expected
  <div> to have explicit "display: flex"...`) from `src/app/opengraph-image.tsx`.
  `willmaness.com`'s root URL has now shown no link-preview image on any
  platform for **almost two months**.
- **Placeholder essay still live and syndicated (finding 12).**
  `content/writing/where-the-data-moat-lives.mdx` is unchanged — full
  title/dek over one sentence of body (`Placeholder — full essay coming
  soon.`), dated 2026-04-01, now **just under six months old**.
  `/feed.xml` still syndicates the full entry with that date to anyone
  subscribed.
- **`/work/vendor_feed` vs `/work/vendor-feed` route collision (finding 18).**
  Both still return `200` independently — a live dashboard
  (`src/app/work/vendor_feed/page.tsx`) and the canonical MDX write-up
  (`content/projects/vendor-feed.mdx`) with near-identical pitch copy,
  reachable one underscore apart, neither `noindex`ed. Confirmed this is the
  same failure mode already fixed for the analogous `/ember` pair (which
  does carry `noindex`) — it was just never applied to this second instance.

## Everything from the original nine findings (01–09): still fixed, no regressions

Spot-checked all nine originally-filed issues (vendor_feed 500, `/ember`
duplicate indexing, `/about` double `<h1>`, widget raw-error copy, work-card
status indicators, custom 404, scaffold SVGs, stale `MEMORY.md`, Sable
positioning) against today's crawl. All eight code fixes that landed between
07-18 and 08-01 hold up:

- `/work/vendor_feed` returns `200` with a designed fallback, not a 500.
- `/ember` carries `noindex, follow` and site chrome.
- `/about` renders exactly one `<h1>`.
- `StaffingDashboard`, `EmberDashboard`, and `RequestTypeBreakdown` all show
  the designed fallback sentence on fetch failure (503s reproduced locally);
  the raw `API error 503` string only reaches `console.error`, never the DOM.
- Work cards show `● In progress` / `● Forthcoming` status dots.
- `/this-does-not-exist-404-test` renders inside full site chrome with a
  proper 404.
- `public/` holds only the headshot — no leftover scaffold SVGs.
- `MEMORY.md` (root) reflects Phases 0–3 shipped and the post-MVP project
  list.
- `project-stellar.mdx` (Sable) unchanged — still an open editorial call,
  not a bug; no action expected without Will's input.

## New this pass: none

Full crawl (all 24 published routes + 404 + XML feeds, both viewports) found
nothing beyond the 19 already-drafted findings. `/work/pipeline-dashboard`
again fails a strict `networkidle` wait — confirmed by design (its
Server-Sent Events connection stays open for live updates), not a bug.
`lichess.org` TLS errors on `/work/chess` and `_vercel/insights`/
`speed-insights` 404s are the same sandbox-has-no-outbound-network artifacts
noted in every prior pass.

## Bottom line

No regressions, no new findings — because nothing has shipped in two
months. The technical diagnosis has been complete and stable since 08-23.
What's overdue is the same fifteen minutes it's been for weeks: merge #250,
close the four PRs it supersedes, and land the two ready one-file fixes —
findings 11 and 12 are both live on `willmaness.com` right now, in front of
exactly the audience this site exists to impress.
