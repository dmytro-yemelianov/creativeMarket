# Data Standard PowerShell Variables

The Autodesk Vault Data Standard runtime pre-loads several helper variables so you can interact with dialogs, WPF controls, and the Vault API without additional boilerplate. The table below consolidates the most referenced entries from the upstream documentation.【F:COMPLETE_DOCUMENTATION.md†L47105-L47124】

| Variable | Availability | Cookbook guidance |
| --- | --- | --- |
| `$dsCommands` | Vault menus | Use it to spawn Data Standard dialogs or ribbon actions from your scripts—ideal for chaining wizards after Vault events. |
| `$dsDiag` | Everywhere | Call `ShowMessage()` during development to surface validations before closing dialogs or to log to the diagnostic console. |
| `$dsWindow` | Everywhere | Bind custom WPF controls, resize dialogs, or register event handlers using the underlying Window object. |
| `$Prop[]` | Everywhere | Read/write Vault or CAD properties without extra API calls. Perfect for recipes such as auto-numbering, property mirroring, or conditional visibility. |
| `$vault` | Vault tabs, menus, dialogs; CAD | Grants direct access to the Vault `WebServiceManager`. Use it to query metadata in `OnTabContextChanged` or to commit changes during workflow automation. |
| `$vaultApplication` | Vault tabs, dialogs | Provides the `IApplication` entry point for UI integrations. Tap into it for document events that occur outside Data Standard dialogs. |

> 💡 Tip: keep variable usage inside helper functions so you can reuse the logic from both Vault and CAD contexts without duplication.
