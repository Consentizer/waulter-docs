---
title: Events & Data Layer
description: >
  All dataLayer events emitted by the Waulter SDK — Waulter:Loaded, Waulter:Open,
  Waulter:Decision, Waulter:Closed, Waulter:Login, Waulter:CustomFieldsUpdated —
  plus event schemas, lifecycle scenarios, and GTM trigger patterns.
---

# Events & Data Layer

The Waulter SDK pushes structured events to `window.dataLayer` at key moments in the consent lifecycle. These events enable you to build GTM triggers, track consent analytics, and integrate with your existing tag management setup.

!!! info "Requires `useGtm: true`"
    Data layer events are only pushed when `useGtm: true` is set in your [WaulterConfig](../implementation/direct/index.md#waulterconfig-reference). DOM CustomEvents fire regardless of this setting.

## Complete event reference

| Event | When it fires | Key data |
|-------|---------------|----------|
| `gtm.init_consent` | SDK starts, before default consent state | — |
| `Waulter:Loaded` | Configuration received from server, SDK ready | `customField1`–`customField10` |
| `Waulter:Open` | Consent banner shown to visitor | `customField1`–`customField10` |
| `Waulter:Decision` | Visitor makes a consent choice | `decision`, `purposes[]`, `customField1`–`customField10` |
| `Waulter:Closed` | Banner closes after decision | `customField1`–`customField10` |
| `Waulter:Login` | Visitor clicks the Waulter login button | `customField1`–`customField10` |
| `Waulter:CustomFieldsUpdated` | `setCustomFields()` called at runtime | `customField1`–`customField10` |

## Event schemas

### Waulter:Decision

The most important event — fired when the visitor makes a consent choice:

```json
{
  "event": "Waulter:Decision",
  "decision": "allow",
  "purposes": ["PU046", "PU050", "PU061"],
  "customField1": "premium",
  "customField2": "en",
  "customField3": "homepage"
}
```

| Field | Type | Values | Description |
|-------|------|--------|-------------|
| `event` | string | `"Waulter:Decision"` | Event name |
| `decision` | string | `"allow"`, `"mixed"`, `"reject"` | The visitor's consent decision |
| `purposes` | string[] | Purpose codes | Array of accepted purpose codes (empty for `"reject"`) |
| `customField1`–`customField10` | string | Any | Custom field values from WaulterConfig |

**Decision values:**

| Decision | Meaning | `purposes` array |
|----------|---------|-----------------|
| `allow` | Visitor accepted all purposes | All purpose codes |
| `mixed` | Visitor customised — some accepted, some rejected | Only the accepted codes |
| `reject` | Visitor rejected all non-essential purposes | Empty `[]` |

### Waulter:Loaded

Fired when the SDK has received configuration from the server and is ready:

```json
{
  "event": "Waulter:Loaded",
  "customField1": "premium",
  "customField2": "en"
}
```

### Waulter:Open / Waulter:Closed

Banner visibility events:

```json
{ "event": "Waulter:Open", "customField1": "premium" }
{ "event": "Waulter:Closed", "customField1": "premium" }
```

### Waulter:CustomFieldsUpdated

Fired when custom fields are updated at runtime via [`setCustomFields()`](../sdk/api-reference.md#setcustomfields):

```json
{
  "event": "Waulter:CustomFieldsUpdated",
  "customField1": "enterprise",
  "customField4": "logged-in"
}
```

## Event lifecycle scenarios

### New visitor (no existing consent)

```
Page Load
  |
  +-- dataLayer.push({ event: "gtm.init_consent" })
  |
  +-- gtag('consent', 'default', { all signals: 'denied' })
  |
  +-- SDK loads configuration from server
  |
  +-- dataLayer.push({ event: "Waulter:Loaded" })
  |
  +-- No existing consent --> banner opens
  |
  +-- dataLayer.push({ event: "Waulter:Open" })
  |
  |   ... visitor interacts with banner ...
  |
  +-- Visitor clicks "Accept All"
  |
  +-- dataLayer.push({
  |     event: "Waulter:Decision",
  |     decision: "allow",
  |     purposes: ["PU046", "PU047", "PU050", "PU061", ...]
  |   })
  |
  +-- gtag('consent', 'update', {
  |     analytics_storage: 'granted',
  |     ad_storage: 'granted', ...
  |   })
  |
  +-- dataLayer.push({ event: "Waulter:Closed" })
```

### Returning visitor (existing consent)

```
Page Load
  |
  +-- dataLayer.push({ event: "gtm.init_consent" })
  |
  +-- gtag('consent', 'default', { all signals: 'denied' })
  |
  +-- SDK loads configuration + existing consent from server
  |
  +-- gtag('consent', 'update', {
  |     analytics_storage: 'granted', ...  // from stored consent
  |   })
  |
  +-- dataLayer.push({
  |     event: "Waulter:Decision",
  |     decision: "allow",
  |     purposes: [... stored purposes ...]
  |   })
  |
  +-- dataLayer.push({ event: "Waulter:Loaded" })
  |
  +-- Banner does NOT open
```

!!! info "Returning visitors still fire `Waulter:Decision`"
    Even when the banner is not shown, the SDK fires `Waulter:Decision` with the stored consent. This ensures GTM triggers fire and consent-dependent tags activate on every page load.

### Reject all

```
  +-- Visitor clicks "Reject All"
  |
  +-- dataLayer.push({
  |     event: "Waulter:Decision",
  |     decision: "reject",
  |     purposes: []
  |   })
  |
  +-- gtag('consent', 'update', { all signals: 'denied' })
  |
  +-- dataLayer.push({ event: "Waulter:Closed" })
```

### Mixed (custom selection)

```
  +-- Visitor selects PU046 and PU050, deselects others
  |
  +-- dataLayer.push({
  |     event: "Waulter:Decision",
  |     decision: "mixed",
  |     purposes: ["PU046", "PU050"]
  |   })
  |
  +-- gtag('consent', 'update', {
  |     analytics_storage: 'granted',
  |     ad_storage: 'denied',
  |     ad_personalization: 'denied', ...
  |   })
  |
  +-- dataLayer.push({ event: "Waulter:Closed" })
```

## Google Consent Mode mapping

### Default consent state

On page load, the SDK sets **all** signals to `denied`:

```javascript
gtag('consent', 'default', {
  ad_personalization: 'denied',
  ad_storage: 'denied',
  ad_user_data: 'denied',
  analytics_storage: 'denied',
  functionality_storage: 'denied',
  personalization_storage: 'denied',
  security_storage: 'denied'
});
```

### Purpose-to-signal mapping

Each purpose maps to one or more Google Consent Mode signals. When a visitor accepts a purpose, the corresponding signals are set to `granted`:

| Purpose Code | Name | GCM Signal |
|-------------|------|-----------|
| PU046 | Web Analytics (Basic) | `analytics_storage` |
| PU047 | Advertising / Marketing | `ad_storage` |
| PU050 | A/B Testing | `analytics_storage` |
| PU061 | Advanced Analytics | `analytics_storage` |
| PU072 | Personalised Advertising | `ad_personalization` |
| PU073 | Remarketing | `ad_personalization` |
| PU074 | Ad Network Measurement | `ad_storage` |

!!! info "Signal aggregation"
    A signal is set to `granted` if **any** accepted purpose maps to it. For example, if the visitor accepts only PU046 (Web Analytics), `analytics_storage` becomes `granted` — even though PU050 and PU061 also map to the same signal.

### Consent update after decision

After the visitor decides, the SDK builds a consent update by:

1. Starting with all signals set to `denied`
2. For each accepted purpose, reading its mapped GCM signal(s)
3. Setting those signals to `granted`

```javascript
// Example: visitor accepted PU046 + PU047
gtag('consent', 'update', {
  ad_personalization: 'denied',
  ad_storage: 'granted',        // PU047
  ad_user_data: 'denied',
  analytics_storage: 'granted',  // PU046
  functionality_storage: 'denied',
  personalization_storage: 'denied',
  security_storage: 'denied'
});
```

## Listening for events in JavaScript

### Using addEventListener

```javascript
// Listen for consent decisions
window.addEventListener('Waulter:Decision', function(event) {
  console.log('Decision:', event.detail.decision);
  console.log('Purposes:', event.detail.purposes);
});

// Listen for SDK ready
window.addEventListener('Waulter:Loaded', function(event) {
  console.log('Waulter CMP loaded');
});

// Legacy event name (still works)
window.addEventListener('cb:decision', function(event) {
  console.log('Decision:', event.detail.decision);
});
```

### Legacy event names

| Current | Legacy |
|---------|--------|
| `Waulter:Loaded` | `cb:loaded` |
| `Waulter:Open` | `cb:open` |
| `Waulter:Decision` | `cb:decision` |
| `Waulter:Closed` | `cb:closed` |
| `Waulter:Login` | `cb:login` |

## Building GTM triggers

### Step 1 — Create data layer variables

In GTM, create the following **Data Layer Variables** (Version 2):

| Variable Name | Data Layer Key |
|---------------|----------------|
| `Waulter_Decision` | `decision` |
| `Waulter_Purposes` | `purposes` |

### Step 2 — Create purpose-checking variables

Create **Custom JavaScript Variables** to check if specific purpose categories are accepted:

**Web analytics allowed?**

```javascript
function() {
  var purposes = {{Waulter_Purposes}};
  if (Array.isArray(purposes)) {
    return purposes.includes("PU061") ||
           purposes.includes("PU046") ||
           purposes.includes("PU050");
  }
  return false;
}
```

**Advertising allowed?**

```javascript
function() {
  var purposes = {{Waulter_Purposes}};
  if (Array.isArray(purposes)) {
    return purposes.includes("PU047") ||
           purposes.includes("PU072") ||
           purposes.includes("PU073") ||
           purposes.includes("PU074");
  }
  return false;
}
```

**A/B testing allowed?**

```javascript
function() {
  var purposes = {{Waulter_Purposes}};
  if (Array.isArray(purposes)) {
    return purposes.includes("PU050");
  }
  return false;
}
```

### Step 3 — Create triggers

Create **Custom Event** triggers in GTM:

| Trigger name | Event name | Condition |
|-------------|-----------|-----------|
| Consent — Accept All | `Waulter:Decision` | `Waulter_Decision` contains `allow` |
| Consent — Analytics Allowed | `Waulter:Decision` | `Web analytics allowed?` equals `true` |
| Consent — Ads Allowed | `Waulter:Decision` | `Advertising allowed?` equals `true` |
| Consent — A/B Testing Allowed | `Waulter:Decision` | `A/B testing allowed?` equals `true` |

### Step 4 — Assign triggers to tags

| Tag | Trigger |
|-----|---------|
| Google Analytics: GA4 Configuration | Consent — Analytics Allowed |
| Google Ads Conversion Tracking | Consent — Ads Allowed |
| Facebook Pixel | Consent — Ads Allowed |
| Optimizely / VWO | Consent — A/B Testing Allowed |
