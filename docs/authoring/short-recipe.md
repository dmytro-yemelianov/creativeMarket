---
title: Single-hook recipe template
description: Boilerplate for short Data Standard recipes (20 lines of script or less).
tags:
  - template
  - recipe
  - data standard
  - powershell
---

# Single-hook recipe template

Use this template whenever you document a focused snippet—think `OnPostCloseDialog` validations or context-aware tab tweaks. Paste the front matter, then fill in each section below.

## Front matter checklist

- `title` – Match the cookbook navigation label.
- `description` – One sentence that summarizes the scenario and outcome.
- `tags` – 3–5 search-friendly keywords (`OnPostCloseDialog`, `validation`, `dialog`).

## Ingredients

List the shared variables, helpers, and UI components the reader needs. Link each entry to the [PowerShell Pantry](../reference/powershell-pantry.md).

- `$dsWindow`
- `$Prop[]`
- UI control names (`TemplateDropDown`)

## Steps

1. **Prepare the context** – Note where the function lives (Vault tab, CAD dialog, etc.).
2. **Add the script** – Provide the actual PowerShell snippet. Keep it under 20 lines.
3. **Validate** – Explain how to test the change inside Vault or CAD.

```powershell
function OnPostCloseDialog {
    param($dsParam)
    # your focused logic goes here
}
```

## Validation / next actions

- [ ] Script saved to the correct `Default.ps1` file.
- [ ] Dialog reloaded and verified locally.
- [ ] Troubleshooting tips linked (e.g., [Debugging Playbook](../troubleshooting/debugging-playbook.md)).

## Sharing callouts

Add optional callouts at the end for:

- Version-specific notes.
- Links to related recipes.
- Screenshots or diagrams that show the final UI.
