---
title: Four-Eyes User Check
description: Checklist for the second approver who validates design quality and policy compliance.
tags:
  - governance
  - lifecycle
  - compliance
  - review
---

# Four-Eyes User Check

This phase represents the second set of eyes that approves or rejects the transition into a controlled lifecycle state. Use it to enforce separation of duties and record the final approval outcome.

## Roles & permissions

| Role | Responsibility | Vault permission |
| --- | --- | --- |
| Design owner | Submits the file with a successful precheck status. | Change state to *For Review*. |
| Reviewer | Executes user check, updates comments, approves/rejects. | Change state to *Released* or *Rejected*. |
| Compliance lead | Audits the checklist results weekly. | View lifecycle history. |

## User check steps

1. **Pull context** – The reviewer opens Vault Explorer, selects the candidate file, and loads the custom "Four Eyes" Data Standard tab.
2. **Inspect metadata** – The tab surfaces the properties validated during the precheck plus any reviewer-specific fields (`REVIEW_NOTES`, `SAFETY_CATEGORY`).
3. **Confirm attachments** – Open the referenced drawing or simulation files using `$vaultExplorerUtil.OpenFile()` and verify revision alignment.
4. **Record outcome** – Update the `FOUR_EYES_STATUS` property to `Approved` or `Rejected` and add comments.
5. **Trigger transition** – If approved, run a scripted transition that moves the file to `Released` and stamps the reviewer’s username + timestamp.

## Reviewer checklist (collapsible)

<details>
<summary>Metadata review</summary>

- [ ] Title block fields match Vault properties.
- [ ] ECO reference recorded.
- [ ] Safety category assigned.

</details>

<details>
<summary>Attachment verification</summary>

- [ ] Drawing opens without missing references.
- [ ] Simulation output attached (if applicable).
- [ ] Manufacturing notes uploaded.

</details>

<details>
<summary>Decision + audit</summary>

- [ ] Reviewer comment added.
- [ ] Lifecycle transition recorded.
- [ ] `$dsDiag` log exported (only when failures occur).

</details>

## Automation hook

Embed the following helper in your Vault menu script so reviewers can launch the checklist quickly.

```powershell
function Invoke-FourEyesUserCheck {
    param($vaultContext)

    $dialog = $dsCommands.GetCustomDialog($vaultContext, 'FourEyesUserCheck.xaml')
    $dialog.ShowDialog()
}
```

## Validation log template

```markdown
### User check summary
- Reviewer:
- Date:
- File path:
- Lifecycle state before/after:

### Outcome
- [ ] Approved
- [ ] Rejected
- Notes:
```
