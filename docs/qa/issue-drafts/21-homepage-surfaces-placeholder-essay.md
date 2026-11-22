---
title: "Homepage 'Recent writing' links to the placeholder essay"
labels: bug, content, high-priority
---

## Problem

The homepage's "Recent writing" section lists "Where the data infrastructure
moat actually lives" (9 min read) as its third card. It links to a page whose
body is one line: "Placeholder — full essay coming soon." This extends
finding 12: the stub is not only on `/writing`, `/feed.xml` and the sitemap,
it is on the front door.

## Suggested fix

Either publish the essay, or mark it as a draft and filter drafts out of the
homepage, `/writing`, `/feed.xml`, and `sitemap.ts` in one place (a `draft`
frontmatter flag respected by the content loader).

## Repro

1. Open `https://www.willmaness.com/`, scroll to "Recent writing".
2. Click the third card.
