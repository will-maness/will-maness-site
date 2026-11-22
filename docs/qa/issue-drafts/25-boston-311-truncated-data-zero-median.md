---
title: "fix(boston-311): dashboard shows near-empty recent weeks and '0.0d' median close as normal"
labels: bug, data, high-priority
---

## Problem

On 2026-11-22 the Boston civic-data dashboard said **"All 4 departments
operating normally."** The underlying `/api/311-departments` response
doesn't support that:

| Department | Latest week | Opened | Closed | Card shows |
| --- | --- | --- | --- | --- |
| Public Health Commission | 2026-09-07 | 1 | 1 | Median close 19.8d |
| Inspectional Services | 2026-11-09 | 2 | 0 | **0.0d** |
| Public Works | 2026-10-12 | 4 | 0 | **0.0d** |
| Transportation – Traffic | 2026-10-26 | 1 | 0 | **0.0d** |

Public Works opening 4 requests in a week is not plausible for Boston, and
the latest weeks end at four different dates while the footer says "Data
last updated Nov 21, 2026". The Request Type Breakdown says "No data
available for this department." The 311 card on the Pipeline Dashboard
shows `Records 0`, and the nightly workflow still concludes `success`.

A VP of Product will read "median close 0.0 days" as either a bug or a
department that closes every ticket instantly. Either way, it undercuts the
best-written project on the site.

## Root cause (likely; needs confirming against the pipeline logs)

- Ingest looks truncated or filtered: recent weeks have a handful of rows.
  Check the CKAN query/resource id in the 311 pipeline. Analyze Boston may
  have rotated the dataset resource for the new year.
- `src/lib/boston-311.ts:361` coerces a `null` median (no closures) to `0`.
- `DepartmentCard.tsx:106-108` uses the last, often partial, week for the
  headline median.
- The "all normal" banner doesn't check data freshness or volume.

## Fix

- Fix the ingest, and fail the run when the row count drops well below the
  trailing average.
- Keep `medianDays` nullable end to end; render "—" when nothing closed.
- Use the last complete week for headline numbers.
- Show an "insufficient data" state instead of "operating normally" when
  volume or freshness checks fail.

## Acceptance criteria

- [ ] Each department's latest complete week has realistic volume
- [ ] No card renders "0.0d" for a week with zero closures
- [ ] Banner can't say "operating normally" on stale or near-empty data
- [ ] Tests in `boston-311.test.ts` cover null median and partial-week cases
