---
title: Four-Eyes Precheck
description: Automate the validation that must occur before a design enters a controlled lifecycle state.
tags:
  - governance
  - lifecycle
  - compliance
  - workflow
---

# Four-Eyes Precheck

Use this phase to ensure a file is eligible for the formal Four-Eyes review. The goal is to catch missing metadata, attachments, or lifecycle mismatches before the reviewer is even notified.

## Ingredients

- `$vaultContext` – Confirms the selected file or item.
- `$vault` / `$vaultConnection` – Queries lifecycle definitions and custom properties.
- `$dsDiag` – Surfaces validation errors directly in Vault Explorer.

## Preconditions

| Requirement | Details |
| --- | --- |
| Lifecycle | File must be in **Work In Progress** or **For Review** before running the precheck. |
| Required properties | `PROJECT_CODE`, `MATERIAL`, `REVISION`, and at least one custom compliance flag. |
| Attachments | Latest drawing (`.idw`/`.dwg`) attached as primary reference. |

## Steps

1. **Detect the lifecycle** – Abort if the entity is already in `Released`.
2. **Validate metadata** – Use `$vault.DocumentService.GetAllFileProperties()` to inspect the required fields.
3. **Check attachments** – Confirm at least one attachment exists using `$vault.DocumentServiceExtensions.GetReferencesByMasterId()`.
4. **Log findings** – Output failures via `$dsDiag.Trace()` and set a custom property like `FOUR_EYES_STATUS = Precheck Failed`.
5. **Gate transitions** – If all checks pass, set `FOUR_EYES_STATUS = Ready` so the user check can proceed.

## Sample script excerpt

```powershell
function Invoke-FourEyesPrecheck {
    param($vaultContext)

    if ($vaultContext.SelectedObject.TypeId.SelectionContext -ne 'filemaster') {
        throw 'Run the precheck on files only.'
    }

    $file = $vault.DocumentService.GetLatestFileByMasterId($vaultContext.SelectedObject.Id)
    $missing = @()

    foreach ($propName in 'PROJECT_CODE','MATERIAL','REVISION') {
        if ([string]::IsNullOrWhiteSpace($Prop[$propName])) {
            $missing += $propName
        }
    }

    if ($missing.Count -gt 0) {
        $dsDiag.ShowMessage("Missing properties: {0}" -f ($missing -join ', '))
        return $false
    }

    return $true
}
```

## Validation checklist

- [ ] Transition blocked when metadata is incomplete.
- [ ] Attachments validated on both Inventor and AutoCAD files.
- [ ] `$dsDiag` output saved for the change record.

## Downloadable issue template

Copy the snippet below into `.github/ISSUE_TEMPLATE/four-eyes-precheck.md` if you want GitHub issues to capture precheck evidence.

```markdown
### Precheck summary
- File name:
- Vault path:
- Lifecycle state:

### Validation
- [ ] PROJECT_CODE populated
- [ ] MATERIAL populated
- [ ] REVISION populated
- [ ] Drawing attached
```
