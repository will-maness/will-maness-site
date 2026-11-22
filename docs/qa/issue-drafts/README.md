# QA issue drafts: consolidated index (as of 2026-11-22)

Draft GitHub issues from the hiring-manager QA passes in `docs/qa/`. **None
of these have been filed as GitHub issues.** Approve this PR, then file the
ones you want tracked. Title and labels are in each file's frontmatter.

**This PR is the complete set.** Before today, drafts 10–19 existed only on
PR #250 and drafts 20–21 only on PR #254, and none of the QA PRs since
#245 had merged. This branch collects all 27 drafts and every report from
2026-08-01 through today, so #245 and #248–#256 can be closed without
merging and without losing anything.

Status was re-verified against production (`www.willmaness.com`) on
2026-11-22. `main` is still `dd40435` (2026-08-01). The 2026-11-22 branch
builds on the 11-15 branch (#256), so it also supersedes #256.

| # | Title | Priority | Status |
| - | ----- | -------- | ------ |
| 01–08 | Original July findings | — | Resolved (#234–#241) |
| 09 | Consider: "Sable" doesn't reinforce the technical-fluency narrative | Discussion | Open (editorial call) |
| 10 | Pipeline Dashboard copy describes chess tracking it doesn't do | — | Superseded by 15 (don't file) |
| 11 | Homepage share image is 0 bytes (missing `display:flex` in OG route) | **Blocker** | Open since 08-23, still 0 bytes in prod |
| 12 | A live essay is placeholder text (and in RSS + sitemap) | **Blocker** | Open since 08-23, still live |
| 13 | Mobile footer overflows on every page | Medium | Open, 45px overflow at 390px |
| 14 | `/work/[slug]` force-dynamic despite `generateStaticParams` | Medium | Open |
| 15 | Pipeline Dashboard: four disagreeing sources of truth | Medium | Open |
| 16 | Hardcoded hex colors violate the token rule | Low | Open |
| 17 | No route-level error boundaries | Low | Open |
| 18 | `/work/vendor-feed` vs `/work/vendor_feed` near-duplicate URLs | Low | Open, `_feed` still 200 and ~800 KB, indexable |
| 19 | Small content nits (bundled) | Low | Open |
| 20 | Metadata/`og:image` point at apex, site served on `www` | Low | Open |
| 21 | Homepage "Recent writing" links to the placeholder essay | **High** | Open (fold into 12) |
| 22 | Chess tracker fails entirely on a single Lichess 429 | **High** | Open (loaded OK on 11-22; code unchanged) |
| 23 | StackList 4-column grid breaks on mobile | Medium | Open (Boston 72px, EmberBrief 56px) |
| 24 | **New:** Pipeline Dashboard shows green for stale/failing pipelines | **High** | New 2026-11-22 |
| 25 | **New:** Boston 311 dashboard: near-empty recent weeks, "0.0d" medians, "all normal" | **High** | New 2026-11-22 |
| 26 | **New:** EmberBrief shows June weather as current conditions (symptom of #210) | Medium | New 2026-11-22 |
| 27 | **New:** Chess picker overflows mobile by 251px; defaults to unsupported event | Medium | New 2026-11-22 |

**Suggested filing order:** 11 + 12/21 (one PR each, very visible, small),
then 24 + 25 (the live-data credibility problem; 24 first so the dashboard
stops hiding the others), then 22 + 27 (chess), then 13 + 23 together (mobile
overflow), then the rest.
