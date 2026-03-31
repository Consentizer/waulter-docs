---
title: GTM Scaffold (Pre-Built Container)
description: >
  Import the Waulter GTM Scaffold — a pre-built container with 79 variables,
  17 triggers, and 2 tags for complete consent-based tag control.
---

# GTM Scaffold

The Waulter GTM Scaffold is a **pre-built GTM container export** (JSON file) that you import into your workspace. It contains all the variables, triggers, and tags needed to control tag firing at a granular, purpose-level — going beyond what Google Consent Mode signals alone provide.

!!! tip "Template + Scaffold = complete setup"
    The scaffold is a companion to the [Community Template](community-template.md). Import both for the fastest path to a fully wired Waulter GTM integration.

## Why use the scaffold?

Google Consent Mode signals are **aggregated** — multiple Waulter purposes map to the same signal (e.g. PU046, PU050, and PU061 all map to `analytics_storage`). This means a tag gated only by `analytics_storage` will fire if **any** analytics purpose is accepted, even if the visitor rejected the specific purpose that tag requires.

The scaffold provides **purpose-level** and **category-level** variables and triggers that let you control tags with precision:

| Control level | Example | Provided by |
|--------------|---------|-------------|
| **GCM signal** | `analytics_storage: granted` | Google Consent Mode (built-in) |
| **Category** | "Analytics & Optimization" category granted | Scaffold variables + triggers |
| **Purpose** | PU050 (A/B Testing) specifically granted | Scaffold variables |

