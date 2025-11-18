---
title: PowerShell Pantry
description: Quick reference for Data Standard shared variables, their scope, and copy-ready snippets.
tags:
  - powershell
  - menu automation
  - connection object
  - vault data standard
  - datasheet dialogs
---

# PowerShell Pantry

> [!TIP]
> Save this page to your favorites—each section includes search keywords such as *menu automation*, *connection object*, and *datasheet dialogs* so you can jump straight to the snippet you need.

## Quick Shopping List

| Variable | Availability | Quick use case |
| --- | --- | --- |
| [$dsCommands](#dscommands) | Vault menus | Launch or override standard dialogs from a custom command. |
| [$dsDiag](#dsdiag) | Everywhere | Trace, inspect, and share diagnostics while iterating on scripts. |
| [$dsParam](#dsparam) | Inventor `OnPostCloseDialog` | Read post-dialog context such as selected files or error states. |
| [$dsWindow](#dswindow) | Everywhere | Reach directly into the WPF window or tab to adjust UI elements. |
| [$IsCADFile](#iscadfile) / [$IsNotCADFile](#iscadfile) | Vault file dialogs | Toggle logic based on whether the target is CAD or non-CAD. |
| [$IsOfficeClient](#isofficeclient) / [$IsNotOfficeClient](#isofficeclient) | Vault menus, file dialogs | Branch workflows for Vault Office clients vs. full clients. |
| [$Prop[]](#prop) | Everywhere | Read/write mapped Vault or CAD properties without extra API calls. |
| [$UIString[]](#uistring) | Everywhere | Pull localized labels/messages that respect UIStrings.xml. |
| [$vault](#vault) | Vault tabs, menus, dialogs; CAD | Access the `WebServiceManager` for full API coverage. |
| [$vaultApplication](#vaultapplication) | Vault tabs, dialogs | Interact with the `IApplication` host (e.g., UI refresh, selection). |
| [$vaultConnection](#vaultconnection) | Vault tabs, menus, dialogs; CAD | Grab the active `Connection` for entity + user info. |
| [$vaultContext](#vaultcontext) | Vault tabs, menus | Learn what the user selected (entity type, IDs, XAML source). |
| [$vaultExplorerUtil](#vaultexplorerutil) | Vault menus, dialogs | Drive Explorer convenience APIs (selection, file open, etc.). |
| [$vaultUsername](#vaultusername) | `OnLogOn` | Capture the authenticated username during logon events. |

---

### $dsCommands {#dscommands}

| Availability | Highlights |
| --- | --- |
| Vault menu scripts | Provides `GetCreateDialog`, `GetEditDialog`, and similar helpers so you can open Data Standard dialogs without rewriting plumbing. |

```powershell
$dialog = $dsCommands.GetCreateDialog($vaultContext.FolderId)
$dialog.XamlFile = "File.xaml"
$dialog.ShowDialog()
```

### $dsDiag {#dsdiag}

| Availability | Highlights |
| --- | --- |
| Everywhere | Offers `Inspect()`, `Trace()`, `Clear()`, and `ShowLog()` for real-time troubleshooting. |

```powershell
$dsDiag.Trace("menu automation: validating inputs")
$dsDiag.ShowLog()
```

### $dsParam {#dsparam}

| Availability | Highlights |
| --- | --- |
| Inventor `OnPostCloseDialog` | Exposes post-close context so Inventor automation can react to success/failure or the selected design file. |

```powershell
if ($dsParam.DialogResult -ne "OK") { return }
$docId = $dsParam.SelectedDocument.Id
```

### $dsWindow {#dswindow}

| Availability | Highlights |
| --- | --- |
| Everywhere | Gives access to the Data Standard window or tab (`System.Windows.Window` / `UserControl`) so you can tweak bindings at runtime. |

```powershell
$tab = $dsWindow.FindName("ProjectBlock")
$tab.Visibility = [System.Windows.Visibility]::Visible
```

### $IsCADFile / $IsNotCADFile {#iscadfile}

| Availability | Highlights |
| --- | --- |
| Vault file dialogs | Quickly branch logic for CAD vs. non-CAD design files. |

```powershell
if ($IsCADFile) {
    $dsDiag.Trace("Running CAD-specific validation")
}
```

### $IsOfficeClient / $IsNotOfficeClient {#isofficeclient}

| Availability | Highlights |
| --- | --- |
| Vault menus and file dialogs | Helps you gate advanced functionality so Vault Office users see streamlined options. |

```powershell
if ($IsOfficeClient) {
    $dsWindow.FindName("AdvancedTab").Visibility = [System.Windows.Visibility]::Collapsed
}
```

### $Prop[] {#prop}

| Availability | Highlights |
| --- | --- |
| Everywhere | Reads or writes Vault/CAD properties without issuing manual service calls. |

```powershell
$projectCode = $Prop["PROJECT_CODE"]
$Prop["STATUS"] = "For Review"
```

### $UIString[] {#uistring}

| Availability | Highlights |
| --- | --- |
| Everywhere | Resolves localized labels defined in `UIStrings.xml` so dialogs remain language-aware. |

```powershell
$statusLabel.Content = $UIString["StatusLabel"]
```

### $vault {#vault}

| Availability | Highlights |
| --- | --- |
| Vault tabs, menus, dialogs; CAD | Exposes the `WebServiceManager`, unlocking Document, Item, and Admin services. |

```powershell
$file = $vault.DocumentService.GetLatestFileByMasterId($vaultContext.SelectedObject.Id)
```

### $vaultApplication {#vaultapplication}

| Availability | Highlights |
| --- | --- |
| Vault tabs and dialogs | Provides the `IApplication` handle for UI refreshes or command routing. |

```powershell
$vaultApplication.RefreshSelectedItems()
```

### $vaultConnection {#vaultconnection}

| Availability | Highlights |
| --- | --- |
| Vault tabs, menus, dialogs; CAD | Grants the active `Connection` with user, vault, and security tokens—ideal for menu automation or downstream API calls. |

```powershell
$userName = $vaultConnection.UserName
$vaultName = $vaultConnection.Vault
```

### $vaultContext {#vaultcontext}

| Availability | Highlights |
| --- | --- |
| Vault tabs and menus | Supplies selection info (entity type, IDs, dialog metadata) so scripts can stay context-aware. |

```powershell
switch ($vaultContext.SelectedObject.TypeId.SelectionContext) {
    "filemaster" { # file logic }
    "itemmaster" { # item logic }
}
```

### $vaultExplorerUtil {#vaultexplorerutil}

| Availability | Highlights |
| --- | --- |
| Vault menus, dialogs | Surfaces Explorer helper methods like `OpenFile`, `AddToSelection`, or refresh utilities. |

```powershell
$file = $vault.DocumentService.GetLatestFileByMasterId($vaultContext.SelectedObject.Id)
$vaultExplorerUtil.OpenFile($file.EntityMasterId, $false)
```

### $vaultUsername {#vaultusername}

| Availability | Highlights |
| --- | --- |
| `OnLogOn` | Captures the authenticated Vault username at logon—helpful for audit trails or custom greetings. |

```powershell
function OnLogOn {
    param($connection)
    $vaultUsername = $connection.UserName
}
```
