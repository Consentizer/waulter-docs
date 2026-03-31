# Waulter External Docs — Claude Code CLI Initialization Prompt

> **How to use**: Paste this entire prompt into a Claude Code CLI session started inside
> `/mnt/c/dev/Waulter-External-Docs` (or wherever your empty repo folder lives).
> The session will scaffold the complete MkDocs project, create all skeleton pages,
> write the brand-tuned `mkdocs.yml`, and produce the repo `README.md`.

---

## PROMPT START — paste everything below this line

---

You are initializing the **Waulter External Documentation** site (`docs.waulter.eu`).
The folder you are working in is currently empty. Your goal is to produce a fully
scaffolded, working MkDocs project that:

1. Is a **clean public-facing** documentation site — no internal technical details,
   no infrastructure, no security implementation internals.
2. Mirrors the structure and MkDocs version from the **internal-docs** repo so the
   two sites stay in sync on tooling. The internal-docs live at
   `/mnt/c/dev/internal-docs` — read its `mkdocs.yml` first to copy the correct
   `mkdocs` version, Python packages, and Material theme version.
3. Has **Waulter brand colours** applied to the Material theme.
4. Contains a full **skeleton of all pages** — each file must have a correct title,
   a one-paragraph stub, and `<!-- TODO: write content -->` markers so writers know
   exactly what to fill in.
5. Includes a `README.md` at repo root that explains the public GitHub repository
   (`https://github.com/waulter/waulter-docs`), how human writers contribute,
   and how **AI agents can consume the docs** as raw Markdown.
6. Is wired for **Azure Static Web Apps (SWA)** deployment — the target hosting
   platform. The subdomain `docs.waulter.eu` **does not exist yet** and must be
   created after SWA provisioning. DNS for `waulter.eu` is managed in
   **Wedos CZ** (`client.wedos.com`). You must produce:
   - `staticwebapp.config.json` at repo root (SWA routing + security headers)
   - `.github/workflows/deploy.yml` using `Azure/static-web-apps-deploy@v1`
     (builds MkDocs, deploys `site/` output, auto-creates PR preview environments)
   - `docs/DEPLOYMENT.md` (ops run-book: Azure Portal provisioning steps,
     how to get the SWA deployment token, and the exact Wedos DNS records —
     CNAME `docs → <n>.azurestaticapps.net` plus TXT ownership validation token)
   This file is **excluded from the public nav** (ops-only), never published as
   a doc page.

Work step by step. After every major step, confirm what you did before moving on.

---

## STEP 0 — Read internal-docs for MkDocs config baseline

```bash
cat /mnt/c/dev/internal-docs/mkdocs.yml
cat /mnt/c/dev/internal-docs/requirements.txt 2>/dev/null || true
cat /mnt/c/dev/internal-docs/.python-version 2>/dev/null || true
```

Extract and note:
- `mkdocs` version pinning
- `mkdocs-material` version
- Any extra plugins (`search`, `mermaid`, `glightbox`, etc.)
- The `theme.palette` block (you will override colours for external docs)
- `site_url`, `repo_url` patterns

---

## STEP 1 — Create project skeleton (bash only, no PowerShell)

```bash
# Confirm working directory is the empty repo
pwd
ls -la

# Create folder tree
mkdir -p docs/{getting-started,implementation/{gtm,direct,platforms},features,sdk,good-practices,accessibility,changelog}
mkdir -p docs/assets/{images,stylesheets}
mkdir -p .github/workflows
```

---

## STEP 2 — Write `mkdocs.yml`

Create `mkdocs.yml` at repo root. Use the version numbers you found in Step 0.
Apply Waulter brand colours (primary: `#0F4C81`, accent: `#00C2B2` — these are
placeholder values; the designer should override them in `docs/assets/stylesheets/waulter.css`).

```yaml
# mkdocs.yml — Waulter External Documentation
# Public site: https://docs.waulter.eu
# GitHub repo: https://github.com/waulter/waulter-docs
# ──────────────────────────────────────────────────────

site_name: Waulter Docs
site_description: >
  Official end-user and developer documentation for Waulter —
  the Consent Management Platform built for modern web.
site_url: https://docs.waulter.eu
site_author: Waulter Team
copyright: "Copyright &copy; 2024 – 2025 Waulter s.r.o."

repo_url: https://github.com/waulter/waulter-docs
repo_name: waulter/waulter-docs
edit_uri: edit/main/docs/

# ── Theme ───────────────────────────────────────────────
theme:
  name: material
  custom_dir: docs/assets/overrides  # optional overrides
  logo: assets/images/waulter-logo-white.svg
  favicon: assets/images/favicon.png
  language: en

  palette:
    # Light mode
    - media: "(prefers-color-scheme: light)"
      scheme: default
      primary: custom          # defined in waulter.css
      accent: custom
      toggle:
        icon: material/weather-sunny
        name: Switch to dark mode
    # Dark mode
    - media: "(prefers-color-scheme: dark)"
      scheme: slate
      primary: custom
      accent: custom
      toggle:
        icon: material/weather-night
        name: Switch to light mode

  features:
    - navigation.tabs
    - navigation.tabs.sticky
    - navigation.sections
    - navigation.expand
    - navigation.path
    - navigation.top
    - navigation.footer
    - toc.follow
    - search.suggest
    - search.highlight
    - search.share
    - content.code.copy
    - content.code.annotate
    - content.tabs.link

# ── Extra CSS / JS ──────────────────────────────────────
extra_css:
  - assets/stylesheets/waulter.css

# ── Extensions ──────────────────────────────────────────
markdown_extensions:
  - admonition
  - pymdownx.details
  - pymdownx.superfences:
      custom_fences:
        - name: mermaid
          class: mermaid
          format: !!python/name:pymdownx.superfences.fence_code_format
  - pymdownx.tabbed:
      alternate_style: true
  - pymdownx.highlight:
      anchor_linenums: true
      line_spans: __span
      pygments_lang_class: true
  - pymdownx.inlinehilite
  - pymdownx.snippets
  - pymdownx.emoji:
      emoji_index: !!python/name:material.extensions.emoji.twemoji
      emoji_generator: !!python/name:material.extensions.emoji.to_svg
  - attr_list
  - md_in_html
  - toc:
      permalink: true
  - tables
  - footnotes
  - def_list

# ── Plugins ─────────────────────────────────────────────
plugins:
  - search:
      lang: en
  - minify:
      minify_html: true
  # Uncomment when mkdocs-git-revision-date-localized-plugin is installed:
  # - git-revision-date-localized:
  #     enable_creation_date: true

# ── Navigation ──────────────────────────────────────────
nav:
  - Home: index.md

  - Getting Started:
    - Overview: getting-started/index.md
    - Quick Start: getting-started/quick-start.md
    - Key Concepts: getting-started/concepts.md

  - Implementation:
    - Overview: implementation/index.md
    - Via Google Tag Manager:
      - GTM Overview: implementation/gtm/index.md
      - Community Template: implementation/gtm/community-template.md
      - Custom HTML Tag: implementation/gtm/custom-html.md
    - Direct Integration:
      - Script Tag: implementation/direct/index.md
      - Async / Deferred Loading: implementation/direct/async-loading.md
    - Platform Guides:
      - WordPress: implementation/platforms/wordpress.md
      - Webflow: implementation/platforms/webflow.md

  - Features:
    - Feature Overview: features/index.md
    - Consent Banner: features/consent-banner.md
    - Purposes: features/purposes.md
    - Scenarios & Domains: features/scenarios.md
    - Google Consent Mode 2.0: features/gcm2.md
    - User Sharing: features/user-sharing.md
    - Policy Documents: features/documents.md
    - Statistics & Reporting: features/statistics.md

  - SDK Reference:
    - SDK Overview: sdk/index.md
    - API Functions: sdk/api-reference.md
    - Data Layer & Events: sdk/data-layer.md
    - Callbacks: sdk/callbacks.md

  - Good Practices:
    - Overview: good-practices/index.md
    - DEV & Testing Workflow: good-practices/dev-testing.md
    - Naming Conventions: good-practices/naming-conventions.md
    - Performance: good-practices/performance.md

  - Accessibility:
    - Overview: accessibility/index.md
    - WCAG Compliance: accessibility/wcag.md

  - Changelog:
    - changelog/index.md

# ── Files intentionally excluded from nav ──────────────
# docs/DEPLOYMENT.md  — Azure SWA + Wedos DNS ops run-book (not a public doc)
# docs/GUARDRAILS.md  — contributor content rules (not a public doc)

# ── Extra variables ─────────────────────────────────────
extra:
  social:
    - icon: fontawesome/brands/github
      link: https://github.com/waulter/waulter-docs
      name: Waulter Docs on GitHub
    - icon: fontawesome/solid/globe
      link: https://waulter.eu
      name: Waulter website
  analytics:
    provider: google              # TODO: add G- measurement ID
    property: G-XXXXXXXXXX
  version:
    provider: mike               # optional: multi-version docs
```

