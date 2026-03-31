---
title: Performance Considerations
description: >
  How Waulter affects Core Web Vitals and what to do to minimise impact on
  LCP, CLS, and INP.
---

# Performance

The Waulter SDK is designed to have minimal impact on page performance. This guide explains how the SDK loads, what affects Core Web Vitals, and how to optimise your implementation.

## How the SDK loads

The Waulter SDK loads **asynchronously** — it does not block page rendering:

1. The browser encounters the `<script src="cdn.waulter.cz/sdk.js">` tag.
2. The script downloads in parallel with other page resources.
3. After loading, the SDK creates a hidden element to fetch configuration from the server.
4. Once configuration arrives, the SDK renders the consent banner.

This asynchronous approach means the SDK does **not** block HTML parsing, CSS rendering, or other scripts from executing.

## Impact on Core Web Vitals

### Largest Contentful Paint (LCP)

**Impact: Minimal**

The SDK loads asynchronously and does not block the main content from rendering. The consent banner is injected after the page's primary content is already painted.

**Optimisation tips:**

- Place the SDK script in `<head>` (not at the end of `<body>`) so it starts loading early
- Use the `Consent Initialization` trigger in GTM to fire the SDK before other tags (this is about timing, not render-blocking)
- Avoid synchronous loading (never add `defer` or remove `async` attributes)

### Cumulative Layout Shift (CLS)

**Impact: Low to moderate**

The consent banner injects new UI elements into the page, which can cause layout shift. The impact depends on the banner position:

| Banner position | CLS impact | Recommendation |
|---------------|-----------|----------------|
| **Bottom bar** | Low | Uses `position: fixed` — does not shift page content |
| **Modal/overlay** | None | Overlays the page — no content movement |
| **Top bar** | Moderate | Can push page content down when it appears |

**Optimisation tips:**

- Prefer `position: fixed` banner templates (bottom bar, modal) over top-positioned banners
- If using a top bar, reserve space in your CSS to prevent shift:
  ```css
  /* Reserve space for top consent banner */
  body { padding-top: 80px; }
  ```
- The built-in Waulter templates use `position: fixed` by default

### Interaction to Next Paint (INP)

**Impact: Minimal**

The consent banner's interactive elements (buttons, toggles) are lightweight DOM elements. Click handlers execute quickly (consent decision → data layer push → API call).

**Optimisation tips:**

- No special action needed — the SDK's event handlers are optimised
- If you add custom JavaScript that listens for `Waulter:Decision`, keep your handler lightweight

## Script loading strategies

### Recommended: standard `<script>` tag

```html
<script src="https://cdn.waulter.cz/sdk.js"></script>
```

The SDK handles its own async initialisation. Do **not** add `async` or `defer` attributes — the SDK manages timing internally.

### Via GTM (recommended for most sites)

When loaded via GTM's **Consent Initialization** trigger, the SDK fires early in the page lifecycle — before other tags. GTM handles the loading mechanics.

### What NOT to do

| Approach | Problem |
|----------|---------|
| Loading with `defer` | Delays SDK initialisation, risking Google tags firing before consent signals |
| Loading at end of `<body>` | Same issue — consent signals set too late |
| Blocking the SDK with a consent wall | The SDK **is** the consent mechanism — don't gate it behind another consent tool |

## CDN and caching

The SDK is served from `cdn.waulter.cz` with appropriate cache headers. The CDN ensures:

- **Low latency** — served from edge locations close to visitors
- **High availability** — CDN handles traffic spikes
- **Cache efficiency** — the SDK file is cached by browsers, reducing load on subsequent visits

## Configuration fetch performance

After the SDK script loads, it fetches configuration from the server. This request is:

- **Lightweight** — the configuration payload is small (a few KB)
- **Cached** — configuration is cached server-side for fast retrieval
- **Non-blocking** — the page continues rendering while configuration is fetched

## Measuring performance

To measure Waulter's impact on your site:

1. **Lighthouse** — run a Lighthouse audit with and without Waulter to compare scores
2. **Web Vitals** — use the [web-vitals](https://github.com/GoogleChrome/web-vitals) library to measure LCP, CLS, and INP in production
3. **Chrome DevTools Performance tab** — record a page load and inspect the SDK's activity in the timeline
4. **GTM Preview** — check tag firing order to ensure consent signals are set before analytics tags

!!! tip "Measure in production"
    Lab measurements (Lighthouse) show potential impact, but real-user metrics (CrUX, web-vitals) show actual impact. Monitor real-user performance after deploying Waulter.