See [Why purpose-level control matters](custom-html.md#why-purpose-level-control-matters) for a detailed explanation of the compliance risk.

## Download and import

### Step 1 — Download the scaffold

Download `waulter_gtm_scaffold.json` from the [Waulter GTM GitHub repository](https://github.com/Consentizer/Waulter-CMP-Google-Tag-Manager).

### Step 2 — Import into GTM

1. In GTM, go to **Admin** > **Import Container**.
2. Click **Choose container file** and select the downloaded JSON.
3. Select the target workspace (usually **Default Workspace**).
4. Choose **Merge** > **Rename conflicting tags, triggers, and variables**.
5. Review the import summary and click **Confirm**.

!!! warning "Use Merge, not Overwrite"
    Always choose **Merge** when importing. **Overwrite** will replace your entire container, deleting all existing tags, triggers, and variables.

### Step 3 — Review and test

1. After import, review the imported items under Tags, Triggers, and Variables.
2. Click **Preview** to test.
3. Verify variables and triggers respond correctly to consent decisions.

## What's included

The scaffold contains **79 variables**, **17 triggers**, and **2 tags** covering 50 purposes across 9 categories.

### Tags (2)

| Tag | Purpose |
|-----|---------|
| **Waulter - Consent State Sync** | Writes consent state to `localStorage` and cookie after each decision. Fires on page load and on every `Waulter:Decision` event. |
| **Waulter - Custom Fields Loader** | Reads custom field values (`waulter_cf1`–`waulter_cf10`) from `localStorage` / cookies and pushes them to the data layer. Fires on Consent Initialization (before the SDK tag). |

### Triggers (17)

Triggers come in **grant/exception pairs** for each category, plus utility triggers.

#### Category-level grant triggers

Fire when the visitor accepts **all purposes** within a category.

| Trigger | Fires when |
|---------|-----------|
| **Waulter - All Consent Granted** | Visitor accepts all purposes (`decision: "allow"`) |
| **Waulter - [PC002] Analytics & Optimization Category Granted** | All analytics purposes accepted |
| **Waulter - [PC003] Website Functionality Category Granted** | All functionality purposes accepted |
| **Waulter - [PC006] Other & Advanced Tracking Category Granted** | All advanced tracking purposes accepted |
| **Waulter - [PC008] Marketing Category Granted** | All marketing purposes accepted |
| **Waulter - AB Testing Granted** | A/B testing purpose (PU050) specifically accepted |

#### Exception triggers

Use as **exception triggers** on tags to **block** them when consent is missing. Attach these to tags as blocking triggers.

| Trigger | Blocks when |
|---------|-----------|
| **Waulter - All Consent Exception** | Not all purposes accepted |
| **Waulter - [PC002] Analytics & Optimization Category Exception** | Analytics category not fully accepted |
| **Waulter - [PC003] Website Functionality Category Exception** | Functionality category not fully accepted |
| **Waulter - [PC006] Other & Advanced Tracking Category Exception** | Advanced tracking category not fully accepted |
| **Waulter - [PC008] Marketing Category Exception** | Marketing category not fully accepted |
| **Waulter - AB Testing Exception** | A/B testing purpose not accepted |
| **Waulter - Marketing Services Exception v1** | Marketing services subset not accepted |
| **Waulter - Marketing Services Exception v2** | Marketing services subset not accepted (alternate grouping) |

#### Utility triggers

| Trigger | Purpose |
|---------|---------|
| **Waulter - Decision Changed** | Fires on every `Waulter:Decision` event (use for custom logic) |
| **Waulter - No Consent (Block GA4)** | Blocks GA4 when no analytics consent given |
| **Waulter - Page Load (All Pages)** | Standard all-pages trigger for scaffolded tags |

### Variables (79)

Variables are organised in a **two-level hierarchy**: category-level (broad checks) and purpose-level (granular checks).

#### Core variables

| Variable | Type | Description |
|----------|------|-------------|
| **Waulter - Decision** | Data Layer | The visitor's consent decision (`"allow"`, `"mixed"`, `"reject"`) |
| **Waulter - Purposes** | Data Layer | Array of accepted purpose codes (e.g. `["PU046", "PU050"]`) |
| **Waulter - Custom Field 1–10** | Data Layer | Custom field values from the data layer |

#### Category-level variables

Each returns `true` / `false` indicating whether all purposes in that category are accepted.

| Variable | Category |
|----------|----------|
| **Waulter - [PC001] Cookie Management Category Allowed** | Cookie Management |
| **Waulter - [PC002] Analytics & Optimization Category Allowed** | Analytics & Optimization |
| **Waulter - [PC003] Website Functionality Category Allowed** | Website Functionality |
| **Waulter - [PC004] Website Security Category Allowed** | Website Security |
| **Waulter - [PC005] Service Provision Category Allowed** | Service Provision |
| **Waulter - [PC006] Other & Advanced Tracking Category Allowed** | Other & Advanced Tracking |
| **Waulter - [PC007] Website Monitoring Category Allowed** | Website Monitoring |
| **Waulter - [PC008] Marketing Category Allowed** | Marketing |
| **Waulter - [PC009] Customer Records CRM Category Allowed** | Customer Records / CRM |

#### Purpose-level variables

Each returns `true` / `false` indicating whether that specific purpose is in the accepted purposes array. The scaffold includes variables for **50 purposes** across all 9 categories.

**Analytics & Optimization (PC002):**

| Variable | Purpose |
|----------|---------|
| Waulter - Web Analytics (PU046) | Basic web analytics |
| Waulter - Traffic Source Analysis (PU047) | Traffic source tracking |
| Waulter - System & Device Analysis (PU048) | Device analytics |
| Waulter - Individual Behavior Analysis (PU045) | Behavioral analytics |
| Waulter - AB Testing (PU050) | A/B testing |
| Waulter - Conversion Tracking (PU051) | Conversion measurement |
| Waulter - Payment Analytics (PU052) | Payment analytics |

**Marketing (PC008):**

| Variable | Purpose |
|----------|---------|
| Waulter - Social Media Interaction (PU055) | Social media |
| Waulter - User Behavior Tracking (PU057) | Behavioral tracking |
| Waulter - Display Advertising (PU058) | Display ads |
| Waulter - Personalized Advertising (PU059) | Ad personalisation |
| Waulter - Ad Campaign Measurement (PU061) | Campaign measurement |
| Waulter - Offer Optimization (PU063) | Offer optimisation |
| Waulter - Social Media Monitoring (PU064) | Social monitoring |
| Waulter - Phone Marketing (PU065) | Phone marketing |
| Waulter - Affiliate Marketing (PU066) | Affiliate tracking |
| Waulter - Cross-Site Tracking (PU067) | Cross-site tracking |
| Waulter - External Ad Display (PU071) | External ads |
| Waulter - Retargeting (PU072) | Retargeting |
| Waulter - Remarketing (PU073) | Remarketing |
| Waulter - Email Marketing (PU074) | Email marketing |

**Website Functionality (PC003):**

| Variable | Purpose |
|----------|---------|
| Waulter - Shopping Cart Memory (PU025) | Cart persistence |
| Waulter - Form Simplification (PU026) | Form autofill |
| Waulter - Function Preferences (PU027) | User preferences |
| Waulter - Content Personalization (PU028) | Content personalisation |
| Waulter - Login Credentials Memory (PU029) | Login memory |
| Waulter - Registration & Login (PU031) | Auth cookies |
| Waulter - Third-Party Login (PU032) | Social login |
| Waulter - YouTube Video Playback (PU034) | YouTube embeds |
| Waulter - Payment Method Storage (PU035) | Payment methods |
| Waulter - Payment Security (PU036) | Payment security |
| Waulter - Automation Platforms (PU037) | Marketing automation |
| Waulter - Online Chat (PU038) | Chat widgets |
| Waulter - Product Comparison (PU075) | Product comparison |
| Waulter - Communication & Forms (PU095) | Forms & comms |
| Waulter - Content Display & Delivery (PU097) | Content delivery |
| Waulter - Payment Processing (PU098) | Payment processing |
| Waulter - Advanced Search & Personalization (PU100) | Search |
| Waulter - Feedback & Experience Analysis (PU101) | Feedback tools |

**Other categories** (Cookie Management, Security, Monitoring, Service Provision, CRM) include purpose variables for PU016, PU023, PU024, PU096, PU081, PU039, PU040, PU041, PU042, PU043, PU044, PU068, PU054, PU076, PU069, PU070, PU099, PU094, PU102.

!!! info "Your configuration uses a subset"
    Your specific Waulter configuration likely uses only a subset of these 50 purposes. The scaffold includes all possible purposes so it works with any configuration. Unused variables have no performance impact.

## How to use triggers and variables

### Example 1: Fire a tag only for specific purposes

To fire a Hotjar tag **only** when Heatmaps & Recordings (PU054) is accepted:

1. Create a trigger: **Custom Event** > Event name: `Waulter:Decision`
2. Add a condition: `Waulter - Heatmaps & Recordings (PU054)` equals `true`
3. Assign this trigger to your Hotjar tag

### Example 2: Use exception triggers to block tags

To block a Facebook Pixel tag when marketing consent is missing:

1. Set the Facebook Pixel's firing trigger to **All Pages** or `Waulter:Decision`
2. Add **Waulter - [PC008] Marketing Category Exception** as a **blocking trigger**
3. The tag will only fire when the marketing category is fully accepted

### Example 3: Category-level gating for GA4

To fire GA4 only when the Analytics & Optimization category is accepted:

1. Use **Waulter - [PC002] Analytics & Optimization Category Granted** as the firing trigger
2. Add **Waulter - [PC002] Analytics & Optimization Category Exception** as the blocking trigger
3. GA4 fires only when all analytics purposes are accepted — not just when any single analytics purpose triggers `analytics_storage: granted`

## Scaffold events

The scaffold tags push two additional events to the data layer:

| Event | When | Data |
|-------|------|------|
| `Waulter:CustomFieldsSet` | Consent Initialization (before SDK) | Custom field values read from `localStorage` / cookies |
| `Waulter:ConsentStateSynced` | After page load and after each decision | `waulter_consent_status` (`"pending"` / `"decided"`), `waulter_consent_decision` |

## Localisation reference

If your GTM container uses Czech or Slovak naming conventions from a legacy setup, here is a mapping:

| Scaffold name (English) | Legacy Czech equivalent |
|------------------------|------------------------|
| `Waulter - All Consent Granted` | `Consent_Decision_Allow` |
| `Waulter - [PC002] Analytics & Optimization Category Allowed` | `Waulter - web analysis allowed?` |
| `Waulter - [PC008] Marketing Category Allowed` | `Waulter - Ads network allowed?` |
| `Waulter - AB Testing (PU050)` | `Waulter - AB testing allowed?` |
| `Waulter - Decision` | `Waulter_Decision` |
| `Waulter - Purposes` | `Waulter_Purposes` |

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| Variables show `undefined` after import | Data layer not populated yet | Variables populate after `Waulter:Decision` fires — check in GTM Preview after a consent interaction |
| Category variable returns `false` unexpectedly | Not all purposes in the category are accepted | Category variables require **all** purposes in the category to be accepted. Check the Purposes array. |
| Exception trigger not blocking a tag | Trigger not added as blocking | Exception triggers must be added as **blocking triggers** (the red minus icon), not firing triggers |
| Scaffold events not appearing | Scaffold tags not firing | Verify the Consent State Sync and Custom Fields Loader tags fire on the correct triggers |
