# OnTabContextChanged Recipes

`OnTabContextChanged` runs whenever the context of a Vault custom tab changes—think switching files, versions, or custom XAML layouts. Use it to refresh the UI, load related metadata, or toggle controls as users navigate the client.【F:COMPLETE_DOCUMENTATION.md†L47064-L47069】

## Typical uses

1. **Update read-only summaries.** Re-query Vault when the selection changes to keep KPIs and BOM rollups fresh.
2. **Switch layouts dynamically.** Bind different XAML user controls to the tab depending on the entity type present in `$context`.
3. **Enable or disable actions.** Inspect lifecycle states, ownership, or category data before showing workflow buttons.

## Implementation checklist

1. Edit the Vault `Default.ps1` script and declare `function OnTabContextChanged($context)`.
2. Determine whether the new selection is a file, folder, or item via `$context.TabContext` and branch early.
3. Call helper functions to populate your WPF controls or hide sections that should not appear.

```powershell
function OnTabContextChanged($context) {
    switch ($context.TabContext.EntityClassId) {
        'FILE'  { Set-FileTabState -Context $context }
        'ITEM'  { Set-ItemTabState -Context $context }
        'FLDR'  { Set-FolderTabState -Context $context }
        default { Clear-CustomTab }
    }
}
```

## Helper recipe: file summary refresh

```powershell
function Set-FileTabState {
    param([Parameter(Mandatory)]$Context)

    $file = $vault.DocumentService.GetLatestFileByMasterId($Context.TabContext.EntityId)
    $Prop['NUMBER']      = $file.Num
    $Prop['DESCRIPTION'] = $file.Title
    $Prop['STATE']       = $file.FileLfCycStateName
}
```

Combine the hook with the `$vault` variable (available in Vault tabs, menus, dialogs, and CAD) to run API calls without creating your own service manager instance.【F:COMPLETE_DOCUMENTATION.md†L47123-L47124】
