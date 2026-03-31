---
title: Performance Considerations
description: >
  How Waulter affects Core Web Vitals and what to do to minimise impact on
  LCP, CLS, and INP.
status: stub
---

# Performance

<!-- TODO:
  - SDK is loaded async — no render blocking
  - Banner injection and CLS (use CSS to reserve space or use position:fixed)
  - Recommended: load Waulter tag on "Consent Initialization - All Pages" (fires
    very early in GTM lifecycle, before DOM-dependent tags)
  - Avoid loading Waulter with a defer tag if you need consent signals before
    other tags fire
  - Testing Core Web Vitals with an active consent banner
-->
