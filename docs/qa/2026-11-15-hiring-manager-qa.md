# QA pass: 2026-11-15, live site read as a VP-of-Product hiring manager

_Twelfth pass. Crawled all 17 published routes on production
(`www.willmaness.com`) at 1440px and 390px with headless Chromium, logging
console errors, failed requests, `<h1>` count, horizontal overflow and
broken images. Cross-checked against source on `main` (`dd40435`, unchanged
since 2026-08-01)._

## What a hiring manager sees

The positioning is still the strongest part of the site. The hero ("I work
on product and strategy in AI infrastructure and data tooling — and I build
to stay grounded"), the in-progress status pills, and the depth of the
project write-ups all read as considered. The Boston civic-data
"two-week minimum for red" logic is exactly the kind of judgment a VP of
Product is looking for. There are no console errors, every page has one `<h1>`,
and no images are broken.

The problems are all about credibility, and three of them hit early:

1. **Sharing the URL shows a blank card.** `/opengraph-image` still returns
   200 with 0 bytes (11). It's usually the first impression in Slack or
   LinkedIn.
2. **The homepage advertises an essay that doesn't exist.** "Where the data
   infrastructure moat actually lives" is still one line, "Placeholder —
   full essay coming soon.", and it's linked from the homepage, `/writing`,
   RSS and the sitemap (12, 21).
3. **New: the chess tracker failed live (22).** One load today showed
   "Could not load tournament data." Two of six parallel Lichess PGN
   fetches got a 429 and `Promise.all` threw away the rest. The paragraph
   directly below says it "backs off automatically when the rate limit gets
   tight." A reviewer who reads code will notice that contradiction.

On a phone, every page scrolls sideways (13). The Stack tables on project
pages are hard to read, and on Boston and EmberBrief they push the overflow
further (23, new).

## Re-verified unchanged since 11-01

11, 12, 13, 18, 20 and 21 are all reproduced on production today. The others are
unchanged in source because `main` hasn't moved.

## The meta-finding: the QA queue is now the most visible defect

The repo is public, and the codebase is part of the pitch. It currently has
**10 open QA PRs** (#245, #248–#255) that each restate the last one, and two
blockers have been open for 12 weeks. A hiring manager who opens the repo
sees a backlog nobody is working through, which undercuts the site's message.
That's a bigger signal than any single bug above.

That's why this PR consolidates everything rather than adding an 11th
restatement (see `issue-drafts/README.md`). Separately, the routine
could open a PR only when it finds something new, instead of posting a weekly
"no change" PR.

## Recommendation

Merge this PR, close #245 and #248–#255 without merging, and file 11, 12
(with 21 folded in) and 22 first. 11 and 12 are each about a one-file fix.
