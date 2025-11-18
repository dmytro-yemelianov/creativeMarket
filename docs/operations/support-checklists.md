---
title: Support Questionnaire Checklists
description: Field-ready checklists converted from the original Vault support questionnaire.
tags:
  - support
  - troubleshooting
  - operations
  - checklist
---

# Support Questionnaire Checklists

Transform the legacy questionnaire into structured checklists so support engineers can capture consistent data before escalating Vault issues.

## How to use

1. Duplicate the sections you need into a GitHub issue, service ticket, or Teams post.
2. Attach any exported logs or `$dsDiag` traces mentioned in the Debugging Playbook.
3. Mark each checkbox as you collect information from the requester.

## Environment snapshot

<details>
<summary>Expand to capture environment details</summary>

- [ ] Vault version / build:
- [ ] Client versions (Inventor, AutoCAD, Vault Client):
- [ ] Authentication type (AD, Vault, SSO):
- [ ] Number of connected sites:
- [ ] Customizations enabled (Data Standard, ADMS extensions):

</details>

## Problem statement

<details>
<summary>Describe the symptoms</summary>

- [ ] Affected command / hook:
- [ ] Error message (copy exact text):
- [ ] Screenshots or video attached:
- [ ] Frequency (always, intermittent):
- [ ] First observed date/time:

</details>

## Data & scope

<details>
<summary>Capture entity details</summary>

- [ ] Vault path(s):
- [ ] Lifecycle states involved:
- [ ] File types (`.ipt`, `.dwg`, etc.):
- [ ] Number of users impacted:
- [ ] Replication or Job Processor involvement:

</details>

## Troubleshooting performed

<details>
<summary>Record what has already been tried</summary>

- [ ] Cleared local workspace / restarted clients.
- [ ] Rebuilt Data Standard cache.
- [ ] Reviewed `$dsDiag` logs.
- [ ] Validated lifecycle definitions.
- [ ] Ran [Debugging Playbook](../troubleshooting/debugging-playbook.md) steps.

</details>

## Escalation package

When escalating, bundle the following artifacts:

- Exported log bundle (`ADMSConsole.exe /Extractlog`).
- `$dsDiag` HTML trace.
- Issue template outputs from [Four-Eyes Precheck](four-eyes-precheck.md) or [User Check](four-eyes-user-check.md) if the issue affects compliance workflows.
