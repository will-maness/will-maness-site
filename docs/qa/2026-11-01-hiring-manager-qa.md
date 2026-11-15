# QA pass — 2026-11-01, live-site read as a VP-of-Product hiring manager

_Eleventh pass. Re-tested production (`www.willmaness.com`) with curl against
the 10-18 report (PR #254). `main` is still `dd40435` (last commit 2026-08-01)._

## Result: no change since 10-18, no new findings

- `/opengraph-image` → 200, **0 bytes** (finding 11, still broken).
- `/writing/where-the-data-moat-lives` still reads "Placeholder — full essay
  coming soon." and is still in `/feed.xml` (3 items) and the sitemap (12, 21).
- `og:image` still points at the apex `willmaness.com`, which 307-redirects to
  `www` (20).
- `/work/vendor_feed` still 200 at ~800 KB, no `noindex` (18).
- `/`, `/about`, `/work`, `/writing`, `/ember` all 200; unknown routes return
  the styled 404.

## Hiring-manager read

Unchanged: the positioning is credible, but a blank link preview and a stub
essay advertised on the homepage, writing index and feed undercut "considered,
current." The public repo also shows 9 open QA PRs, which a reviewer will see.

## Recommendation

Don't merge another QA PR. Merge #254 (it carries the full draft set, 20 and
21 included), close #245, #248–#253 and this one as superseded, then land the
fixes for 11, 12 and 20.

**No new issue drafts this week;** the existing drafts in
`docs/qa/issue-drafts/` (and on #254) already cover everything above. Nothing
is filed until you approve.
