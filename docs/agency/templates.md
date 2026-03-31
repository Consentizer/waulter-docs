---
title: Template Configs & Batch Updates
description: >
  Creating template configurations, duplicating per client, and the batch update
  workflow that enables changes across 10/50/100 deployments in minutes.
status: stub
---

# Template Configs & Batch Updates

The template pattern is the most powerful workflow for agencies managing Waulter across multiple client websites. By maintaining a master "template" configuration, you can standardise settings and roll out updates efficiently.

<!-- TODO:
  ## Creating a template configuration
  - Start with a new configuration in the dashboard
  - Name it with a `[TEMPLATE]` prefix: e.g. `[TEMPLATE] Standard EU Banner`
  - Configure your agency's standard settings:
    - Default purposes and their descriptions
    - GCM mode (Basic or Advanced)
    - Banner styling and template choice
    - Default legal texts in all supported languages
  - Keep the template configuration **inactive** — it's a blueprint, not live

  ## Duplicating per client
  - Use the Duplicate action to create a copy for each client
  - Name the copy following naming conventions: `[PROD] Client Name — Region`
  - Customise client-specific settings:
    - Whitelisted domains
    - Client-specific legal texts or branding
    - Client-specific purposes (if different from standard)
  - Activate the client configuration when ready to go live

  ## Naming conventions for templates
  - `[TEMPLATE] Standard EU Banner` — your default EU template
  - `[TEMPLATE] Advanced GCM` — template with Advanced GCM mode
  - `[TEMPLATE] Minimal` — stripped-down template for simple sites
  - See: [Naming Conventions](../good-practices/naming-conventions.md)

  ## Batch update workflow
  When you need to update settings across all clients:
  1. **Update the template** with the new settings (e.g. new purpose, updated text, GCM change)
  2. **Duplicate the updated template** for each affected client
  3. **Review** the new copies — verify client-specific customisations are preserved
  4. **Activate** the new configurations and deactivate the old ones
  5. **Verify** by checking a sample of client sites

  ## How this saves time
  | Number of clients | Manual update time | Template pattern time |
  |-------------------|-------------------|-----------------------|
  | 10 | ~2 hours | ~15 minutes |
  | 50 | ~10 hours | ~45 minutes |
  | 100 | ~20 hours | ~1.5 hours |

  ## Tips for template management
  - Keep a changelog of template changes
  - Version your templates: `[TEMPLATE v2] Standard EU Banner`
  - Test template changes on a single client before rolling out to all
  - Archive old templates rather than deleting them
-->
