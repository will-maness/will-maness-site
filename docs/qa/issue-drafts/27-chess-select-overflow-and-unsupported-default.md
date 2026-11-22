---
title: "fix(chess): tournament picker overflows mobile by 251px and defaults to an unsupported event"
labels: bug, mobile
---

## Problem

Two issues on `/work/chess` on production, 2026-11-22:

1. **Mobile overflow.** At 390px the `<select id="tournament-select">` sizes
   to its longest option ("35. ekipno prvenstvo Hrvatske 2026 (Croatian Team
   Championship) - 1. ženska HŠL") and renders 617px wide. The page scrolls
   sideways by 251px, the worst on the site (the footer accounts for 45px
   everywhere; see 13).
2. **Unhelpful default.** The tracker auto-selected "FIDE World Cadet
   Championship 2026 | G08" (under-8 girls) and showed "...uses a format that
   isn't supported yet." The intro promises it "auto-discovers the current
   top Lichess Broadcast." A hiring manager's first view of the feature is a
   not-supported message.

## Fix

- Add `w-full max-w-full` (or a token-based max width) and `truncate` to the
  select in `ChessTrackerInner.tsx:39-43`.
- In `useTournament.ts`, choose the default from tournaments whose format is
  supported, ranked by Lichess broadcast tier, live ones first. Fall back to
  the first entry only if none qualify.

## Acceptance criteria

- [ ] `/work/chess` has no horizontal overflow from the select at 390px
- [ ] The default selection renders standings/pairings, not the unsupported message, whenever any supported tournament exists
