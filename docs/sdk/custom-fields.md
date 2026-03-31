---
title: Custom Fields
description: >
  How to use the 10 custom fields (customField1–10) in the WaulterConfig object,
  update them at runtime, and target them in scenarios.
---

# Custom Fields

Waulter provides **10 custom fields** (`customField1` through `customField10`) that you can use to pass contextual data to the SDK. Custom fields enable advanced [scenario targeting](../dashboard/scenarios.md) and are included in all [data layer events](../features/events.md).

## Setting custom fields at initialisation

Custom fields are set in the `WaulterConfig` object before the SDK loads. Values can come from any JavaScript source:

```javascript
window.WaulterConfig = window.WaulterConfig || {};
Object.assign(window.WaulterConfig, {
  id: "YOUR_CONFIG_ID",
  useGtm: true,

  // From localStorage (with fallback)
  customField1: localStorage.getItem("userTier") || "free",
  customField2: localStorage.getItem("language") || "en",

  // From page context
  customField3: document.querySelector('meta[name="page-type"]')?.content || "default",

  // From JavaScript variables
  customField4: typeof userId !== 'undefined' ? "logged-in" : "anonymous",

  // Static values
  customField5: "campaign-summer"
});
```

### Via GTM template

When using the [Community Template](../implementation/gtm/community-template.md), custom fields are configured directly in the template's field UI. You can use GTM variables:

| Template Field | Value |
|---------------|-------|
| Custom Field 1 | `{{User Tier}}` |
| Custom Field 2 | `{{Page Type}}` |
| Custom Field 3 | `summer-campaign` |

### Via data-* attributes

Custom fields can also be set via `data-*` attributes on the script tag:

```html
<script
  src="https://cdn.waulter.cz/sdk.js"
  data-id="YOUR_CONFIG_ID"
  data-use-gtm="true"
  data-custom-field-1="premium"
  data-custom-field-2="homepage"
></script>
```

## Runtime updates via setCustomFields()

Custom fields can be updated after initialisation using the SDK method:

```javascript
window.WaulterSDK.setCustomFields({
  customField1: 'enterprise',
  customField4: 'logged-in'
});
```

**Behaviour:**

- Only the specified fields are updated; others remain unchanged.
- Fires a `Waulter:CustomFieldsUpdated` data layer event and DOM CustomEvent.
- Updated values are included in all subsequent `Waulter:*` events.
- The new values take effect for scenario evaluation on the next page load (scenarios are evaluated server-side during SDK initialisation).

**Example — update after login:**

```javascript
document.getElementById('loginForm').addEventListener('submit', function() {
  // After successful login, update custom fields
  window.WaulterSDK.setCustomFields({
    customField1: 'premium',
    customField4: 'logged-in'
  });
});
```

## Use in scenario targeting

Custom fields are available as targeting variables in [scenario rules](../dashboard/scenarios.md). This enables dynamic consent experiences based on visitor context.

**Common patterns:**

| Scenario rule | What it does |
|---------------|-------------|
| `customField1 equals "premium"` | Show a premium-branded banner to paying users |
| `customField2 contains "/shop"` | Use a shop-specific configuration for e-commerce pages |
| `customField3 equals "campaign-summer"` | Load a campaign-specific consent flow |
| `customField4 equals "logged-in"` | Skip re-consent for authenticated users |

All [scenario operators](../dashboard/scenarios.md#supported-operators) work with custom fields: `equals`, `not-equals`, `contains`, `startsWith`, `endsWith`, `regex`, `>`, `<`, `mod`, `in`.

## Inclusion in data layer events

Custom field values are included in **all** `Waulter:*` data layer events:

```javascript
// Waulter:Decision event includes custom fields
{
  event: "Waulter:Decision",
  decision: "allow",
  purposes: ["PU046", "PU050", "PU061"],
  customField1: "premium",
  customField2: "en",
  customField3: "homepage",
  customField4: "logged-in",
  customField5: "campaign-summer"
}
```

This allows you to:

- **Segment consent analytics in GTM** — create variables and triggers that read custom field values
- **Build custom reports** — correlate consent decisions with user tier, page type, or campaign
- **Debug targeting** — verify which custom field values are being sent

## Common use cases

| Field | Use case | Example values |
|-------|----------|---------------|
| `customField1` | User tier / subscription level | `free`, `premium`, `enterprise` |
| `customField2` | Language or locale | `cs`, `en`, `de` |
| `customField3` | Page type or section | `homepage`, `checkout`, `blog`, `product` |
| `customField4` | Authentication state | `logged-in`, `anonymous` |
| `customField5` | Campaign or A/B variant | `campaign-summer`, `control`, `variant-a` |
| `customField6` | Region or market | `cz`, `sk`, `eu` |
| `customField7` | Content category | `news`, `sport`, `entertainment` |
| `customField8` | Referral source | `google`, `facebook`, `direct` |
| `customField9` | Device type | `desktop`, `mobile`, `tablet` |
| `customField10` | Reserved for future use | — |

!!! tip "Use meaningful fallback values"
    Always provide a fallback value (e.g. `"NA"` or `"default"`) when reading from dynamic sources like `localStorage` or DOM elements. This prevents `undefined` or `null` values from reaching scenario rules.
