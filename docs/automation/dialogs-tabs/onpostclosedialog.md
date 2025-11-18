# OnPostCloseDialog hook

Use `OnPostCloseDialog` to run validation or property-update logic after an Inventor or AutoCAD Data Standard dialog closes but before Vault writes iProperties. That timing lets you clean up values that were entered in the UI while the dialog context is still in memory.

## Lifecycle timing

1. A user completes a create/edit dialog inside the CAD add-in and clicks **OK**.
2. Data Standard raises `OnPostCloseDialog` while the dialog window (`$dsWindow`) is still instantiated.
3. Your script can read or update UI controls, `$Prop[]` values, and any context in `$dsParam`.
4. After your code returns, the add-in sets the final file name and persists property changes to Vault.

## Context objects

| Object | Availability | Typical use |
| --- | --- | --- |
| [`$dsWindow`](../../../COMPLETE_DOCUMENTATION.md#data-standard-powershell-variables) | Always | Query or update dialog controls before the window is destroyed. |
| [`$dsParam`](../../../COMPLETE_DOCUMENTATION.md#data-standard-powershell-variables) | Inventor/AutoCAD dialogs | Read context such as the dialog name, CAD document type, or numbering scheme choice. |
| [`$Prop[]`](../../../COMPLETE_DOCUMENTATION.md#data-standard-powershell-variables) | Always | Overwrite iProperties before Vault applies them. |

> **Tip:** `$dsWindow.Name` resolves to either `InventorWindow` or `AutoCADWindow`, making it easy to branch logic per host.
>
> `$vaultContext` is not populated in CAD dialogs, so expect it to be `$null` inside `OnPostCloseDialog`.

## Step-by-step example – auto-prefix part numbers

Goal: ensure that Inventor parts created from a "Machined" template automatically prefix the part number with `MC-` after the dialog closes.

1. **Detect the dialog** – bail out if the hook fires for AutoCAD.
2. **Read the selected template** – grab the template picklist from the dialog via `$dsWindow.FindName()`.
3. **Update the part number** – modify `$Prop["_Part Number"]` before Vault persists the value.

```powershell
function OnPostCloseDialog {
    param($dsParam)

    if ($dsWindow.Name -ne 'InventorWindow') {
        return
    }

    $templatePicker = $dsWindow.FindName('TemplateDropDown')
    if ($null -eq $templatePicker) {
        return
    }

    $templateName = $templatePicker.SelectedItem
    if ($templateName -match 'Machined' -and $Prop['_Part Number'].Value -notmatch '^MC-') {
        $Prop['_Part Number'].Value = 'MC-{0}' -f $Prop['_Part Number'].Value
    }
}
```

Because the hook runs before the file name and properties are committed, the user sees the final `MC-` prefix immediately when the document opens in Inventor.

> **Try it**
>
> 1. Open `C:\ProgramData\Autodesk\Extensions\DataStandard\CAD\Addins\Default.ps1` on a CAD client.
> 2. Replace the placeholder `OnPostCloseDialog` with the example above (or wrap your own logic inside the function).
> 3. Save the file and relaunch Inventor/AutoCAD so the add-in reloads.
> 4. Create a new file with the Machined template—the part number is rewritten before the file hits Vault.

## Troubleshooting

- **Hook never fires:** Verify that the `OnPostCloseDialog` function exists in `Default.ps1`. Removing it or renaming it prevents Data Standard from calling the hook.
- **Variables are `$null`:** Ensure you are editing the CAD Default script, not a Vault tab script. `$dsParam` is only populated in the dialog implementation.
- **Edits do not persist:** Confirm that the property you modify is writable. You can inspect availability in the [Data Standard PowerShell variables reference](../../../COMPLETE_DOCUMENTATION.md#data-standard-powershell-variables) and by checking Vault property definitions.
