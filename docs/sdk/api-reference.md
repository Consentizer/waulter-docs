---
title: API Reference
description: Complete reference for all public WaulterSDK methods.
---

# API Reference

All public methods are available on the `window.WaulterSDK` object after the SDK has initialised. Always check that the SDK is ready before calling any method — see [SDK Overview](index.md#when-is-the-sdk-ready).

---

## openWidget()

Opens the consent banner or preference centre, allowing the visitor to review or change their consent.

**Signature:**

```javascript
window.WaulterSDK.openWidget()
```

**Parameters:** None

**Returns:** `void`

**Example:**

```html
<button onclick="window.WaulterSDK.openWidget()">
  Cookie Settings
</button>
```

```javascript
// With SDK readiness guard
if (window.WaulterSDK) {
  window.WaulterSDK.openWidget();
}
```

!!! tip "GDPR requirement"
    Under GDPR, visitors must be able to withdraw or change their consent at any time. Provide a visible link or button on every page that calls `openWidget()`.

---

## appendDocument()

Renders a managed legal document (Cookie Policy, Privacy Policy) into a DOM element.

**Signature:**

```javascript
window.WaulterSDK.appendDocument(containerId, documentId)
```

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `containerId` | string | Yes | The `id` attribute of the wrapper element where the document will be injected. This is a custom ID you define on your page. |
| `documentId` | string | Yes | The document ID from the Waulter dashboard (e.g. `YOUR_DOC_ID`) |

**Returns:** `void`

**Example:**

```html
<div id="your-element-id"></div>

<script>
  if (window.WaulterSDK) {
    window.WaulterSDK.appendDocument('your-element-id', 'YOUR_DOC_ID');
  }
</script>
```

!!! warning "Async loading"
    The SDK loads asynchronously. If you call `appendDocument` before the SDK is ready, it will fail silently. Use the [polling pattern](../implementation/direct/async-loading.md#the-polling-pattern) for reliable execution.

**Notes:**

- The container element must exist in the DOM when the function is called.
- The document content is fetched from the Waulter server and injected as HTML.
- The rendered document inherits your site's CSS. Waulter wraps content in a `.waulter-document` container class that you can target for styling.
- Document content is managed in the Waulter dashboard — no code deploy is needed when legal text changes.

---

## setCustomFields()

Updates one or more custom fields at runtime. This triggers a `Waulter:CustomFieldsUpdated` data layer event and DOM CustomEvent.

**Signature:**

```javascript
window.WaulterSDK.setCustomFields(fields)
```

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `fields` | object | Yes | An object with `customField1`–`customField10` keys and string values |

**Returns:** `void`

**Example:**

```javascript
window.WaulterSDK.setCustomFields({
  customField1: 'enterprise',
  customField4: 'logged-in'
});
```

**Behaviour:**

- Only the specified fields are updated; unspecified fields remain unchanged.
- The update fires a `Waulter:CustomFieldsUpdated` event on both `window.dataLayer` and as a DOM `CustomEvent`.
- Updated values are included in all subsequent data layer events.
- Updated values are available for scenario rule evaluation on the next page load.

See [Custom Fields](custom-fields.md) for detailed usage patterns.

---

## getCategories()

Returns the current purpose categories with their consent state.

**Signature:**

```javascript
window.WaulterSDK.getCategories()
```

**Parameters:** None

**Returns:** `Array` — an array of category objects, each containing purposes with their consent state.

**Example:**

```javascript
var categories = window.WaulterSDK.getCategories();
console.log(categories);
// [
//   {
//     name: "Analytics",
//     purposes: [
//       { code: "PU046", name: "Web Analytics", technicalPurpose: false },
//       { code: "PU050", name: "A/B Testing", technicalPurpose: false }
//     ]
//   },
//   ...
// ]
```

**Notes:**

- Technical purposes (essential cookies) have `technicalPurpose: true` and are always granted.
- The structure reflects your configuration's purpose categories as defined in the dashboard.

