---
title: Scenarios
description: >
  How Waulter scenarios target visitors using rules (URL, referer, pageview, custom fields),
  supported operators, evaluation flow (first-match-wins), and the forceStartCB re-consent flag.
status: stub
---

# Scenarios

Scenarios are **targeting rules** that determine which configuration is loaded for a given visitor context. They enable you to serve different consent experiences based on URL patterns, referrer domains, page views, acceptance state, purposes, and custom fields.

<!-- TODO:
  ## What is a Scenario?
  - A named rule that maps visitor context to a specific configuration
  - Scenarios are attached to a configuration and evaluated on every page load
  - Enable use cases like: different banners per subdomain, re-consent flows, A/B testing

  ## Rule targeting fields
  The following fields are available for scenario rules:
  | Field | Description | Example |
  |-------|-------------|---------|
  | `url` | Current page URL | `https://example.com/pricing` |
  | `referer` | HTTP Referer header | `https://google.com` |
  | `pageview` | Number of page views in session | `3` |
  | `acceptance` | Current consent state | `accepted`, `rejected` |
  | `purposes` | Specific purpose consent state | `analytics:granted` |
  | `customField1`–`customField10` | Custom field values set via SDK | Any string value |

  ## Supported operators
  | Operator | Description | Example |
  |----------|-------------|---------|
  | `equals` | Exact match | `url equals https://example.com` |
  | `not-equals` | Not an exact match | `url not-equals https://example.com` |
  | `contains` | Substring match | `url contains /pricing` |
  | `not-contains` | Does not contain substring | `url not-contains /admin` |
  | `startsWith` | Begins with string | `url startsWith https://shop.` |
  | `endsWith` | Ends with string | `url endsWith /checkout` |
  | `regex` | Regular expression match | `url regex ^https://.*\.example\.com` |
  | `mod` | Modulo operation | `pageview mod 5` |
  | `>` | Greater than | `pageview > 3` |
  | `<` | Less than | `pageview < 10` |
  | `in` | Value in list | `customField1 in [vip,premium]` |

  ## Logical nesting: all / any
  - Rules can be nested using `all` (AND) and `any` (OR) logic
  - Enables complex targeting: "url contains /shop AND (customField1 equals vip OR pageview > 5)"

  ## Evaluation flow
  - Scenarios are evaluated **top-to-bottom** in the order they appear in the dashboard
  - **First match wins** — once a scenario matches, evaluation stops
  - If no scenario matches, the **fallback configuration** is used
  - Order matters: place more specific scenarios above more general ones
-->

<!-- TODO: Add Mermaid diagram showing evaluation flow:
  ```mermaid
  flowchart TD
    A[Page Load] --> B{Scenario 1 matches?}
    B -->|Yes| C[Load Config for Scenario 1]
    B -->|No| D{Scenario 2 matches?}
    D -->|Yes| E[Load Config for Scenario 2]
    D -->|No| F{Scenario N matches?}
    F -->|Yes| G[Load Config for Scenario N]
    F -->|No| H[Load Fallback Config]
  ```
-->

<!-- TODO:
  ## The forceStartCB flag
  - When set on a scenario, forces the consent banner to show even if the visitor has already consented
  - Use case: re-consent flows when purposes change, legal text updates, or periodic consent renewal
  - How to enable: toggle in the scenario settings in the dashboard
  - Visitor experience: banner reappears as if they are a new visitor
  - Previous consent state is preserved until the visitor makes a new decision

  ## Best practices for scenario ordering
  - Place re-consent (forceStartCB) scenarios at the top
  - Place DEV/staging scenarios before PROD scenarios
  - Use the fallback as your default PROD configuration
  - Document your scenario order in your team's runbook
-->
