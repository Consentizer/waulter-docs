---
title: Cookie Detection
description: >
  How Waulter's automated crawler detects cookies, banners, GCM state, and third-party scripts —
  and how the "master check" diff view shows what changed between crawls.
status: stub
---

# Cookie Detection

Waulter includes an automated cookie detection feature that crawls your website to identify cookies, consent banners, Google Consent Mode state, and third-party scripts. Results are displayed in the B2B dashboard so you can verify your compliance posture.

<!-- TODO:
  ## What the crawler detects
  - **Cookies**: first-party and third-party cookies set by your site and its scripts
  - **Banners**: whether a consent banner is present and functional
  - **GCM state**: whether Google Consent Mode signals are being sent correctly
  - **Third-party scripts**: external scripts loaded on your pages (analytics, ads, etc.)

  ## How results appear in the dashboard
  - Detection results are shown per-domain in your configuration view
  - Each detected item shows: name, type, source, category, and compliance status
  - Items are categorised by purpose (analytics, marketing, etc.)
  - Flagged items highlight potential compliance issues
-->

![Cookie detection results](../assets/images/placeholder-cookie-detection.png)
<!-- SCREENSHOT: Replace with actual screenshot of cookie detection results in the dashboard -->

<!-- TODO:
  ## The "master check" — diff view
  - A git-like diff view showing what changed between consecutive crawls
  - Before/after states for each detected item
  - New cookies highlighted in green, removed cookies in red, changed cookies in yellow
  - Helps you spot unexpected changes: new third-party scripts, missing cookies, GCM regressions
  - Useful for verifying that deployments haven't introduced unwanted tracking
-->

![Master check diff view](../assets/images/placeholder-master-check.png)
<!-- SCREENSHOT: Replace with actual screenshot of the master check diff view showing before/after states -->

<!-- TODO:
  ## How to use detection results
  - Review after each deployment to catch new tracking scripts
  - Compare before/after when adding new third-party integrations
  - Use as evidence for compliance audits
  - Share reports with your DPO or legal team

  ## Detection frequency
  - How often crawls run
  - How to trigger a manual re-crawl
  - How long results are retained
-->

!!! warning "Guardrail"
    This documentation covers the **observable results** of cookie detection as they appear
    in the dashboard. Internal details of the detection pipeline, modules, scoring, or
    infrastructure are not documented here.
