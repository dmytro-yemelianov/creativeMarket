# Contributing to the Cookbook

Thanks for helping expand the Autodesk Vault Programmer's Cookbook. Use this guide to stay consistent across GitHub Pages, GitBook, and any downstream exports.

## 1. Pick the right template

| Scenario | Template |
| --- | --- |
| Single hook, short PowerShell snippet | [Single-hook recipe template](authoring/short-recipe.md) |
| Multi-phase workflow or compliance guide | [Workflow recipe template](authoring/workflow-recipe.md) |

Each template includes the required front matter, section headings, and validation checklist. Copy the template into a new Markdown file under `docs/` and replace the placeholder text.

## 2. Link shared ingredients

- **Variables** – Link to the [PowerShell Pantry](reference/powershell-pantry.md) entry whenever you reference `$dsWindow`, `$Prop[]`, or other shared helpers.
- **Diagnostics** – Point to the [Debugging Playbook](troubleshooting/debugging-playbook.md) for troubleshooting steps instead of duplicating them.
- **Compliance** – Recipes that reference governance workflows should link to the [Four-Eyes checklists](operations/four-eyes-precheck.md).

## 3. Keep navigation tidy

1. Update `mkdocs.yml` with the new page under the appropriate section.
2. Add a short description or quick link to `docs/index.md` if the recipe is a flagship scenario.
3. Test `mkdocs serve` locally to confirm your page renders and navigation order is correct.

## 4. Provide validation details

Every recipe must include:

- A test plan or checklist.
- Rollback instructions (even if the rollback is "remove the script").
- Links to supporting issue templates or logs when available.

## 5. Open a pull request

1. Fork the repo and create a feature branch.
2. Commit docs + navigation changes together.
3. Open a PR summarizing the scenario, testing steps, and any screenshots.
4. Tag reviewers who own the affected Vault lifecycle or Data Standard area.
