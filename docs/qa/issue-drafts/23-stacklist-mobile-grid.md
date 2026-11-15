---
title: "fix(ui): StackList's fixed 4-column grid breaks on mobile"
labels: bug, mobile, medium-priority
---

## Problem

Every project page with a "Stack" section renders it through
`src/components/ui/StackList.tsx`, which sets its grid with an inline style:

```tsx
style={{ gridTemplateColumns: "22px 1.4fr 2fr 110px" }}
```

At 390px that leaves the role column about 90px wide. In the 2026-11-15
screenshots of `/work/boston-civic-data`:

- Role text wraps to 5–6 lines per row ("Nightly / pipeline cron / — fetches
  / from CKAN, …").
- Unbroken strings run into the frequency column.
- The `DAILY` label pushes past the viewport: its bounding rect ends at
  x=462 on Boston and x=446 on EmberBrief. That's why those two pages
  overflow by 72px and 56px, more than the 45px the footer causes on every
  page (finding 13).

This also breaks two of the project's own rules: the QA routine flags inline
`style=` on Tailwind layout containers, and `bg-[#8C9D7B]` in the legend
is a raw hex value (already covered by finding 16).

## Fix

Switch to Tailwind classes with a mobile layout. For example, at `< sm`
stack name + frequency on one line with the role underneath
(`grid-cols-[22px_1fr_auto]`, role spanning columns 2–3), and only use the
4-column grid from `sm:` up. Re-check `/work/boston-civic-data` and
`/work/ember` at 390px.

## Files

- `src/components/ui/StackList.tsx`