---

## STEP 3 — Write brand CSS

Create `docs/assets/stylesheets/waulter.css`:

```css
/* ═══════════════════════════════════════════════════════════
   Waulter External Docs — Brand Stylesheet
   Primary:  #0F4C81  (deep navy blue)
   Accent:   #00C2B2  (teal / consent-green)
   ───────────────────────────────────────────────────────────
   DESIGNER NOTE: Replace the hex values below to match the
   current Waulter brand guidelines before go-live.
   ═══════════════════════════════════════════════════════════ */

:root {
  --waulter-primary:     #0F4C81;
  --waulter-primary-alt: #0D3D6B;
  --waulter-accent:      #00C2B2;
  --waulter-accent-alt:  #009E90;
  --waulter-text-light:  #FFFFFF;
}

/* Material custom primary */
[data-md-color-primary="custom"] {
  --md-primary-fg-color:        var(--waulter-primary);
  --md-primary-fg-color--light: var(--waulter-primary-alt);
  --md-primary-fg-color--dark:  var(--waulter-primary-alt);
  --md-primary-bg-color:        var(--waulter-text-light);
}

/* Material custom accent */
[data-md-color-accent="custom"] {
  --md-accent-fg-color: var(--waulter-accent);
}

/* Navigation tabs */
.md-tabs {
  background-color: var(--waulter-primary);
}

/* Header */
.md-header {
  background-color: var(--waulter-primary);
}

/* Sidebar active item */
.md-nav__link--active {
  color: var(--waulter-accent) !important;
  font-weight: 600;
}

/* Admonition accent override for "tip" blocks */
.md-typeset .admonition.tip,
.md-typeset details.tip {
  border-color: var(--waulter-accent);
}
.md-typeset .tip > .admonition-title,
.md-typeset .tip > summary {
  background-color: rgba(0, 194, 178, 0.12);
}
.md-typeset .tip > .admonition-title::before,
.md-typeset .tip > summary::before {
  background-color: var(--waulter-accent);
}

/* Inline code */
.md-typeset code {
  background-color: rgba(15, 76, 129, 0.08);
  color: var(--waulter-primary);
}

/* Dark mode overrides */
[data-md-color-scheme="slate"] {
  --md-primary-fg-color:        var(--waulter-primary);
  --md-accent-fg-color:         var(--waulter-accent);
}
[data-md-color-scheme="slate"] .md-typeset code {
  background-color: rgba(0, 194, 178, 0.10);
  color: var(--waulter-accent);
}
```

---

## STEP 4 — Write `requirements.txt`

Copy the package list from internal-docs `requirements.txt` and ensure these are present:

```
mkdocs>=1.6
mkdocs-material>=9.5
mkdocs-minify-plugin>=0.8
pymdown-extensions>=10.0
```

---

## STEP 5 — Write all skeleton Markdown files

For **every file** below, create the file, add the correct H1 title, a one-paragraph
description stub, and `<!-- TODO: write content -->` where body copy goes.
Also add YAML front matter with `description` and `status: stub`.

### `docs/index.md` — Home

```markdown
---
title: Waulter Documentation
description: >
  Official documentation for Waulter — the Consent Management Platform
  that puts privacy-first UX at the centre of your web presence.
hide:
  - navigation
  - toc
---

# Welcome to Waulter Docs

Waulter is a fully managed **Consent Management Platform (CMP)** designed for
modern web teams who need GDPR/ePrivacy compliance without sacrificing user
experience or page performance.

<div class="grid cards" markdown>

-   :material-rocket-launch: **[Get Started](getting-started/index.md)**
    — Deploy Waulter in under 15 minutes.

-   :material-code-tags: **[SDK Reference](sdk/index.md)**
    — Explore every function, event, and data-layer push.

-   :material-star-check: **[Good Practices](good-practices/index.md)**
    — Recommended patterns for DEV/PROD setups and testing.

-   :material-google: **[Google Consent Mode 2.0](features/gcm2.md)**
    — Full GCM v2 implementation guide.

</div>

## Documentation for agents and AI tooling

All pages are available as raw Markdown via the public GitHub repository:

```
https://github.com/waulter/waulter-docs
```

AI agents can pull individual pages directly:
```
https://raw.githubusercontent.com/waulter/waulter-docs/main/docs/<path>.md
```

Or clone the entire repo to provide the docs as context:
```bash
git clone https://github.com/waulter/waulter-docs.git
```
```

---

### `docs/getting-started/index.md`

```markdown
---
title: Getting Started with Waulter
description: An orientation to Waulter CMP — what it does, what you get, and where to begin.
status: stub
---

# Getting Started

Waulter is a hosted Consent Management Platform (CMP) that collects, stores, and
signals user consent across your web properties. It ships as a lightweight JavaScript
SDK that you can deploy through Google Tag Manager or a direct script tag.

<!-- TODO:
  - What Waulter gives you (consent banner, preference centre, consent receipts)
  - Who this documentation is for (developers, tag managers, DPOs)
  - Link to quick-start
-->
```

---

### `docs/getting-started/quick-start.md`

```markdown
---
title: Quick Start — 5 Minutes to Live
description: Deploy Waulter on your website in five minutes using Google Tag Manager.
status: stub
---

# Quick Start

The fastest path to a live consent banner is through **Google Tag Manager**.

<!-- TODO:
  Prerequisites checklist:
    - A Waulter account (link to sign-up)
    - A GTM container on your site
    - Your Waulter Configuration ID (found in the dashboard)

  Step 1 — Add the Waulter GTM Community Template
  Step 2 — Create the Waulter tag
  Step 3 — Configure your Configuration ID
  Step 4 — Publish your container
  Step 5 — Verify consent is working

  Add "What's next?" links to Implementation > GTM > Community Template
-->
```

---

### `docs/getting-started/concepts.md`

