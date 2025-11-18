# OnTabContextChanged hook

`OnTabContextChanged` fires inside the Vault Client whenever the selection that drives a custom tab changes (for example, when the user clicks a different file, item, or folder). Use it to repopulate controls in your XAML without rebuilding the entire tab.

## Lifecycle timing

1. A user selects a new object in Vault or switches to another XAML-based tab.
2. The Vault add-in populates `$vaultContext` with the new selection metadata.
3. `OnTabContextChanged` executes so you can fetch data, manipulate `$dsWindow`, and refresh bindings.
4. Control returns to Vault, which renders the updated tab content.

## Context objects

| Object | Availability | Typical use |
| --- | --- | --- |
| [`$vaultContext`](../../../COMPLETE_DOCUMENTATION.md#data-standard-powershell-variables) | Vault tabs & menus | Determine which entity type (file, item, change order) triggered the hook and find the active XAML file. |
| [`$vault`](../../../COMPLETE_DOCUMENTATION.md#data-standard-powershell-variables) | Vault tabs | Call into the Vault API (DocumentService, ItemService, etc.) to pull related data. |
| [`$dsWindow`](../../../COMPLETE_DOCUMENTATION.md#data-standard-powershell-variables) | Vault tabs | Update controls (grids, list views, text blocks) that are defined in the tab’s XAML. |

## Step-by-step example – dual-mode BOM/Associations tab

The sample below adapts Autodesk’s documentation snippet so a single tab can show either a BOM (when a CAD file is selected) or the files associated with an Item.

1. **Determine the active XAML file** – use `$vaultContext.UserControl.XamlFile` to keep the logic scoped to the tab.
2. **Branch on selection type** – look at `$vaultContext.SelectedObject.TypeId.SelectionContext` to distinguish file masters from item masters.
3. **Query Vault** – call either `DocumentService.GetLatestFileByMasterId()` or `ItemService.GetItemsByIds()`.
4. **Refresh the UI** – push the resulting collection into the control declared in XAML with `$dsWindow.FindName()`.

```powershell
function OnTabContextChanged {
    $xamlFile = [System.IO.Path]::GetFileName($vaultContext.UserControl.XamlFile)

    if ($vaultContext.SelectedObject.TypeId.SelectionContext -eq 'filemaster' -and $xamlFile -eq 'cad-bom.xaml') {
        $fileMasterId = $vaultContext.SelectedObject.Id
        $file = $vault.DocumentService.GetLatestFileByMasterId($fileMasterId)
        $bomRows = @(GetFileBOM $file.Id)
        $dsWindow.FindName('BomList').ItemsSource = $bomRows
        return
    }

    if ($vaultContext.SelectedObject.TypeId.SelectionContext -eq 'itemmaster' -and $xamlFile -eq 'associated-files.xaml') {
        $item = $vault.ItemService.GetItemsByIds(@($vaultContext.SelectedObject.Id))[0]
        $assocFiles = @(GetAssociatedFiles @($item.Id) $([System.IO.Path]::GetDirectoryName($vaultContext.UserControl.XamlFile)))
        $dsWindow.FindName('AssociatedFiles').ItemsSource = $assocFiles
    }
}
```

Rename the control IDs (`BomList`, `AssociatedFiles`) so they match the `x:Name` values in your XAML.

> **Try it**
>
> 1. Open `C:\ProgramData\Autodesk\Extensions\DataStandard\Vault\AddinVault\Default.ps1`.
> 2. Paste the sample `OnTabContextChanged` function (or merge the relevant branches into your existing function).
> 3. Ensure the XAML files referenced in the script (`cad-bom.xaml`, `associated-files.xaml`) live under the Vault tab configuration folder.
> 4. Relaunch the Vault client and switch between files and items to watch the tab refresh automatically.

## Troubleshooting

- **The hook never runs:** Confirm that the custom tab is still registered in your Vault client and that its XAML file path matches the value used in `$vaultContext.UserControl.XamlFile`.
- **List stays empty:** Inspect the Event Viewer or wrap calls with `$dsDiag.WriteLine()` to log API errors. Often the selection type (`filemaster` vs. `itemmaster`) is mismatched with the tab logic.
- **Out-of-date data:** Remember that `OnTabContextChanged` only fires when the context changes. If you need auto-refresh within the same selection, call your data-loading function from other events (button clicks, timers) and continue to reuse `$vaultContext`.
