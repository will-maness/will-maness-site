# QA pass — 2026-10-18, live-site read as a VP-of-Product hiring manager

_Tenth pass. Tested production (`www.willmaness.com`) over HTTPS with curl,
plus a source cross-check against `main` (`dd40435`, unchanged since
2026-08-01). Read alongside the open QA PRs (#245, #248–#253); this pass adds
two new findings and does not re-add the earlier drafts._

## Result: nothing fixed since 10-11, two new findings

Live state is identical to the 10-11 pass:

- `/opengraph-image` → `200 image/png`, **0 bytes** (finding 11, still broken).
- `/writing/where-the-data-moat-lives` still the one-line placeholder, still
  in `/feed.xml` (3 items) and `/sitemap.xml` (finding 12).
- `/work/vendor_feed` (800 KB) and `/work/vendor-feed` (60 KB) both 200, no
  `noindex` (finding 18).
- All other routes 200; unknown routes return the styled 404.

## New findings

| # | Finding | Evidence |
| - | ------- | -------- |
| 20 | Canonical host mismatch: site is served on `www`, but metadata points at the apex | `metadataBase` in `src/app/layout.tsx:29` is `https://willmaness.com`. The apex 307-redirects to `www`. As a result `og:image`, every `<loc>` in `/sitemap.xml`, and the `Sitemap:` line in `/robots.txt` all name a URL that redirects. Crawlers and some unfurlers (Slack, LinkedIn) handle redirects inconsistently, and it compounds finding 11. No `<link rel="canonical">` is emitted on the homepage. |
| 21 | The homepage promotes the placeholder essay | "Recent writing" on `/` lists the three essays by date; the 9-minute "Where the data infrastructure moat actually lives" is the third card and links to the stub. A hiring manager reads this on the front door, not only on `/writing`, so finding 12 is more visible than the 10-11 report implied. |

## Hiring-manager read

The positioning lands: the homepage opener and the About page's outreach-email
framing are credible and human. Three things undercut "considered, current"
before the first project is opened: a blank link preview, an advertised essay
that is a stub (on the homepage, the writing index, and the feed), and a
`2026-08-01` main freeze that leaves 8 QA PRs and the earlier fix PRs
unreviewed. The unreviewed queue is itself visible in the public repo, which
this site invites reviewers to read.

## Recommendation (one action)

Merge #250, close #245, #248, #249, #251, #252 and #253 as superseded, then land
the two one-file fixes for 11 and 12 and the one-line `metadataBase` fix for 20
(point it at `https://www.willmaness.com`, or move the redirect so apex is
canonical and update the `www` domain setting to match).

**No GitHub issues were filed.** Drafts for the two new findings are in
`docs/qa/issue-drafts/20-*.md` and `21-*.md`; nothing is filed until you approve
this PR.