```markdown
---
title: Key Concepts
description: >
  Core vocabulary you need to understand before implementing Waulter:
  purposes, scenarios, configurations, and consent signals.
status: stub
---

# Key Concepts

Understanding these terms will help you navigate every other section of this documentation.

<!-- TODO: Define each term clearly for a developer audience:

  ## Configuration
  A named set of settings attached to a domain. Contains purposes, UI text,
  GCM mode settings, and whitelisted domains.

  ## Purpose
  A data-processing activity for which the user can give or deny consent
  (e.g. "Analytics", "Marketing", "Personalisation").
  Waulter purposes map to GCM consent types — document the mapping table here.

  ## Scenario
  A named targeting rule that determines WHICH configuration is loaded for a given
  visitor context (country, subdomain, URL pattern, etc.).

  ## Whitelisted Domains
  Domains that are allowed to load the Waulter SDK. Affects statistics collection
  but does NOT block the banner from rendering on non-whitelisted domains.

  ## Configuration ID
  The unique string that identifies your configuration (e.g. "AG1035").
  Passed as a parameter to the SDK on initialisation.

  ## Consent State
  The current set of granted/denied purposes for a visitor session.

  ## GCM Signal
  The consent signal sent to Google Tag Manager's consent API
  (analytics_storage, ad_storage, etc.)

  ## User Sharing (Cross-Domain Consent)
  The mechanism by which a visitor's consent is transported across your
  domain portfolio without asking again. See: features/user-sharing.md
-->
```

---

### `docs/implementation/index.md`

```markdown
---
title: Implementation Overview
description: >
  Choose the right implementation path for your stack — GTM Community Template,
  Custom HTML, or direct script tag.
status: stub
---

# Implementation

Waulter can be deployed in three ways. Choose the approach that fits your technical
setup.

<!-- TODO:
  ## Comparison table: GTM Template vs Custom HTML vs Direct
  | Approach           | Best for              | Setup time | Flexibility |
  |--------------------|-----------------------|------------|-------------|
  | GTM Community Tmpl | Most teams            | ~5 min     | High        |
  | GTM Custom HTML    | Advanced GTM users    | ~15 min    | Full        |
  | Direct script tag  | Headless / custom CMS | ~30 min    | Full        |

  Add architecture diagram: how SDK initialises, loads config, fires GCM signals.
-->
```

---

### `docs/implementation/gtm/index.md`

```markdown
---
title: Google Tag Manager — Overview
description: Deploy Waulter through Google Tag Manager for the lowest-friction setup.
status: stub
---

# Google Tag Manager

Google Tag Manager (GTM) is the recommended deployment method for most websites.

<!-- TODO:
  - Why GTM (separation of concerns, no code deploy needed)
  - Two approaches within GTM: Community Template vs Custom HTML
  - Prerequisites: GTM container, Waulter account, Config ID
  - Link to sub-pages
-->
```

---

### `docs/implementation/gtm/community-template.md`

```markdown
---
title: GTM Community Template
description: >
  Install the official Waulter GTM Community Template from the Google Tag Manager
  Template Gallery and configure it in minutes.
status: stub
---

# GTM Community Template

The Waulter Community Template is available directly in the GTM Template Gallery.
It abstracts all SDK configuration into a point-and-click interface.

<!-- TODO:
  Step-by-step:
  1. Open GTM > Templates > Search Gallery > "Waulter"
  2. Add to workspace
  3. Create tag, configure fields:
     - Configuration ID (required)
     - GCM Mode (standard / advanced)
     - Wait-for-update timeout
  4. Set trigger to "Consent Initialization - All Pages"
  5. Preview & Publish

  Screenshot placeholders: template gallery search, tag config fields.

  ## Template Fields Reference
  | Field              | Type    | Default | Description                       |
  |--------------------|---------|---------|-----------------------------------|
  | Configuration ID   | string  | —       | Your Waulter config ID (e.g. AG…) |
  | GCM Mode           | select  | Basic   | Basic or Advanced GCM signalling  |
  | Wait-for-update    | number  | 2000    | Ms to wait before firing tags     |

  GUARDRAIL: Do not document internal template field IDs (ccid vs id) here.
-->
```

---

### `docs/implementation/gtm/custom-html.md`

```markdown
---
title: GTM Custom HTML Tag
description: >
  Advanced GTM setup using a Custom HTML tag — full control over SDK initialization,
  async loading patterns, and fallback logic.
status: stub
---

# Custom HTML Tag

Use a GTM Custom HTML tag when you need full control over how and when the
Waulter SDK fires, or when the Community Template does not cover your use case.

<!-- TODO:
  ## Why Custom HTML?
  - Custom timing or loading strategy
  - Integration with existing consent stacks
  - Special trigger requirements

  ## Anatomy of the appendDocument pattern
  Explain the code pattern found in production deployments (document.readyState
  guard + polling loop), WHY it exists (async SDK initialisation), and when it
  fires. Include a clean annotated version of the pattern:

  ```html
  <script>
  (function() {
    // Helper: call appendDocument once SDK is ready
    var callWhenReady = function() {
      if (window.WaulterSDK && typeof window.WaulterSDK.appendDocument === 'function') {
        // SDK already ready at this moment
        window.WaulterSDK.appendDocument('waulterCookies', 'YOUR_DOC_ID');
        return;
      }
      // SDK not yet loaded — poll every 100 ms, give up after 8 s
      var deadline = Date.now() + 8000;
      var interval = setInterval(function() {
        if (window.WaulterSDK && typeof window.WaulterSDK.appendDocument === 'function') {
          clearInterval(interval);
          window.WaulterSDK.appendDocument('waulterCookies', 'YOUR_DOC_ID');
        } else if (Date.now() >= deadline) {
          clearInterval(interval);
          console.warn('[Waulter] Timeout: SDK not ready after 8 s');
        }
      }, 100);
    };

    // Guard for document.readyState
    if (document.readyState === 'loading') {
      document.addEventListener('DOMContentLoaded', callWhenReady);
    } else {
      callWhenReady();   // DOM already interactive or complete
    }
  })();
  </script>
  ```

  ## Why the polling pattern exists
  The Waulter SDK is loaded asynchronously to avoid render-blocking the page.
  There is no guaranteed order between the script that calls `appendDocument` (often
  loaded via GTM, which itself loads after the main HTML) and the SDK loader.
  The polling interval (100 ms) and 8-second timeout are chosen to balance
  responsiveness against graceful degradation.

  ## DOMContentLoaded guard — why it matters
  GTM fires after the HTML has been parsed but some integrations inject Custom HTML
  tags very early in the `<head>`. The `document.readyState` check ensures the DOM
  is available before the SDK tries to attach UI elements.
-->
```

---

### `docs/implementation/direct/index.md`

```markdown
---
title: Direct Script Tag Integration
description: >
  Integrate Waulter directly in your HTML without a tag manager — suitable for
  headless CMS, server-side rendered apps, and fully custom setups.
status: stub
---

# Direct Script Tag

<!-- TODO:
  ## Basic installation
  ```html
  <script
    src="https://sdk.waulter.eu/v1/sdk.js"
    data-waulter-id="YOUR_CONFIG_ID"
    async
  ></script>
  ```

  ## Placement: why it must go in <head>
  Explain GCM2 requirement — consent signals must fire before analytics tags.

  ## Async vs defer
  - async: recommended for most sites
  - defer: use only if you need DOMContentLoaded ordering guarantee
  - Synchronous (no attribute): NEVER recommended — blocks rendering

  ## Self-hosted / CSP environments
  Note that self-hosting is not supported in this version — link to FAQ.
-->
```

