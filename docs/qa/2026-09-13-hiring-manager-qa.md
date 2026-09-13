# QA pass — reading the site as a VP-of-Product hiring manager

_2026-09-13. Seventh pass in this series, following 2026-07-11
(`2026-07-11-hiring-manager-qa.md`, PR #232), 2026-07-18 (PR #233),
2026-07-25 (PR #242, merged), 2026-08-01 (PR #245, still open), 2026-08-23
(PR #248, still open), 2026-08-30 (PR #249, still open), and 2026-09-06
(`2026-09-06-hiring-manager-qa.md`, PR #250, still open — consolidates
#245/#248/#249). Ran a clean `pnpm install`, `pnpm build`,
`pnpm exec vitest run`, `pnpm lint`, and `pnpm start` against today's
`main` and independently re-hit the two blocker findings against the
running server rather than trusting last week's write-up._

## Headline: this is now the fifth open QA PR, and the two blockers have been live on production for a month

`main` is unchanged at `dd40435` — frozen for **six weeks** (since
2026-08-01). #250 already did the work of consolidating the four prior open
QA PRs and re-deriving the full 19-item backlog with suggested fixes in
`docs/qa/issue-drafts/`; nothing has changed since then, so this pass does
not repeat that work or re-add those draft files. It exists to (a)
independently re-confirm the two blockers are still live and (b) flag that
the review queue itself is now the actual problem:

| PR | Pass date | State |
| -- | --------- | ----- |
| #245 | 2026-08-01 | Open 6 weeks, unreviewed |
| #248 | 2026-08-23 | Open 3 weeks, unreviewed, Vercel check failing |
| #249 | 2026-08-30 | Open 2 weeks, unreviewed |
| #250 | 2026-09-06 | Open 1 week, unreviewed, consolidates the above three |

**Recommendation, unchanged from last week and more urgent now: merge
#250, close #245/#248/#249 without merging (fully superseded), and treat
findings 11 and 12 below as this week's actual work.** Both are one-file
fixes. Opening a sixth overlapping PR next week instead of merging one of
the existing five would be the wrong call — the diagnosis has been stable
for three straight passes.

## Re-confirmed against a running server: both blockers, byte-for-byte

- **Broken homepage share image (finding 11).** `curl` against a fresh
  `pnpm build && pnpm start` on `/opengraph-image` still fails; the server
  log shows the identical Satori error (`Expected <div> to have explicit
  "display: flex"...`) from `src/app/opengraph-image.tsx`'s title block.
  `willmaness.com`'s root URL has now shown no link-preview image on any
  platform for **at least a month**.
- **Placeholder essay still live and syndicated (finding 12).**
  `content/writing/where-the-data-moat-lives.mdx` is unchanged — full
  title/dek over a one-sentence body, dated 2026-04-01, now **over five
  and a half months old**. `curl .../feed.xml` still syndicates the full
  entry with that date.

Also re-spot-checked finding 18 (the `/work/vendor_feed` vs
`/work/vendor-feed` one-character route collision): both still return `200`
independently, confirming it's still live, not something fixed as a side
effect of anything that has landed.

## Everything else

`pnpm build` (37/37 routes, `/work/[slug]` still `ƒ` dynamic despite
`generateStaticParams` — finding 14, unchanged), `pnpm exec vitest run`
(471/471), and `pnpm lint` are all clean, matching every pass since 08-23.
No source has changed since 09-06, so there is no new surface to find
regressions or new issues in. Full findings 01–19, repro steps, and
suggested fixes remain in `docs/qa/issue-drafts/` on PR #250 — this pass
adds no new draft files since nothing has changed.

## Bottom line

No regressions, no new findings — because nothing has shipped. The
technical diagnosis has been complete and stable since 08-23. What's
overdue is fifteen minutes to merge #250, close the three PRs it
supersedes, and land the two one-file fixes (11, 12) that have now been
sitting ready, with a broken share image and a stale placeholder post live
on `willmaness.com`, for roughly a month.
