---
title: "fix(pipeline-dashboard): health status reports 'success' for pipelines that are stale or failing"
labels: bug, high-priority
---

## Problem

`/work/pipeline-dashboard` is the page that tells a data-tooling hiring
manager "I know how to run pipelines." On 2026-11-22 production it showed
all three pipelines with `status: "success"` (green), while the same cards
showed they were broken. First SSE event from `/api/pipeline-status`:

```
311          success  lastSuccessAt 2026-11-21  recordCount 0
vendor-feed  success  lastSuccessAt 2026-06-21  recordCount 33  error "1424 pages failed"
ember        success  lastSuccessAt 2026-11-22  recordCount 0
```

Vendor Feed hasn't extracted anything in five months and has 1,424 failed
pages, and it still gets a green dot. The GitHub Actions runs behind it
(`pipeline-vendor-feed.yml`, `pipeline-311.yml`) also conclude `success`
every day, so nothing alerts on this anywhere.

## Root cause

- `buildVendorFeedStatus` (`src/lib/PipelineStatusService.ts:56`) sets
  `success` if `extracted > 0` across **all time** in `vf_raw_pages`. Once
  anything has ever been extracted, it's green forever.
- `ember-ingest` (`src/app/api/cron/ember-ingest/route.ts:288`) upserts
  `success` whenever `runIngest` doesn't throw, even with 0 clusters and no
  weather updated (see draft 26).
- The 311 run row records `success` with `record_count = 0`.
- Nothing compares `lastSuccessAt` against the pipeline's expected cadence.

## Fix

- Derive health in one place from recency + outcome: `failed` if the last
  attempt errored, `stale` (yellow) if `lastSuccessAt` is older than ~2×
  the cron cadence, `success` only otherwise.
- Vendor feed: scope the counts to the latest run (or last 24h), not all
  of `vf_raw_pages`.
- Treat "0 records" as degraded for pipelines where zero is never normal
  (311), and have the ingest jobs write `failed`/`degraded` rather than
  `success` when the meaningful output is empty.
- Make the workflows exit non-zero when a run produces nothing, so GitHub
  emails on failure.
- Then actually fix the vendor-feed scraper (1,424 failed pages).

## Acceptance criteria

- [ ] A pipeline with no success within 2× its cadence renders as stale, not green
- [ ] Vendor Feed shows failed/stale on today's data
- [ ] Unit tests in `PipelineStatusService.test.ts` cover stale and zero-record cases
- [ ] A broken run turns the GitHub Actions job red

## Files

`src/lib/PipelineStatusService.ts`, `src/app/api/cron/ember-ingest/route.ts`,
`src/components/projects/pipeline-dashboard/PipelineCard.tsx`,
`.github/workflows/pipeline-*.yml`
