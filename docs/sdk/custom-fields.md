---
title: Custom Fields
description: >
  How to use the 10 custom fields (customField1–10) in the WaulterConfig object,
  update them at runtime, and target them in scenarios.
status: stub
---

# Custom Fields

Waulter provides **10 custom fields** (`customField1` through `customField10`) that you can use to pass contextual data to the SDK. Custom fields enable advanced scenario targeting and are included in all dataLayer events.

<!-- TODO:
  ## Setting custom fields in WaulterConfig
  Custom fields can be set during SDK initialisation via the configuration object:
  ```js
  window.WaulterConfig = {
    configId: 'YOUR_CONFIG_ID',
    customField1: 'premium',
    customField2: 'en-GB',
    customField3: 'campaign-summer-2024'
  };
  ```

  ## Runtime updates via setCustomFields()
  Custom fields can be updated after initialisation using the SDK method:
  ```js
  window.WaulterSDK.setCustomFields({
    customField1: 'enterprise',
    customField4: 'logged-in'
  });
  ```
  - Only the specified fields are updated; others remain unchanged
  - Fires a `Waulter:CustomFieldsUpdated` dataLayer event

  ## Use in scenario targeting
  - Custom fields are available as targeting rules in scenarios
  - Example: show a different banner to `customField1 equals premium` visitors
  - All operators (equals, contains, regex, etc.) work with custom fields
  - See: [Scenarios](../dashboard/scenarios.md)

  ## Inclusion in dataLayer events
  - Custom field values are included in all `Waulter:*` dataLayer events
  - This allows GTM triggers and variables to read custom field state
  - Useful for segmenting consent analytics by user type, tier, or campaign

  ## Use cases
  | Field | Example use | Value |
  |-------|-------------|-------|
  | `customField1` | User tier | `free`, `premium`, `enterprise` |
  | `customField2` | Page type | `homepage`, `checkout`, `blog` |
  | `customField3` | Campaign | `summer-2024`, `black-friday` |
  | `customField4` | Login state | `logged-in`, `anonymous` |
  | `customField5` | A/B test variant | `control`, `variant-a` |
-->
