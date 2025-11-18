# Vault Data Standard Cookbook

This repository houses a community-friendly "cookbook" that translates Autodesk Vault Data Standard documentation into task-based guides.

## Goals

- **Speed up customization.** Provide concise recipes for the most common Vault and CAD scripting hooks (`OnPostCloseDialog`, `OnTabContextChanged`).
- **Highlight PowerShell primitives.** Summarize built-in Data Standard variables so you can bootstrap automation with fewer API calls.
- **Preserve provenance.** Each page links back to the official Autodesk help system via the aggregated [`COMPLETE_DOCUMENTATION.md`](COMPLETE_DOCUMENTATION.md) source file.

## Audience

Vault administrators, CAD automation specialists, and partners who extend Autodesk Vault through Data Standard scripts.

## Contributing

1. Fork the repository and create a topic branch.
2. Edit or add Markdown files under `docs/` to contribute new recipes or references.
3. Run `mkdocs serve` locally to preview changes.
4. Submit a pull request—GitHub Actions will build and deploy the site automatically once your changes are merged.

## Deployment

The site uses [MkDocs](https://www.mkdocs.org/) and GitHub Pages. Every push to `main` builds the docs and publishes them to the `github-pages` environment via GitHub Actions. See `.github/workflows/gh-pages.yml` for details.
