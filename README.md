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
3. Install dependencies with `pip install -r requirements.txt` and run `mkdocs serve` locally to preview changes.
4. Submit a pull request—GitHub Actions will build and deploy the site automatically once your changes are merged.

## Deployment

The site uses [MkDocs](https://www.mkdocs.org/) and GitHub Pages. A single workflow (`.github/workflows/gh-pages.yml`) handles both pull-request validation and production deployments:

1. **Configure Pages:** In GitHub, go to *Settings → Pages*, select **GitHub Actions** as the source, and (optionally) assign the `github-pages` environment used in the workflow.
2. **Customize metadata:** Update `site_url` and `repo_url` inside `mkdocs.yml` so canonical links point at your GitHub Pages hostname (for example, `https://<github-username>.github.io/creativeMarket/`).
3. **Automatic builds:** Every push to `main` triggers the workflow, installs MkDocs from `requirements.txt`, runs `mkdocs build --strict`, and uploads the generated `site/` directory.
4. **Automatic deploys:** When the build succeeds on `main`, the workflow publishes the uploaded artifact to GitHub Pages via `actions/deploy-pages`. Pull requests only run the build step so documentation regressions are caught before merging.
