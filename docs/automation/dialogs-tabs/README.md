# Dialogs & Tabs Automation Hooks

The Autodesk Vault Data Standard exposes lifecycle hooks for both dialogs and custom tabs so that administrators can shape user interactions without editing compiled add-ins. Use this section as a quick-start guide for the two hooks most administrators rely on when policing dialog input and keeping tab content in sync with the current selection.

## Available pages

- [OnPostCloseDialog](onpostclosedialog.md) – run code after CAD dialogs close but before iProperties persist.
- [OnTabContextChanged](ontabcontextchanged.md) – refresh the contents of a Vault tab every time the user switches context.
