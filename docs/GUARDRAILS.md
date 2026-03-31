# Content Guardrails for waulter-docs

This file defines what MUST NOT appear in this public repository.
It is enforced in PR review and must be read by every contributor.

## Never publish:

### Internal technical architecture
- How the Waulter backend is structured
- Microservice topology or service names
- Database schema or storage formats
- CDN provider or edge infrastructure

### Crawler & detection pipeline internals
- Crawler internal pipeline architecture (modules, stages, processing flow)
- LLM detection or dual-channel system details
- Confidence scoring formulas or thresholds
- Signal extraction internals or heuristics
- CosmosDB, event bus, or other infrastructure details
- APIM routing, function app names, or internal endpoints

### Security implementation details
- Token signing algorithms or key lengths
- Consent receipt cryptographic details
- Authentication mechanisms between SDK and backend (e.g. Auth0 configuration)
- Rate-limiting or abuse-prevention logic

### Internal field names and API internals
- Undocumented SDK properties (those prefixed with `_`)
- Internal GTM template field IDs (e.g. `ccid` vs `id` — refer to them only
  by their user-visible label in the template UI)
- Internal API endpoint paths or base URLs
- EventLog internal schema or data models
- Internal user codebook identifiers (e.g. C020)

### Commercial and operational information
- Pricing, tier limits, or commercial terms
- Internal SLA targets or uptime figures
- Customer-specific configuration details
- Names of specific customers or their config IDs

### Unreleased features
- Features in development or beta must not be documented publicly
  until explicitly approved by Product

## What IS safe to document:

- What the cookie crawler **detects** (observable results visible in the dashboard)
- What the master check **shows** (diff view in the UI — before/after states)
- How scenarios **evaluate** (user-facing logic: rules, operators, ordering)
- Event names and schemas pushed to dataLayer (public SDK contract)
- How sharing and roles work from the user's perspective
- Template workflow (user-facing duplication and batch-update feature)

## Always do:

- Use placeholder Config IDs in all code examples: `YOUR_CONFIG_ID` or `AG0000`
- Use placeholder document IDs: `YOUR_DOC_ID`
- Describe observable behaviour, not implementation
- When in doubt: ask before publishing
