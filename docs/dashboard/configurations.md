---
title: Configurations
description: What a Waulter Configuration is, active vs inactive states, the "Vyzkoušet" (Try it) preview button, and Configuration IDs.
status: stub
---

# Configurations

A **Configuration** is the core unit in Waulter — it defines everything about how consent is collected on a specific website or domain: which purposes are presented, what the banner looks like, which languages are supported, and how GCM signals are sent.

<!-- TODO:
  ## What is a Configuration?
  - A named set of settings attached to one or more domains
  - Contains: purposes, UI text/translations, GCM mode, whitelisted domains, styling
  - Each configuration has a unique Configuration ID (e.g. "AG1035")

  ## Configuration ID
  - The unique string identifier passed to the SDK on initialisation
  - Found in the dashboard configuration detail view
  - Used in GTM template fields or direct script tag `data-waulter-id` attribute

  ## Active vs Inactive states
  - Visual signaling with badges in the dashboard list view
  - Active: configuration is live and serving consent banners
  - Inactive: configuration exists but is not serving
  - How to activate/deactivate a configuration
-->

![Configuration active/inactive states](../assets/images/placeholder-config-states.png)
<!-- SCREENSHOT: Replace with actual screenshot of the B2B dashboard showing active vs inactive configs with badge indicators -->

<!-- TODO:
  ## The "Vyzkoušet" (Try it) button
  - What it does: opens a preview of your consent banner as end-users will see it
  - How to use it: click the button from the configuration detail page
  - Why it matters: test banner appearance, text, and behaviour before going live
  - Does NOT affect live visitors or statistics
  - Works even on inactive configurations
-->

![Vyzkoušet preview button](../assets/images/placeholder-vyzkouset-button.png)
<!-- SCREENSHOT: Replace with actual screenshot showing the Vyzkoušet button and the preview it opens -->