---

### `docs/implementation/direct/async-loading.md`

```markdown
---
title: Async Loading & the appendDocument Pattern
description: >
  How Waulter handles asynchronous SDK initialization, the appendDocument
  polling pattern, and how to embed policy documents safely.
status: stub
---

# Async Loading & appendDocument

<!-- TODO: Full explanation cross-linking implementation/gtm/custom-html.md content -->
```

---

### `docs/implementation/platforms/wordpress.md`

```markdown
---
title: WordPress
description: Installing and configuring Waulter CMP on a WordPress site.
status: stub
---

# WordPress

<!-- TODO: Plugin method, GTM method, manual method. Common pitfalls with caching plugins. -->
```

---

### `docs/implementation/platforms/webflow.md`

```markdown
---
title: Webflow
description: Installing Waulter CMP on Webflow projects.
status: stub
---

# Webflow

<!-- TODO: Adding custom code in Webflow head, GTM approach, testing in Webflow preview mode -->
```

---

### `docs/features/index.md`

```markdown
---
title: Feature Overview
description: Everything Waulter can do — consent collection, GCM signalling, user sharing, and more.
status: stub
---

# Features

<!-- TODO: Cards or table linking each feature page with a one-liner description -->
```

---

### `docs/features/consent-banner.md`

```markdown
---
title: Consent Banner
description: How the Waulter consent banner works, what it shows, and how users interact with it.
status: stub
---

# Consent Banner

<!-- TODO:
  - First layer vs second layer (preference centre)
  - Banner positions (bottom bar, modal, corner)
  - Accept all / Reject all / Customise
  - Withdraw consent trigger (floating button)
  - Multi-language behaviour
  - How banner appearance is configured (dashboard, not in code)
-->
```

---

### `docs/features/purposes.md`

```markdown
---
title: Purposes
description: >
  What Waulter purposes are, how they map to GCM consent types, and how to configure
  them in the dashboard.
status: stub
---

# Purposes

Purposes are the individual data-processing activities for which Waulter collects
user consent. Each purpose has a name, description, and one or more GCM consent
type mappings.

<!-- TODO:
  ## Default purpose categories
  | Category       | Typical purposes                  | GCM mapping         |
  |----------------|-----------------------------------|---------------------|
  | Essential       | Security, load balancing          | security_storage    |
  | Analytics       | Traffic analysis, heatmaps        | analytics_storage   |
  | Marketing       | Retargeting, ad measurement       | ad_storage, ad_personalization_storage |
  | Personalisation | UX personalisation                | personalization_storage |
  | Preferences     | Language, region                  | functionality_storage |

  ## Adding custom purposes
  ## Configuring purposes per scenario
  ## Purposes and the consent receipt
-->
```

---

### `docs/features/scenarios.md`

```markdown
---
title: Scenarios & Whitelisted Domains
description: >
  How Waulter scenarios control which configuration loads for a given visitor,
  and how domain whitelisting affects statistics but not banner display.
status: stub
---

# Scenarios & Whitelisted Domains

<!-- TODO:
  ## What is a Scenario?
  A Scenario is a targeting rule: "for visitors from these countries / on these
  subdomains / matching these URL patterns, load THIS configuration."

  ## Creating and ordering scenarios
  Scenarios are evaluated top-to-bottom. The first match wins.

  ## Whitelisted Domains
  ### What whitelisting does
  - Consent events from whitelisted domains are counted in your statistics dashboard.
  ### What whitelisting does NOT do
  - The consent banner is displayed regardless of whether a domain is whitelisted.
  - The SDK still initialises and signals GCM on non-whitelisted domains.
  - Whitelisting is NOT a security mechanism — it is a statistics filter.

  !!! tip "Common confusion"
      Many implementers assume that a domain must be whitelisted before Waulter
      will "work" on it. This is incorrect. The banner shows on any domain that
      loads the SDK with a valid Configuration ID.

  ## Scenarios and DEV/PROD separation
  Link to: good-practices/dev-testing.md
-->
```

---

### `docs/features/gcm2.md`

```markdown
---
title: Google Consent Mode 2.0
description: >
  A complete guide to implementing Google Consent Mode v2 with Waulter — including
  Basic Mode, Advanced Mode, and the required consent types.
status: stub
---

# Google Consent Mode 2.0

Google Consent Mode v2 (GCM2) became mandatory for all Google Ads advertisers
using EU traffic from **March 2024**. Waulter supports both Basic and Advanced
modes out of the box.

<!-- TODO:
  ## GCM2 overview
  - What changed from v1 to v2
  - The 7 consent types: analytics_storage, ad_storage, ad_user_data,
    ad_personalization, functionality_storage, personalization_storage, security_storage
  - New in v2: ad_user_data and ad_personalization

  ## Basic Mode vs Advanced Mode
  | Mode     | Description                                      | Modelling |
  |----------|--------------------------------------------------|-----------|
  | Basic    | No tags fire until consent is given              | No        |
  | Advanced | Tags fire in consent-denied state for modelling  | Yes       |

  ## Configuring GCM mode in Waulter
  - Dashboard setting (Basic / Advanced toggle)
  - GTM template field

  ## Default consent state
  Waulter sets default consent state to `denied` for all non-essential types
  on page load, before the banner has been interacted with.

  ## Consent signal flow diagram (Mermaid)
  ```mermaid
  sequenceDiagram
    participant Browser
    participant GTM
    participant WaulterSDK
    participant Google Tags
    Browser->>GTM: Page loads, GTM fires
    GTM->>WaulterSDK: Waulter tag fires (Consent Init trigger)
    WaulterSDK->>GTM: gtag('consent','default',{...denied...})
    Note over Google Tags: Tags wait in denied state
    Browser->>WaulterSDK: User clicks "Accept All"
    WaulterSDK->>GTM: gtag('consent','update',{...granted...})
    GTM->>Google Tags: Consent updated → tags fire
  ```

  ## Testing GCM2
  Use GTM Preview + Google's Tag Assistant to verify consent signals.
  Link to: good-practices/dev-testing.md
-->
```

---

### `docs/features/user-sharing.md`

```markdown
---
title: User Sharing (Cross-Domain Consent)
description: >
  How Waulter's User Sharing feature carries a visitor's consent across multiple
  domains without prompting them again.
status: stub
---

# User Sharing

User Sharing allows your visitors to consent once and have that decision honoured
across your entire domain portfolio — without being shown the banner again on every site.

<!-- TODO:
  ## How it works
  - Consent is stored against a persistent user identifier (not a third-party cookie)
  - When the user visits Domain B, Waulter checks for an existing consent record
    linked to that identifier
  - If found and within the consent validity period: banner is suppressed
  - If not found or expired: new consent collection begins

  ## Setting up User Sharing
  Step-by-step: enable in dashboard, configure participating domains.

  ## The consent handoff mechanism
  Explain the URL parameter / localStorage approach used.
  DO NOT DISCLOSE: internal cryptographic details, token signing,
  or server-side session architecture.

  ## Privacy considerations
  - Consent decisions are scoped to the user's browser session / identifier
  - The mechanism is GDPR-compliant (no cross-site tracking beyond consent signal)
  - Users can always withdraw consent, which propagates across shared domains

  ## Limitations
  - Requires all participating domains to have Waulter deployed
  - Works within the same Waulter account's domain portfolio
  - Browser privacy modes (Safari ITP, Firefox ETP) may affect persistence

  ## User Sharing and statistics
  Explain how shared consents are counted in the dashboard (which domain
  "owns" the consent event).
-->
```

