---
title: WordPress
description: Installing and configuring Waulter CMP on a WordPress site.
---

# WordPress

Waulter can be deployed on WordPress using three approaches: via a **GTM plugin** (recommended), using a **header/footer code plugin**, or by editing your theme's `header.php` directly.

## Option 1: Via GTM plugin (recommended)

If your WordPress site uses Google Tag Manager:

1. Install a GTM plugin (e.g. "GTM4WP" or "Google Tag Manager for WordPress").
2. Configure the plugin with your GTM container ID.
3. Follow the [GTM implementation guide](../gtm/index.md) to add Waulter to your GTM container.
4. Manage Waulter entirely from GTM — no WordPress code changes needed.

!!! tip "Why GTM is recommended"
    Using GTM means you can update Waulter settings, change Configuration IDs, and manage consent triggers without touching WordPress. This is especially valuable when multiple plugins manage scripts.

## Option 2: Header/footer code plugin

If you don't use GTM, use a plugin that lets you add code to the `<head>` section:

1. Install a plugin like "Insert Headers and Footers" or "WPCode".
2. Add the following to the **Header** section:

```html
<script>
  window.WaulterConfig = window.WaulterConfig || {};
  Object.assign(window.WaulterConfig, {
    id: "YOUR_CONFIG_ID",
    defaultAllowDuration:  90,
    defaultMixedDuration:  90,
    defaultRejectDuration: 90,
    useGtm: true
  });
</script>
<link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
<script src="https://cdn.waulter.cz/sdk.js"></script>
```

3. Replace `YOUR_CONFIG_ID` with your Configuration ID.
4. Save and verify on the frontend.

## Option 3: Theme `header.php` (advanced)

For direct theme editing:

1. Go to **Appearance** > **Theme File Editor** (or edit via FTP/SSH).
2. Open `header.php`.
3. Add the SDK code inside the `<head>` tag, as early as possible (after `<meta charset>`):

```php
<!-- Waulter CMP SDK -->
<script>
  window.WaulterConfig = window.WaulterConfig || {};
  Object.assign(window.WaulterConfig, {
    id: "YOUR_CONFIG_ID",
    defaultAllowDuration:  90,
    defaultMixedDuration:  90,
    defaultRejectDuration: 90,
    useGtm: true
  });
</script>
<link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
<script src="https://cdn.waulter.cz/sdk.js"></script>
<!-- End Waulter CMP SDK -->
```

!!! warning "Use a child theme"
    If you edit `header.php` directly, use a **child theme** to prevent your changes from being overwritten during theme updates.

## Adding a consent re-open button

Add a "Cookie Settings" link to your footer or privacy page. In your theme or via a widget:

```html
<a href="#" onclick="window.WaulterSDK.openWidget(); return false;">
  Cookie Settings
</a>
```

Or use WordPress's **Custom HTML** widget in a footer widget area.

## Cookie Policy page with appendDocument

To render a managed Cookie Policy document on a WordPress page:

1. Create a new page (e.g. "Cookie Policy").
2. Switch to the **Code Editor** (or use a Custom HTML block).
3. Add a container element and the [polling pattern](../direct/async-loading.md#the-polling-pattern):

```html
<div id="your-element-id"></div>

<script>
(function() {
  var callWhenReady = function() {
    if (window.WaulterSDK && typeof window.WaulterSDK.appendDocument === 'function') {
      window.WaulterSDK.appendDocument('your-element-id', 'YOUR_DOC_ID');
      return;
    }
    var deadline = Date.now() + 8000;
    var interval = setInterval(function() {
      if (window.WaulterSDK && typeof window.WaulterSDK.appendDocument === 'function') {
        clearInterval(interval);
        window.WaulterSDK.appendDocument('your-element-id', 'YOUR_DOC_ID');
      } else if (Date.now() >= deadline) {
        clearInterval(interval);
      }
    }, 100);
  };
  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', callWhenReady);
  } else {
    callWhenReady();
  }
})();
</script>
```

4. Replace `YOUR_DOC_ID` with your document ID from the Waulter dashboard.
5. Link to this page with `?no_waulter_cb` appended to suppress the banner on the cookie policy page.

## WordPress-specific considerations

### Caching plugins

!!! warning "Caching can affect consent"
    WordPress caching plugins (WP Super Cache, W3 Total Cache, WP Rocket, etc.) may cache the page HTML including the consent state. This can cause issues:

    - Visitors may see a cached version where the banner does not appear
    - Consent state may not update properly after a decision

    **Solution:** Exclude the `vaswaulter` cookie from your caching plugin's cache rules. Most caching plugins allow you to specify cookies that bypass the cache.

| Caching plugin | Where to exclude |
|---------------|-----------------|
| WP Rocket | Settings > Advanced > Never Cache Cookies: `vaswaulter` |
| W3 Total Cache | Performance > Page Cache > Rejected Cookies: `vaswaulter` |
| WP Super Cache | Settings > WP Super Cache > Advanced > Rejected Cookies |
| LiteSpeed Cache | Settings > Cache > Do Not Cache Cookies: `vaswaulter` |

### Security plugins

Some security plugins (Wordfence, Sucuri) may block external scripts. If the SDK fails to load:

1. Check if `cdn.waulter.cz` is blocked by your security plugin's firewall.
2. Add `cdn.waulter.cz` to the allowed domains list.
3. Verify `api.waulter.eu` is also allowed (for configuration fetching).

### Content Security Policy (CSP)

If your WordPress site uses a CSP header, add the following:

```
script-src cdn.waulter.cz;
style-src fonts.googleapis.com;
font-src fonts.gstatic.com;
```

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| Banner not appearing | Caching serving old HTML | Clear cache; exclude `vaswaulter` cookie from cache |
| Banner not appearing | Security plugin blocking CDN | Whitelist `cdn.waulter.cz` in your security plugin |
| Banner not appearing | Code added to footer instead of header | Move SDK code to the `<head>` section |
| `appendDocument` not working | WordPress stripping inline scripts | Use a Custom HTML block instead of the visual editor |
| Consent not persisting | Caching plugin serving cached consent state | Exclude `vaswaulter` cookie from cache rules |
