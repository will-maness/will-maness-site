# QA pass: 2026-11-22, live site read as a VP-of-Product hiring manager

_Thirteenth pass. Crawled all 17 sitemap routes plus a 404 on production
(`www.willmaness.com`) at 1440px and 390px with headless Chromium, logging
console errors, failed requests, `<h1>` count, horizontal overflow and
broken images. This time I also read the live API payloads behind each
embedded dashboard and the recent GitHub Actions runs for the data
pipelines. `main` is still `dd40435` (2026-08-01)._

## What a hiring manager sees

The shell is still in good shape. Every page has one `<h1>`, there are no
console errors or broken images, and the custom 404 works. The writing and
positioning remain the strongest part of the site.

This pass looked past the page chrome at the **data inside the live
widgets**, and that's where the biggest new problem is. The site's pitch is
"I build data products." Right now three of its live data products are
quietly serving stale or broken data, and the dashboard built to monitor
them says everything is green:

1. **Pipeline Dashboard reports success on broken pipelines (24).** Vendor
   Feed's last successful extraction was June 21, with "1424 pages failed",
   and it still shows a green dot. 311 and EmberBrief show `Records 0` and
   are also green. The nightly GitHub Actions jobs all conclude `success`,
   so no alert fires anywhere. For a data-infra hiring manager, a health
   check that can't detect failure is the worst possible thing to put on
   this page.
2. **Boston 311 says "All 4 departments operating normally" on near-empty
   data (25).** Recent weeks show 1–4 requests opened per department, and
   three cards show a median close time of "0.0d" because a null median
   becomes 0. This is the best-written project on the site, and the live
   numbers contradict its own logic.
3. **EmberBrief shows June weather as current conditions (26).** "Last
   checked Jun 6." This is the visible symptom of open issue #210.

Smaller, new: the chess tracker's tournament `<select>` is 617px wide on a
phone, so the page scrolls sideways by 251px. Today it also defaulted to an
under-8 event in a format it doesn't support (27).

## Re-verified unchanged since 11-15

- 11: `/opengraph-image` is 200 with **0 bytes**.
- 12/21: "Where the data infrastructure moat actually lives" is still one
  line, "Placeholder — full essay coming soon.", and is in the sitemap and RSS.
- 13: 45px horizontal overflow at 390px on every page.
- 18: `/work/vendor_feed` is 200 and ~800 KB.
- 20: sitemap and `og:image` use the apex domain; the apex 307s to `www`.
- 23: StackList overflow on Boston (72px) and EmberBrief (56px).
- 22: the chess tracker loaded fine today (no 429), but the `Promise.all`
  fan-out is unchanged, so the risk remains.

## The pattern

Nothing in the pipelines fails loudly. Jobs exit 0, rows are written as
`success`, and the UI shows the last value it has with no freshness check.
Drafts 24–26 are really one theme: **make staleness visible**. Draft 24
should go first because it makes the other problems visible on the site.

## Recommendation

Merge this PR and close #245 and #248–#256 unmerged, since this branch
contains all of them. File 11 and 12 first (small and very visible), then
24 and 25.
