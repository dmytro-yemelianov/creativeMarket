# OnPostCloseDialog Recipes

`OnPostCloseDialog` fires when a CAD user dismisses a Data Standard dialog with **OK**. In Inventor and AutoCAD the hook executes *before* the filename is finalized and mapped properties update, making it ideal for last-second validation or property manipulation.【F:COMPLETE_DOCUMENTATION.md†L47041-L47048】

## Typical uses

1. **Guarantee mapped property accuracy.** Prompt the user (or auto-fix) if required iProperties are blank before the dialog hands control back to Vault.
2. **Derive filenames or item numbers.** Because the callback runs prior to naming, you can adjust `$Prop['Name']` or `$Prop['Number']` with deterministic logic.
3. **Coordinate follow-up automation.** Invoke downstream scripts through `$dsCommands` or `$vault` once the dialog is ready to close.

## Implementation checklist

1. Create or edit the CAD `Default.ps1` script delivered with Data Standard.
2. Add a function signature that accepts `$context` (commonly `$dsParam`) so you can read the active document, selected file, or user input.
3. Call helper functions (examples below) to keep the callback concise.

```powershell
function OnPostCloseDialog($context) {
    Ensure-RequiredProperties -Context $context -Required @('TITLE', 'DESCRIPTION')
    Set-DefaultNumbering -Context $context
}
```

## Helper recipe: property validation

```powershell
function Ensure-RequiredProperties {
    param(
        [Parameter(Mandatory)]$Context,
        [string[]]$Required
    )

    foreach ($propName in $Required) {
        if ([string]::IsNullOrWhiteSpace($Prop[$propName])) {
            $dsDiag.ShowMessage("Property '$propName' cannot be empty before closing the dialog.")
            throw "Missing property $propName"
        }
    }
}
```

- `$Prop[]` provides access to Vault/CAD properties everywhere Data Standard runs.【F:COMPLETE_DOCUMENTATION.md†L47121-L47122】
- `$dsDiag` is available everywhere and is handy for debugging or blocking dialog closure.【F:COMPLETE_DOCUMENTATION.md†L47116-L47116】

## Helper recipe: numbering templates

```powershell
function Set-DefaultNumbering {
    param(
        [Parameter(Mandatory)]$Context
    )

    if ([string]::IsNullOrEmpty($Prop['Number'])) {
        $Prop['Number'] = (Get-Date -Format 'yyyyMMdd') + "-" + $Context.UserName
    }
}
```

Keep these helpers in separate `.ps1` modules and dot-source them from `Default.ps1` so your `OnPostCloseDialog` stays readable.
