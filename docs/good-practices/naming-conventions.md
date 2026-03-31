---
title: Naming Conventions
description: >
  Recommended naming taxonomy for Waulter configurations, scenarios, and GTM
  variables across DEV and PROD environments.
---

# Naming Conventions

Consistent naming prevents production accidents and makes the Waulter dashboard immediately readable to any team member. Follow these conventions across configurations, scenarios, and GTM elements.

## Configuration names

**Pattern:** `[ENV] Site Name — Region/Details`

| Example | Environment | Purpose |
|---------|-------------|---------|
| `[PROD] Acme Corp — EU` | Production | Live EU configuration |
| `[DEV] Acme Corp — EU` | Development | Testing copy |
| `[PROD] Acme Corp — US` | Production | US-specific configuration |
| `[TEMPLATE] Standard EU Banner` | Template | Master template (never active) |
| `[TEMPLATE v2] Standard EU Banner` | Template | Updated version |

### Rules

- Always start with the environment prefix in square brackets
- Use the company/site name as the primary identifier
- Add region, variant, or detail after a dash
- Keep it short enough to read in the dashboard list view

## Scenario names

**Pattern:** `[ENV] Description — trigger type`

| Example | Purpose |
|---------|---------|
| `[PROD] EU visitors — all pages` | Default production scenario |
| `[DEV] Staging subdomain` | Routes staging traffic to DEV config |
| `[PROD] Campaign — Black Friday` | Campaign-specific scenario |
| `[PROD] Premium users — customField1` | User-type targeting |

## GTM variable names

**Pattern:** `Waulter - [ENV] - Variable Name`

| Example | Type | Purpose |
|---------|------|---------|
| `Waulter - PROD - Configuration ID` | Constant | Production Configuration ID |
| `Waulter - DEV - Configuration ID` | Constant | Development Configuration ID |
| `Waulter - Configuration ID` | Lookup Table | Returns PROD or DEV ID based on hostname |
| `Waulter - Decision` | Data Layer Variable | Reads `decision` from data layer |
| `Waulter - Purposes` | Data Layer Variable | Reads `purposes` array from data layer |
| `Waulter - Web Analysis Allowed` | Custom JavaScript | Checks if analytics purposes are accepted |
| `Waulter - Ads Allowed` | Custom JavaScript | Checks if advertising purposes are accepted |

## GTM tag names

**Pattern:** `[Waulter] ENV — action`

| Example | Purpose |
|---------|---------|
| `[Waulter] PROD — Consent Init` | Production SDK tag |
| `[Waulter] DEV — Consent Init` | Development SDK tag |
| `[Waulter] PROD — Cookie Policy` | appendDocument tag for cookie policy page |

## GTM trigger names

**Pattern:** `Waulter - Trigger Description`

| Example | Event | Purpose |
|---------|-------|---------|
| `Waulter - Consent Decision` | `Waulter:Decision` | Fires on any consent decision |
| `Waulter - Accept All` | `Waulter:Decision` (with condition) | Fires only on "allow" decisions |
| `Waulter - Analytics Allowed` | `Waulter:Decision` (with condition) | Fires when analytics purposes are accepted |
| `Waulter - Ads Allowed` | `Waulter:Decision` (with condition) | Fires when advertising purposes are accepted |

## Why naming matters

| Without conventions | With conventions |
|-------------------|-----------------|
| `Config 1`, `Test config`, `My site` | `[PROD] Acme — EU`, `[DEV] Acme — EU` |
| Impossible to distinguish DEV from PROD at a glance | Immediately clear which is which |
| Risk of testing on production | Environment is obvious from the name |
| Hard for new team members to navigate | Self-documenting naming |

!!! tip "Establish conventions early"
    Set naming conventions before creating your first configuration. Renaming later is possible but disruptive. The earlier you standardise, the more consistent your dashboard will be.

## Agency-specific conventions

For agencies managing multiple clients:

**Pattern:** `[ENV] Client Name — domain.com`

| Example | Purpose |
|---------|---------|
| `[PROD] Client ABC — abc.com` | Client ABC's production config |
| `[DEV] Client ABC — abc.com` | Client ABC's development config |
| `[PROD] Client XYZ — xyz.eu` | Client XYZ's production config |
| `[TEMPLATE] Standard EU` | Agency's master template |

This convention makes the dashboard scannable when managing 50+ client configurations.
