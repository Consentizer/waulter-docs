# waulter-docs

> **Public GitHub repository for [docs.waulter.eu](https://docs.waulter.eu)**
> — the official end-user and developer documentation for Waulter CMP.

[![Deploy to GitHub Pages](https://github.com/waulter/waulter-docs/actions/workflows/deploy.yml/badge.svg)](https://github.com/waulter/waulter-docs/actions/workflows/deploy.yml)

---

## About

This repository contains the source for the Waulter external documentation site.
It is built with [MkDocs](https://www.mkdocs.org/) and the
[Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) theme.

**Key principle:** this repo contains only *end-user and developer* documentation.
Internal architecture, security implementation details, and infrastructure
information are maintained separately in the internal docs repository.

---

## Local development

### Prerequisites

- Python 3.11+
- pip

### Install dependencies

```bash
pip install -r requirements.txt
```

### Run locally

```bash
mkdocs serve
```

The site will be available at `http://localhost:8000`.

---

## Contributing

1. Fork or branch from `main`
2. Edit or add Markdown files under `docs/`
3. Run `mkdocs serve` locally to preview your changes
4. Open a pull request against `main`

### Writing guidelines

- Write for a developer or technically-minded marketer audience
- Use concrete code examples — never pseudocode when real syntax is available
- Do not disclose internal technical architecture, security implementation,
  or infrastructure details (see Guardrails below)
- Keep pages focused: one concept per page
- Use admonitions (`!!! tip`, `!!! warning`, `!!! info`) for callouts

### Content guardrails

The following topics must **never** appear in this repository:

| Prohibited topic                          | Reason                              |
|-------------------------------------------|-------------------------------------|
| Internal API endpoints / base URLs        | Security & confidentiality          |
| Database schema or data model internals   | Confidentiality                     |
| Infrastructure topology (servers, CDN)    | Security                            |
| Internal GTM field naming (`ccid`, etc.)  | May change; confuses end users      |
| Security mechanisms (token signing, etc.) | Security                            |
| Pricing or commercial terms               | Not appropriate for public docs     |
| Unreleased features                       | No pre-announcements without sign-off |

---

## Deployment

The site deploys automatically to GitHub Pages via GitHub Actions on every push to
`main`. See `.github/workflows/deploy.yml`.

---

## For AI agents

This documentation is available as raw Markdown for AI agent consumption.

### Pull a single page

```
https://raw.githubusercontent.com/waulter/waulter-docs/main/docs/<path>.md
```

Example:
```
https://raw.githubusercontent.com/waulter/waulter-docs/main/docs/features/gcm2.md
```

### Clone the entire documentation corpus

```bash
git clone https://github.com/waulter/waulter-docs.git
```

The `docs/` folder contains all content. The `mkdocs.yml` at the repo root
defines the navigation structure.

### MkDocs navigation map

The canonical navigation tree is in the `nav:` section of `mkdocs.yml`.
Use this to understand how pages relate to each other and to generate
contextually relevant links.

---

## License

Documentation content is © Waulter s.r.o. All rights reserved.