---

### `docs/features/documents.md`

```markdown
---
title: Policy Documents
description: >
  How Waulter renders legal documents (Cookie Policy, Privacy Policy) inside the
  consent banner or on a standalone page — and how to implement them.
status: stub
---

# Policy Documents

Waulter can render versioned legal documents (Cookie Policy, Privacy Policy, Terms)
directly on your website using the `appendDocument` SDK function.

<!-- TODO:
  ## What appendDocument does
  Injects a managed, versioned document into a DOM element of your choosing.
  The document content is maintained in the Waulter dashboard — no code deploy
  needed when the legal text changes.

  ## Implementation patterns

  ### Pattern 1: Static HTML page
  ```html
  <div id="waulterCookies"></div>
  <script>
    // Assumes WaulterSDK is already initialised
    window.WaulterSDK.appendDocument('waulterCookies', 'YOUR_DOC_ID');
  </script>
  ```

  ### Pattern 2: Via GTM Custom HTML (async-safe)
  Refer to: implementation/gtm/custom-html.md — the full polling pattern.
  Use this when the SDK may not yet be initialised at the time your script runs.

  ### Pattern 3: SPA / React integration
  ```jsx
  useEffect(() => {
    if (window.WaulterSDK?.appendDocument) {
      window.WaulterSDK.appendDocument('waulterCookies', 'YOUR_DOC_ID');
    }
  }, []);
  ```

  ## Document IDs
  Each managed document has a unique ID (e.g. "AG1035"). Find it in the
  Waulter dashboard under Documents.

  ## Why the polling pattern is needed
  The Waulter SDK loads asynchronously. If you call `appendDocument` before the
  SDK script has finished executing, `window.WaulterSDK` will be undefined.
  The polling pattern (100 ms interval, 8-second timeout) solves this reliably.
  See: implementation/gtm/custom-html.md for the full annotated pattern.

  ## Document container element
  The first argument to `appendDocument` is the ID of the HTML element where the
  document will be injected. The element must exist in the DOM when the function fires.

  ## Styling documents
  Documents inherit your site's CSS. Waulter wraps content in a
  `.waulter-document` container class that you can target.
-->
```

---

### `docs/features/statistics.md`

```markdown
---
title: Statistics & Reporting
description: How Waulter tracks consent interactions and what the statistics dashboard shows.
status: stub
---

# Statistics & Reporting

<!-- TODO:
  ## What is tracked
  - Banner impressions
  - Accept All / Reject All / Partial consent interactions
  - Consent withdrawal events
  - Per-purpose consent rates

  ## Whitelisted domains and statistics
  Only events from whitelisted domains appear in the dashboard.
  Non-whitelisted domains still fire GCM signals — they just do not contribute
  to your statistics report. See: features/scenarios.md

  ## Dashboard overview (screenshots placeholder)

  ## Exporting data
-->
```

---

### `docs/sdk/index.md`

```markdown
---
title: SDK Overview
description: The Waulter JavaScript SDK — what it exposes and how to work with it.
status: stub
---

# SDK Overview

The Waulter SDK exposes a global `window.WaulterSDK` object after initialisation.
All public methods are documented in this section.

<!-- TODO:
  ## When is the SDK ready?
  Initialisation is asynchronous. Always guard SDK calls:
  ```js
  if (window.WaulterSDK) {
    // safe to call
  }
  ```
  Or use the polling pattern for non-critical, deferred calls.
  See: implementation/gtm/custom-html.md

  ## Available public methods (summary table)
  | Method                       | Returns  | Description                        |
  |------------------------------|----------|------------------------------------|
  | `appendDocument(el, docId)`  | void     | Render a policy document           |
  | `openPreferences()`          | void     | Open the preference centre         |
  | `getConsentState()`          | object   | Current per-purpose consent object |
  | `onConsentUpdate(callback)`  | void     | Register a consent-change listener |
  | `revokeConsent()`            | void     | Programmatically withdraw consent  |

  Link to full API reference: sdk/api-reference.md
-->
```

---

### `docs/sdk/api-reference.md`

```markdown
---
title: API Reference
description: Complete reference for all public WaulterSDK methods.
status: stub
---

# API Reference

<!-- TODO: One section per method with signature, parameters table, return value,
example, and any gotchas. Do NOT expose internal endpoints, undocumented methods,
or methods prefixed with underscore. -->
```

---

### `docs/sdk/data-layer.md`

```markdown
---
title: Data Layer & Events
description: >
  All dataLayer pushes that Waulter emits — consent granted/denied events,
  initialisation events, and custom GTM triggers you can build on top of them.
status: stub
---

# Data Layer & Events

Waulter pushes structured events to `window.dataLayer` at key consent moments.
You can build GTM triggers on these events to fire tags only when specific consent
conditions are met.

<!-- TODO:
  ## Event: waulter_consent_default
  Fired on every page load before user interaction.
  ```json
  {
    "event": "waulter_consent_default",
    "waulter": {
      "configId": "AG1035",
      "purposes": {
        "analytics": "denied",
        "marketing": "denied"
      }
    }
  }
  ```

  ## Event: waulter_consent_update
  Fired when the user makes or changes a consent decision.
  ```json
  {
    "event": "waulter_consent_update",
    "waulter": {
      "configId": "AG1035",
      "consentType": "accept_all",
      "purposes": {
        "analytics": "granted",
        "marketing": "granted"
      }
    }
  }
  ```

  ## Event: waulter_sdk_ready
  Fired when `window.WaulterSDK` is available and initialised.

  ## Building GTM triggers from events
  Example: fire your analytics tag only when `waulter_consent_update` fires
  AND `waulter.purposes.analytics === 'granted'`.

  ## GTM variables
  Recommended GTM variable setup to read consent state from dataLayer.
-->
```

---

### `docs/sdk/callbacks.md`

```markdown
---
title: Callbacks
description: Subscribe to SDK events programmatically using the WaulterSDK callback API.
status: stub
---

# Callbacks

<!-- TODO: onConsentUpdate, onSdkReady, error handling -->
```

---

### `docs/good-practices/index.md`

```markdown
---
title: Good Practices Overview
description: Recommended patterns that connect multiple Waulter features into reliable workflows.
status: stub
---

# Good Practices

These guides cross-reference multiple Waulter features to show how they work together
in real-world setups. Read these after you understand the individual features.
```

---

### `docs/good-practices/dev-testing.md`

