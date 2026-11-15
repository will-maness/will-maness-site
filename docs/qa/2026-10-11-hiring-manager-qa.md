# QA pass — 2026-10-11, live-site read as a VP-of-Product hiring manager

_Ninth pass in the series. This one tested the **production site**
(`www.willmaness.com`) over HTTPS rather than a local build, because `main` has
been frozen at `dd40435` since 2026-08-01 and nothing a local build shows has
changed. No application code is touched by this PR._

## Result: both blockers still live in production (10+ weeks)

| # | Finding | Live evidence (2026-10-11) |
| - | ------- | -------------------------- |
| 11 | Homepage share image broken | `GET /opengraph-image?643ad4a142de2069` → `200 image/png` with a **0-byte body**. Twitter/LinkedIn/Slack/iMessage previews of the root domain show no image. Cause per #248/#250: Satori needs `display: flex` on the title `<div>` in `src/app/opengraph-image.tsx`. |
| 12 | Placeholder essay published | `/writing/where-the-data-moat-lives` renders title, dek and "9 min read", then the body is one line: "Placeholder — full essay coming soon." Still in `/feed.xml` (3 items) and `/sitemap.xml`. Dated April 2026. |
| 18 | `/work/vendor_feed` vs `/work/vendor-feed` | Both return 200 with near-duplicate content; neither carries `noindex`. `vendor_feed` ships ~800 KB of HTML vs ~60 KB for `vendor-feed`. Same failure mode already fixed for `/ember`. |

## Spot-checks that passed

- All 19 sitemap routes return 200 in under 1 s (one outlier: `vendor_feed`, 2.2 s).
- Each page has exactly one `<h1>`; `/ember` is `noindex, follow`; unknown routes return a styled 404 with `noindex`.
- `/about` copy reads well and the outreach-email framing works; no regressions on findings 01–09.
- `/rss.xml` is a 404 (feed lives at `/feed.xml`, which `/about` links correctly) — fine, noting only for completeness.

## Hiring-manager read

The first impression is strong, and the two blockers are exactly where a
reviewer looks first: the link preview when the URL is pasted into a
message, and the writing index, where the one essay titled around the
site owner's core thesis (data-stack defensibility) is empty. Either unpublish
the placeholder (`draft`/remove from index and feed) or write it; a visible
stub undercuts the "considered, current" positioning more than having two
essays would.

## Recommendation (one action)

Merge #250 (it supersedes #245, #248, #249, #251), then land the two one-file
fixes for 11 and 12. The full 19-item backlog and suggested fixes live in
`docs/qa/issue-drafts/` as staged in #250. **No GitHub issues were filed by
this pass**; per the review-first convention, nothing is filed until you
approve. This PR deliberately does not re-add those draft files, to avoid a
sixth overlapping copy.
