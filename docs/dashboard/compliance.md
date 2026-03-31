---
title: Compliance Reports & Notifications
description: What compliance reports show in the Waulter B2B zone, what notifications users receive, and how to act on findings.
---

# Compliance Reports & Notifications

The Waulter dashboard provides compliance reports and notifications to help you maintain GDPR/ePrivacy compliance across your web properties. These reports synthesise information from [cookie detection](cookie-detection.md), consent statistics, and configuration health checks.

## Compliance status overview

Each configuration in the dashboard displays a compliance status indicator:

| Status | Meaning |
|--------|---------|
| **Compliant** | All detected cookies are categorised, consent is being collected, GCM signals are working |
| **Needs attention** | Minor issues found — e.g. uncategorised cookies, missing translations |
| **Non-compliant** | Significant issues — e.g. no consent collection, cookies set before consent, GCM not working |

## What compliance reports show

### Per-configuration health

| Check | What it verifies |
|-------|-----------------|
| **Consent collection** | The SDK is deployed and collecting consent on whitelisted domains |
| **Cookie categorisation** | All detected cookies are assigned to a purpose category |
| **GCM signals** | Google Consent Mode default and update signals are firing correctly |
| **Legal documents** | Cookie Policy and Privacy Policy links are configured and accessible |
| **Purpose coverage** | All detected tracking technologies have a matching purpose |
| **Consent validity** | Consent durations are configured and reasonable |

### Per-purpose breakdown

For each purpose category, the report shows:

- Number of cookies assigned to this purpose
- Consent rate (what percentage of visitors grant this purpose)
- Whether the GCM signal mapping is correct

### Historical trends

- Compliance status over time (weekly/monthly)
- Changes in detected cookies
- Consent rate trends per purpose

## Notifications

The dashboard displays notifications when issues are detected:

| Notification type | Trigger | Urgency |
|------------------|---------|---------|
| **New cookies detected** | Cookie detection finds cookies not in your configuration | Medium |
| **GCM signal missing** | Google Consent Mode signals not detected on a whitelisted domain | High |
| **Configuration inactive** | An active deployment references a deactivated configuration | High |
| **Consent rate drop** | Significant decrease in consent acceptance rate | Low |
| **Detection complete** | A new cookie detection scan has finished | Informational |
| **Document expiring** | A linked legal document is approaching its review date | Medium |

## Interpreting findings

### Uncategorised cookies

**What it means:** Cookies were detected on your site that are not assigned to any purpose category in your Waulter configuration.

**Action:** Review each uncategorised cookie:

1. Identify which script sets it (check the detection results for the source).
2. Determine its purpose (analytics, marketing, functionality, etc.).
3. Assign it to the correct purpose category in the dashboard.
4. If the cookie is unnecessary, remove the script that sets it.

### Cookies set before consent

**What it means:** One or more cookies are being set before the visitor has given consent.

**Action:**

1. Check your tag firing order — ensure all non-essential tags use [consent triggers](../features/events.md#building-gtm-triggers).
2. Verify that the Waulter SDK fires on the **Consent Initialization** trigger (before other tags).
3. Review any inline scripts that set cookies without waiting for consent.

### GCM signals not detected

**What it means:** The detection scan did not find Google Consent Mode default or update signals on your page.

**Action:**

1. Verify `useGtm: true` is set in your WaulterConfig.
2. Check that the SDK loads correctly (no script errors in the browser console).
3. Ensure your GTM container is published with the Waulter tag.

## Acting on findings

### For developers

- Review the [cookie detection diff view](cookie-detection.md#the-master-check--diff-view) after deployments
- Ensure new integrations use consent-dependent triggers
- Verify GCM signals in [GTM Preview mode](../features/gcm2.md#testing-gcm-integration)

### For DPOs / compliance officers

- Review compliance reports regularly (weekly recommended)
- Escalate uncategorised cookies to the development team
- Keep legal documents up to date
- Use consent statistics to demonstrate compliance during audits

### For agency managers

- Monitor compliance status across all client configurations
- Use batch reporting to identify clients that need attention
- Share compliance reports with clients as part of your service

## Re-verifying after fixes

After addressing findings:

1. Make the necessary changes (update configuration, fix tag firing order, etc.).
2. Wait for the next detection scan or trigger a manual re-scan.
3. Check that the findings have been resolved.
4. Verify the compliance status has improved.

!!! tip "Document your remediation"
    Keep a record of compliance findings and the actions you took to resolve them. This documentation is valuable during audits.