```markdown
---
title: DEV & Testing Workflow
description: >
  How to safely develop and test Waulter before deploying to production — using
  Configuration naming conventions, Scenarios, and whitelisted domains.
status: stub
---

# DEV & Testing Workflow

Testing a CMP before going live requires a disciplined setup. This guide walks you
through the recommended approach used by Waulter's own implementation team.

<!-- TODO:
  ## The DEV / PROD configuration pair

  Create **two** configurations in the Waulter dashboard:
  - `[PROD] My Site` — your live configuration
  - `[DEV] My Site` — an identical copy used only for testing

  The naming convention `[DEV]` and `[PROD]` prefixes (or `DEV_` and `PROD_`)
  makes it impossible to confuse them in the dashboard and in GTM variables.

  !!! warning "Never test on your PROD configuration"
      Testing purposes or GCM mode changes on your production configuration
      creates noise in your consent statistics and can temporarily affect
      live GCM signals.

  ## Scenario-based targeting for DEV traffic

  Create a DEV Scenario that targets:
  - A specific subdomain (e.g. `dev.mysite.com`), OR
  - A URL pattern (e.g. `/staging/`), OR
  - A GTM Preview mode flag

  Map this Scenario to your `[DEV]` configuration so DEV traffic always
  loads DEV config, and PROD traffic always loads PROD config.

  ## Whitelisted domains and testing

  Your DEV domain (e.g. `dev.mysite.com`) should be whitelisted in your DEV
  configuration. This way:
  - DEV consent events appear in your DEV statistics (not polluting PROD stats)
  - You get real data on banner impression and consent rates during testing

  !!! info "Whitelisting does not gate banner display"
      Forgetting to whitelist your DEV domain does NOT prevent the banner from
      showing. It only means DEV sessions won't be counted in statistics.
      See: [Scenarios & Whitelisted Domains](../features/scenarios.md)

  ## GTM Preview mode testing

  1. Open GTM Preview for your container
  2. Navigate to your test site
  3. Verify: Waulter tag fires on Consent Initialization trigger
  4. Verify: `waulter_consent_default` appears in dataLayer
  5. Accept / Reject consent in the banner
  6. Verify: `waulter_consent_update` appears in dataLayer with correct purpose values
  7. Check GTM's Consent State tab to confirm GCM signals

  ## Copying configuration between DEV and PROD

  Once your DEV configuration is finalised:
  1. In the Waulter dashboard, use the **Duplicate Configuration** action
  2. Rename the copy with the `[PROD]` prefix
  3. Update the Configuration ID in your GTM PROD variable
  4. Publish your GTM PROD container

  Never manually re-create PROD from scratch — duplication ensures nothing
  is missed.

  ## Checklist before go-live

  - [ ] PROD configuration has correct legal texts in all active languages
  - [ ] PROD configuration is mapped to a PROD Scenario
  - [ ] PROD domain is whitelisted
  - [ ] GTM PROD container uses PROD Configuration ID
  - [ ] GCM mode is set to the agreed mode (Basic or Advanced)
  - [ ] Consent banner tested in Chrome, Firefox, Safari, mobile
  - [ ] GTM Preview confirms correct dataLayer events
  - [ ] Google Tag Assistant confirms GCM signals
  - [ ] Accessibility check completed (see [Accessibility](../accessibility/index.md))
-->
```

---

### `docs/good-practices/naming-conventions.md`

```markdown
---
title: Naming Conventions
description: >
  Recommended naming taxonomy for Waulter configurations, scenarios, and GTM
  variables across DEV and PROD environments.
status: stub
---

# Naming Conventions

Consistent naming prevents production accidents and makes the Waulter dashboard
immediately readable to any team member.

<!-- TODO:
  ## Configuration names
  Pattern: `[ENV] Site Name — Region (if multi-region)`
  Examples:
  - `[PROD] Acme Corp — EU`
  - `[DEV] Acme Corp — EU`
  - `[PROD] Acme Corp — US (CCPA)`

  ## Scenario names
  Pattern: `[ENV] Description — trigger type`
  Examples:
  - `[PROD] EU visitors — all pages`
  - `[DEV] Staging subdomain`

  ## GTM variable names
  Pattern: `Waulter - [ENV] - Variable Name`
  Examples:
  - `Waulter - PROD - Configuration ID`
  - `Waulter - DEV - Configuration ID`

  ## GTM tag names
  Pattern: `[Waulter] ENV — action`
  Examples:
  - `[Waulter] PROD — Consent Init`
  - `[Waulter] DEV — Consent Init`
-->
```

---

### `docs/good-practices/performance.md`

```markdown
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
```

---

### `docs/accessibility/index.md`

```markdown
---
title: Accessibility
description: >
  How Waulter is built to be accessible to all users, including those using
  assistive technologies.
status: stub
---

# Accessibility

Waulter is designed to meet **WCAG 2.1 Level AA** accessibility requirements.
This section documents the accessibility features of the consent UI and provides
guidance for implementers.

<!-- TODO:
  ## Keyboard navigation
  - Tab order through banner elements
  - Focus trap in modal consent UI
  - Escape key behaviour (closes preference centre)

  ## Screen reader support
  - ARIA roles on banner, dialog, and button elements
  - ARIA-live region for dynamic consent state changes
  - Language attribute on the consent dialog

  ## Colour contrast
  - Waulter UI meets WCAG 2.1 AA contrast ratios (4.5:1 text, 3:1 UI components)
  - Dark mode support

  ## Touch targets
  - All interactive elements meet 44×44 px minimum touch target size

  ## Reduced motion
  - Banner entrance animations respect `prefers-reduced-motion` media query

  ## Implementer responsibilities
  - Ensure the DOM element you use for `appendDocument` is reachable by tab order
  - Do not visually hide the consent withdrawal trigger (floating button)
  - If you use a custom CSS override, verify you have not broken contrast ratios

  ## Testing accessibility
  Recommended tools:
  - [axe DevTools](https://www.deque.com/axe/)
  - [NVDA](https://www.nvaccess.org/) (Windows screen reader)
  - [VoiceOver](https://support.apple.com/guide/voiceover/) (macOS/iOS)
  - Chrome Accessibility Tree (DevTools > Elements > Accessibility pane)
-->
```

---

### `docs/accessibility/wcag.md`

```markdown
---
title: WCAG 2.1 Compliance Statement
description: Waulter's formal WCAG 2.1 Level AA compliance statement.
status: stub
---

# WCAG 2.1 Compliance Statement

<!-- TODO:
  ## Conformance level
  Waulter targets WCAG 2.1 Level AA conformance for all consent UI elements.

  ## Known limitations (if any)
  ## How to report an accessibility issue
  ## Last reviewed date
-->
```

---

### `docs/changelog/index.md`

```markdown
---
title: Changelog
description: Release history for Waulter CMP and SDK.
---

# Changelog

<!-- TODO: Use Keep a Changelog format (https://keepachangelog.com) -->

## Unreleased

## [1.0.0] — YYYY-MM-DD
### Added
- Initial public documentation release
```

---

## STEP 6 — Write `README.md` at repo root

```markdown
# waulter-docs

> **Public GitHub repository for [docs.waulter.eu](https://docs.waulter.eu)**
> — the official end-user and developer documentation for Waulter CMP.

[![Deploy to GitHub Pages](https://github.com/waulter/waulter-docs/actions/workflows/deploy.yml/badge.svg)](https://github.com/waulter/waulter-docs/actions/workflows/deploy.yml)

---

## About

This repository contains the source for the Waulter external documentation site.
It is built with [MkDocs](https://www.mkdocs.org/) and the
[Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) theme.

**Key principle:** this repo contains only *end-user and developer* documentation.
Internal architecture, security implementation details, and infrastructure
information are maintained separately in the internal docs repository.

---

## Local development

### Prerequisites

- Python 3.11+
- pip

### Install dependencies

```bash
pip install -r requirements.txt
```

### Run locally

```bash
mkdocs serve
```

The site will be available at `http://localhost:8000`.

---

## Contributing

1. Fork or branch from `main`
2. Edit or add Markdown files under `docs/`
3. Run `mkdocs serve` locally to preview your changes
4. Open a pull request against `main`

### Writing guidelines

- Write for a developer or technically-minded marketer audience
- Use concrete code examples — never pseudocode when real syntax is available
- Do not disclose internal technical architecture, security implementation,
  or infrastructure details (see Guardrails below)
