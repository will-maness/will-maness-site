---
title: "Metadata points at the apex domain, but the site is served on www"
labels: bug, seo, low-priority
---

## Problem

`metadataBase` in `src/app/layout.tsx` is `https://willmaness.com`, but the apex
307-redirects to `https://www.willmaness.com`. So `og:image`, every `<loc>` in
`/sitemap.xml`, and the `Sitemap:` line in `/robots.txt` reference a URL that
redirects. Unfurlers and crawlers handle that inconsistently, and it makes the
broken OG image (finding 11) harder to diagnose. No `<link rel="canonical">`
is emitted.

## Suggested fix

Set `metadataBase` to the host the site is actually served on
(`https://www.willmaness.com`), and add `alternates: { canonical: "./" }` so
each page declares a canonical URL. Update `sitemap.ts` / `robots.ts` if they
hardcode the host.

## Repro

1. `curl -sI https://willmaness.com/sitemap.xml` → `307` to `www`.
2. `curl -s https://www.willmaness.com/ | grep og:image` → apex URL.
