---
title: "fix(chess): tracker fails entirely when one Lichess PGN request gets a 429"
labels: bug, high-priority
---

## Problem

On 2026-11-15, `/work/chess` on production rendered **"Could not load
tournament data." / Try again** where the tracker should be (desktop, 1440px,
headless Chromium). Network log for that load:

```
broadcast?nb=30 → 200
study/<round>.pgn ×6 → 200, 200, 200, 429, 200, 429
```

Directly below the error, the page copy says the tracker "backs off
automatically when the rate limit gets tight." To a hiring manager, a
failing widget sitting on top of a claim that it handles rate limits looks
worse than either problem would on its own.

## Root cause

`fetchRoundData` in `src/components/projects/chess/BroadcastService.ts`
(line ~388) fetches **every played round's PGN in parallel** with
`Promise.all`, straight from the visitor's browser:

- Any single non-OK response throws and rejects the whole batch, so one
  429 sinks standings, pairings and format detection together.
  `useTournament.ts` catches that and dispatches `FETCH_ERROR`.
- The 60-second poll refetches *all* played rounds every time, including
  finished rounds whose PGN can't change. Request volume grows with the
  length of the event.
- The rate-limit backoff (`resolvePollingInterval`) only reads headers from
  the broadcast-list call and the single-game call, never from the PGN
  fan-out. When the PGN calls get a 429, nothing backs off.

**Caveat:** the sandbox egress IP is shared, so it probably hits Lichess's
per-IP limit more often than a typical visitor would. Earlier crawls the same
morning loaded fine. Even so, the design has no tolerance for a single 429,
and the error is reproducible.

## Fix

1. Cache finished-round PGNs (in memory, or in `sessionStorage` keyed by round
   id) and refetch only `ongoing` rounds on each poll.
2. Fetch sequentially or with a concurrency limit of about 2, and on a 429
   honor `Retry-After` / back off instead of throwing.
3. Use `Promise.allSettled` and render partial standings with a small
   "some rounds still loading" note instead of failing everything.
4. Longer term: proxy through a cached route handler (`revalidate: 60`) so
   Lichess sees one client (the site) instead of every visitor.

## Files

- `src/components/projects/chess/BroadcastService.ts`
- `src/components/projects/chess/useTournament.ts`
- `content/projects/chess.mdx` (make the backoff claim match the real behavior)