- Keep pages focused: one concept per page
- Use admonitions (`!!! tip`, `!!! warning`, `!!! info`) for callouts

### Content guardrails

The following topics must **never** appear in this repository:

| Prohibited topic                          | Reason                              |
|-------------------------------------------|-------------------------------------|
| Internal API endpoints / base URLs        | Security & confidentiality          |
| Database schema or data model internals   | Confidentiality                     |
| Infrastructure topology (servers, CDN)    | Security                            |
| Internal GTM field naming (`ccid`, etc.)  | May change; confuses end users      |
| Security mechanisms (token signing, etc.) | Security                            |
| Pricing or commercial terms               | Not appropriate for public docs     |
| Unreleased features                       | No pre-announcements without sign-off |

---

## Deployment

The site deploys automatically to GitHub Pages via GitHub Actions on every push to
`main`. See `.github/workflows/deploy.yml`.

---

## For AI agents

This documentation is available as raw Markdown for AI agent consumption.

### Pull a single page

```
https://raw.githubusercontent.com/waulter/waulter-docs/main/docs/<path>.md
```

Example:
```
https://raw.githubusercontent.com/waulter/waulter-docs/main/docs/features/gcm2.md
```

### Clone the entire documentation corpus

```bash
git clone https://github.com/waulter/waulter-docs.git
```

The `docs/` folder contains all content. The `mkdocs.yml` at the repo root
defines the navigation structure.

### MkDocs navigation map

The canonical navigation tree is in the `nav:` section of `mkdocs.yml`.
Use this to understand how pages relate to each other and to generate
contextually relevant links.

---

## License

Documentation content is © Waulter s.r.o. All rights reserved.
```

---

## STEP 7 — Azure Static Web Apps deployment setup

The site is deployed as an **Azure Static Web Apps (SWA)** resource.
The subdomain `docs.waulter.eu` does not yet exist — it must be created in
Wedos CZ DNS after the SWA resource is provisioned.

### 7a — Create `staticwebapp.config.json` at repo root

Azure SWA needs this file to understand that MkDocs outputs to `site/` and to
configure routing rules (redirect bare `/` correctly, handle 404s).

```json
{
  "navigationFallback": {
    "rewrite": "/404.html",
    "exclude": ["/assets/*", "/*.{css,js,png,svg,ico,json,xml,txt}"]
  },
  "routes": [
    {
      "route": "/",
      "rewrite": "/index.html"
    }
  ],
  "responseOverrides": {
    "404": {
      "rewrite": "/404.html",
      "statusCode": 404
    }
  },
  "globalHeaders": {
    "X-Content-Type-Options": "nosniff",
    "X-Frame-Options": "SAMEORIGIN",
    "Referrer-Policy": "strict-origin-when-cross-origin",
    "Permissions-Policy": "geolocation=(), microphone=(), camera=()"
  },
  "mimeTypes": {
    ".json": "text/json"
  }
}
```

### 7b — Write `.github/workflows/deploy.yml`

This workflow builds MkDocs and deploys to Azure SWA.
`AZURE_STATIC_WEB_APPS_API_TOKEN` is a repository secret set after the SWA
resource is created in the Azure Portal (see Step 7c).

```yaml
name: Deploy to Azure Static Web Apps

on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
  workflow_dispatch:

concurrency:
  group: "azure-swa-${{ github.ref }}"
  cancel-in-progress: true

jobs:
  build_and_deploy:
    if: github.event_name == 'push' || github.event_name == 'workflow_dispatch' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy

    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Install MkDocs dependencies
        run: pip install -r requirements.txt

      - name: Build MkDocs site
        run: mkdocs build --strict
        # Output lands in ./site — this is what SWA deploys

      - name: Deploy to Azure Static Web Apps
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          action: "upload"
          app_location: "/"          # repo root (where staticwebapp.config.json lives)
          output_location: "site"    # MkDocs build output folder
          skip_app_build: true       # we already ran mkdocs build above

  close_pull_request:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Environment
    steps:
      - name: Close staging environment
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          action: "close"
```

> **Pull request preview environments**: Azure SWA automatically creates a temporary
> staging URL for every open PR. This lets you preview documentation changes before
> merging to `main`. The `close_pull_request` job tears down the environment when
> the PR is closed.

### 7c — Azure provisioning instructions (human steps, not automated)

Add these as a `docs/DEPLOYMENT.md` file (excluded from nav, for ops use):

```markdown
# Deployment — Azure Static Web Apps

## One-time Azure setup

1. **Create the SWA resource** in Azure Portal:
   - Resource group: choose or create (e.g. `rg-waulter-docs`)
   - Plan type: Free tier is sufficient for documentation
   - Region: West Europe (closest to waulter.eu)
   - Deployment source: **GitHub** → authorise, select
     `waulter/waulter-docs`, branch `main`
   - Build preset: **Custom**
   - App location: `/`
   - Output location: `site`
   - Click **Review + Create**

2. **Copy the deployment token**:
   - After creation: SWA resource → Manage deployment token → Copy
   - In GitHub repo → Settings → Secrets and variables → Actions
   - Create secret: `AZURE_STATIC_WEB_APPS_API_TOKEN` → paste token

3. **Trigger first deploy**:
   - Push any commit to `main`, or manually run the workflow
   - The site will be live at the default SWA URL:
     `https://<auto-generated-name>.azurestaticapps.net`
   - Verify the site loads correctly before adding the custom domain

## Custom domain: docs.waulter.eu

### Step 1 — Add custom domain in Azure Portal

- SWA resource → Custom domains → Add
- Enter: `docs.waulter.eu`
- Azure will show you a **CNAME validation record** and the **CNAME target**
  (format: `<name>.azurestaticapps.net`)
- Copy both values before moving to Wedos

### Step 2 — Create DNS records in Wedos CZ

