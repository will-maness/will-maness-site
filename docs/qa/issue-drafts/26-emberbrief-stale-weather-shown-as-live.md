---
title: "fix(ember): dashboard presents five-month-old weather as current conditions"
labels: bug, data
---

## Problem

`/work/ember` says the dashboard shows "current fire activity and
conditions... scored and briefed automatically on a daily cadence." On
2026-11-22 it showed **"Last checked Jun 6, 9:20 AM UTC"**, and
`/api/ember-data` returns `observedAt: Jun 06 2026`. Wind, humidity and
temperature are from June, but they're laid out exactly like live readings.
The only clue is a small timestamp.

This is the downstream symptom of open issue #210 (expired Synoptic API
key). The ingest still writes `success` each day, so nothing flags it
(see draft 24).

## Fix

- Resolve #210 (rotate `SYNOPTIC_API_KEY`).
- Independently: if `observedAt` is older than ~36h, render a clear "Weather
  data unavailable since <date>" state instead of the readings, and skip the
  AI briefing that depends on them.
- Have `ember-ingest` record `degraded` when `weatherUpdated === 0`.

## Acceptance criteria

- [ ] Weather older than the threshold is never shown as current
- [ ] The stale state uses designed copy (consistent with the #235 fallback work)
- [ ] Pipeline Dashboard reflects the degraded weather ingest
