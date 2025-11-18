---
title: VGet Automation Playbook
description: Batch-download Vault folders or file sets with the VGet command-line utility.
tags:
  - operations
  - automation
  - cli
  - downloads
  - vault explorer
---

# VGet Automation Playbook

Use **VGet.exe** (installed alongside the Vault Client) when you need scripted downloads of Vault content without driving the GUI. This playbook collects the most common scenarios so you can mirror project folders, seed build servers, or archive released data straight from the command line.

## When to use VGet

- **Build and simulation servers** need a fresh workspace before running Inventor, AutoCAD, or custom tooling.
- **Disaster-recovery drills** require a scripted snapshot of the `Released` lifecycle state.
- **Field deployments** (e.g., offline laptops) must preload content before travelling.

## Ingredients

| Ingredient | Details |
| --- | --- |
| VGet executable | Located under `C:\Program Files\Autodesk\Vault Client\VGet.exe` on every full Vault Client install. |
| Vault connection info | Vault server name, vault name, and a Vault user with at least **Read** permission on the target folders. |
| Output workspace | Empty folder that will host the mirrored files. Consider mapping to `D:\VaultCache\ProjectX`. |
| Scope definition | Either a Vault folder path (`$/Designs/Robot`) or a text file with entity URNs for fine-grained control. |
| Optional filters | Revision, lifecycle state, or file extensions—apply with command-line switches to keep downloads small. |

## Core commands

| Scenario | Command | Notes |
| --- | --- | --- |
| Mirror a folder | `VGet.exe /S VaultServer /V Vault /U designer /P ****** /L "$/Designs/Robot" /O "D:\VaultCache\Robot" /M` | `/M` maintains the Vault folder structure under the output directory. |
| Pull latest released files only | `VGet.exe /S VaultServer /V Vault /U qa /P ****** /L "$/Designs/Robot" /STATE Released /REV LATEST` | Combine `/STATE` and `/REV` to avoid partially approved data. |
| Filter by extension | `VGet.exe /F "*.idw;*.dwg" ...` | Pair with any other scenario to keep only documentation files. |
| Scope from list file | `VGet.exe /FILE scope.txt /O D:\Archive` | `scope.txt` contains one Vault path or URN per line. |

> [!TIP]
> VGet returns non-zero exit codes when authentication fails or when it cannot find the specified folder. Capture the exit code in your automation so CI pipelines can stop early.

## Workflow recipe – nightly mirror of Released files

1. **Create a service account** with read-only access to the Released lifecycle state.
2. **Provision workspace** on the target server (for example `D:\VaultCache\Nightly`).
3. **Author a wrapper script** using the template below so logs land next to the downloads.
4. **Schedule** the script with Windows Task Scheduler or Azure Automation.

```powershell
$env:VGET="C:\Program Files\Autodesk\Vault Client\VGet.exe"
$log = Join-Path $PSScriptRoot ("vget-{0:yyyyMMdd-HHmm}.log" -f (Get-Date))
$args = @(
    '/S', 'vault.prod.local',
    '/V', 'Engineering',
    '/U', 'svc_vault_download',
    '/P', $env:VAULT_SVC_PWD,
    '/L', '$/Products/Released',
    '/O', 'D:\VaultCache\ReleasedMirror',
    '/STATE', 'Released',
    '/REV', 'LATEST',
    '/M'
)
Start-Process -FilePath $env:VGET -ArgumentList $args -Wait -PassThru |
    Tee-Object -FilePath $log | Out-Null
if ($LASTEXITCODE -ne 0) {
    throw "VGet failed with exit code $LASTEXITCODE. See $log"
}
```

## Validation checklist

- [ ] Workspace path cleared (optional) before running the job.
- [ ] Latest files retrieved when the command is re-run.
- [ ] Exit code monitored and surfaced in CI/CD or monitoring.
- [ ] Logs archived alongside the downloaded payload.

## Troubleshooting

| Symptom | Resolution |
| --- | --- |
| `Login failed` | Confirm the account can sign into the Vault Client and that the password does not contain characters blocked by your shell quoting rules. |
| Downloads stop early | Inspect the log for `File already exists` messages—clear the workspace or add `/FRESH` to force overwrites. |
| Files missing dependencies | Combine `/D` to pull children, or generate a BOM scope file via the Vault API before calling VGet. |
| Task Scheduler success but empty folder | Run the script interactively once to accept any first-run prompts and to confirm the account has filesystem permissions. |

## Related recipes

- Tie VGet jobs to lifecycle changes documented in the [Four-Eyes User Check](four-eyes-user-check.md) so released packages immediately replicate downstream.
- Bundle VGet logs with the [Support Questionnaire Checklists](support-checklists.md) when reporting download failures.