Log in to [client.wedos.com](https://client.wedos.com) → DNS Manager →
select `waulter.eu` zone.

Add two records:

| Type  | Name  | Value                              | TTL  |
|-------|-------|------------------------------------|------|
| CNAME | docs  | `<name>.azurestaticapps.net`       | 3600 |
| TXT   | docs  | `<azure-validation-token>`         | 3600 |

> **Wedos note**: Wedos propagates DNS changes within ~15 minutes for their
> own nameservers. Allow up to 1 hour for full propagation before Azure
> validates the domain ownership.

### Step 3 — Complete domain validation in Azure Portal

- Return to SWA → Custom domains → `docs.waulter.eu` → Validate
- Azure automatically provisions a **free managed TLS certificate** (Let's Encrypt)
  — no manual certificate management needed
- Once validated, `https://docs.waulter.eu` is live

### Step 4 — Update mkdocs.yml site_url

After the custom domain is confirmed:
```bash
# In mkdocs.yml, ensure:
site_url: https://docs.waulter.eu
```
Commit and push to trigger a redeploy.

## Environments

| Environment | URL                                             | Branch  |
|-------------|--------------------------------------------------|---------|
| Production  | https://docs.waulter.eu                          | main    |
| PR preview  | Auto-generated by Azure SWA per PR               | PR branch |
| Local dev   | http://localhost:8000 (`mkdocs serve`)            | any     |

## Costs

Azure SWA Free tier includes:
- 100 GB bandwidth/month
- Custom domain + free TLS
- PR preview environments
- Sufficient for a documentation site

No additional Azure cost is expected for this workload.
```

---

## STEP 8 — Write `docs/assets/stylesheets/.gitkeep` and placeholder logo

```bash
touch docs/assets/images/.gitkeep
# Create a placeholder SVG logo so MkDocs doesn't error on missing logo
cat > docs/assets/images/waulter-logo-white.svg << 'EOF'
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 160 40">
  <!-- Placeholder: replace with actual Waulter SVG logo -->
  <text x="0" y="30" font-family="sans-serif" font-size="28"
        font-weight="bold" fill="#ffffff">Waulter</text>
</svg>
EOF
```

---

## STEP 9 — Create `.gitignore`

```
# MkDocs build output (never commit — SWA deploys from CI)
site/

# Python
__pycache__/
*.pyc
.venv/
venv/

# OS
.DS_Store
Thumbs.db

# Azure SWA local emulator (if used)
.swa/
```

---

## STEP 10 — Final verification

```bash
# Check folder tree
find . -not -path './.git/*' -not -path './site/*' | sort

# Validate mkdocs config
mkdocs build --strict 2>&1 | tail -20
```

If `mkdocs build` fails:
- Missing `mkdocs-minify-plugin`: run `pip install mkdocs-minify-plugin`
- Missing `pymdown-extensions`: run `pip install pymdown-extensions`
- Broken nav: check that every file listed in `mkdocs.yml > nav` exists under `docs/`

---

## STEP 11 — Confirm with summary

After all steps complete, output:
1. Full file tree (from `find . -not -path './.git/*' -not -path './site/*' | sort`)
2. Result of `mkdocs build --strict` (must show 0 warnings or document each warning)
3. Confirmation that `mkdocs serve` starts without errors
4. The proposed public GitHub repo name: **`waulter-docs`** under the `waulter` org
   → `https://github.com/waulter/waulter-docs`
5. A **post-scaffold checklist** for Marek to complete manually before the site goes live:

```
POST-SCAFFOLD MANUAL CHECKLIST (not automated — Marek must do these):

Azure:
  [ ] Create Azure Static Web Apps resource in Azure Portal
        Resource group : rg-waulter-docs (or existing)
        Region         : West Europe
        Deployment src : GitHub → waulter/waulter-docs, branch: main
        Build preset   : Custom | App location: / | Output: site
  [ ] Copy the SWA deployment token from Azure Portal
  [ ] Add GitHub repo secret: AZURE_STATIC_WEB_APPS_API_TOKEN
  [ ] Trigger first deploy (push to main or run workflow manually)
  [ ] Confirm the default SWA URL loads: https://<n>.azurestaticapps.net

Custom domain (docs.waulter.eu):
  [ ] In Azure Portal: SWA → Custom domains → Add → enter docs.waulter.eu
  [ ] Note the CNAME target (<n>.azurestaticapps.net) and TXT validation token
  [ ] Log in to client.wedos.com → DNS Manager → waulter.eu zone
  [ ] Add CNAME record: docs  →  <n>.azurestaticapps.net  TTL 3600
  [ ] Add TXT record:  docs  →  <azure-validation-token>   TTL 3600
  [ ] Wait ~15 min for Wedos propagation, then click Validate in Azure Portal
  [ ] Confirm free TLS certificate provisioned automatically
  [ ] Confirm https://docs.waulter.eu loads correctly

GitHub:
  [ ] Create public repo: github.com/waulter/waulter-docs
  [ ] Push this scaffolded folder as the initial commit
  [ ] Verify GitHub Actions workflow runs green on first push

Branding (before public launch):
  [ ] Replace placeholder SVG logo in docs/assets/images/waulter-logo-white.svg
  [ ] Replace colour hex values in docs/assets/stylesheets/waulter.css
  [ ] Add Google Analytics measurement ID in mkdocs.yml (G-XXXXXXXXXX)
```

---

## GUARDRAILS REFERENCE (for all future content authors)

Store this as `docs/GUARDRAILS.md` (excluded from nav, for contributors only):

```markdown
# Content Guardrails for waulter-docs

This file defines what MUST NOT appear in this public repository.
It is enforced in PR review and must be read by every contributor.

## Never publish:

### Internal technical architecture
- How the Waulter backend is structured
- Microservice topology or service names
- Database schema or storage formats
- CDN provider or edge infrastructure

### Security implementation details
- Token signing algorithms or key lengths
- Consent receipt cryptographic details
- Authentication mechanisms between SDK and backend
- Rate-limiting or abuse-prevention logic

### Internal field names and API internals
- Undocumented SDK properties (those prefixed with `_`)
- Internal GTM template field IDs (e.g. `ccid` vs `id` — refer to them only
  by their user-visible label in the template UI)
- Internal API endpoint paths or base URLs

### Commercial and operational information
- Pricing, tier limits, or commercial terms
- Internal SLA targets or uptime figures
- Customer-specific configuration details
- Names of specific customers or their config IDs

### Unreleased features
- Features in development or beta must not be documented publicly
  until explicitly approved by Product

## Always do:

- Use placeholder Config IDs in all code examples: `YOUR_CONFIG_ID` or `AG0000`
- Use placeholder document IDs: `YOUR_DOC_ID`
- Describe observable behaviour, not implementation
- When in doubt: ask before publishing
```

---

## PROMPT END

---

### Notes for Marek

| Decision | Choice | Rationale |
|---|---|---|
| **Public GitHub repo name** | `waulter/waulter-docs` | Short, unambiguous, conventional. Matches `docs.waulter.eu`. Avoids "external" (would imply "internal" exists publicly). |
| **Hosting** | Azure Static Web Apps (Free tier) | Already in Waulter's Azure footprint; free TLS via Let's Encrypt; PR preview environments out of the box; no server management. |
| **MkDocs theme** | Material (same as internal-docs) | Consistent toolchain, same plugin set, no new learning curve. Brand colours via custom CSS only — theme upgrade safe. |
| **Brand colours** | `#0F4C81` primary, `#00C2B2` accent | Placeholder — override in `waulter.css` to match actual brand guide before go-live. |
| **GitHub Actions workflow** | `Azure/static-web-apps-deploy@v1` | SWA deploy action handles staging environments per PR automatically; `skip_app_build: true` keeps build + deploy concerns separate. |
| **DNS: custom domain** | CNAME `docs` → SWA URL in Wedos CZ | Standard CNAME delegation; TXT record for Azure domain ownership validation; Wedos propagates in ~15 min. Azure provisions free TLS automatically. |
| **`staticwebapp.config.json`** | Routing + security headers | MkDocs static output needs explicit 404 fallback; security headers (X-Frame-Options, etc.) improve posture at zero cost. |
| **`docs/DEPLOYMENT.md`** | Step-by-step Azure + Wedos ops guide | Keeps provisioning instructions inside the repo where they stay in sync with the workflow file; excluded from public nav. |
| **appendDocument pattern** | Fully documented in `custom-html.md` and `documents.md` | The polling pattern is the correct approach for async SDK; documenting it prevents implementers from cargo-culting it without understanding it. |
| **Whitelisting/statistics clarification** | Explicit callout box in `scenarios.md` | Single most common implementation misconception — a dedicated tip block prevents support tickets. |
| **Guardrails** | In-repo `GUARDRAILS.md` + README section | Enforced at PR review level; travels with the content. |
| **AI agent consumption** | Raw GitHub URLs + clone instructions in README and `index.md` | Makes the docs directly consumable by MCP tools, Claude Code, and other agents without any API. |
