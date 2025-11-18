## Lifecycle Governance

### Four Eyes Check

The Four Eyes Check enforces segregation of duties during lifecycle state changes so that no document can be promoted by the same person twice in a row. The recipe contains two sequential controls—**Precheck** and **User Check**—and both must pass for the transition to succeed.

| Stage | Purpose | Configuration Points |
| --- | --- | --- |
| Precheck | Confirms that the transition is eligible for Four Eyes logic. | Lifecycle definition (from/to states), document file-extension filters, document property filters, related-document property filters (first-level related files only). |
| User Check | Confirms that a different person is performing the transition. | Vault user history for the lifecycle state, comparison of *Current User* vs. *Previous User*. |

#### Precheck details

1. **Lifecycle selection** – Specify the exact lifecycle name plus the *from* and *to* states that require dual control.
2. **Document filters** – Add file-extension criteria (for example, `ipt`, `iam`, `dwg`) and property filters (such as project, release category, or custom UDAs) that must match before continuing.
3. **Related-document scan** – Optionally repeat the file-extension/property filters for first-level dependents to ensure that sub-components also satisfy governance rules.
4. **Flow reference** – Use the existing [Precheck flowchart](../images/illustration-voa-precheck-workflow.png) to visualize how each condition gates the state change.

Only when *all* conditions evaluate to true does the system advance to the User Check.

#### User Check details

1. Read the user stored on the most recent state change (the *Previous User*).
2. Compare it to the *Current User* attempting the transition.
3. **Pass condition** – Users are different. The state change proceeds.
4. **Fail condition** – Users match. The transition is canceled, and Vault informs the user that a different approver is required.

If the Precheck fails, the User Check never executes, ensuring that lifecycle or property misconfigurations are surfaced first. Administrators can disable the recipe by leaving the configuration blank, at which point the governance logic defaults to “allow.”

![](../images/illustration-voa-precheck-workflow.png)

### Implementation tips

* Maintain a central table of lifecycle names and their required property filters so administrators can reuse settings consistently across products.
* When testing, use two sample accounts (for example, `designer.test` and `checker.test`) and alternate check-ins to validate both the Precheck and User Check outcomes.
* Document any exceptions (e.g., prototype lifecycles) directly inside the lifecycle definition so downstream teams know when the Four Eyes Check is intentionally bypassed.

## Command-line Automation

### VGet overview

`VGet` is a lightweight command-line utility that retrieves Vault files or folders and saves them to a local path. It is ideal for scripted build steps, nightly exports, or quick ad-hoc downloads when the full client UI is unnecessary.

#### Prerequisites

* Vault client components installed on the machine running `VGet`.
* Network access to the Vault server (typically HTTPS on port 443 or the port configured for ADMS).
* Login credentials with download permissions for the target Vault folders/files.
* (Optional) A configuration file or environment variables for storing server URL, Vault name, and authentication tokens to avoid typing them repeatedly.

#### Common CLI examples

```bash
# Download a single file to the current working directory
vget \
  --server https://vault.company.com \
  --vault Engineering \
  --entity "$/Projects/2026/Valve/Valve.iam" \
  --output ./Valve.iam

# Mirror an entire folder (including children) into ./exports
vget \
  --server https://vault.company.com \
  --vault Engineering \
  --entity "$/Projects/2026/Valve" \
  --recursive \
  --output ./exports

# Download a folder but filter by file type (e.g., only DWG)
vget \
  --server https://vault.company.com \
  --vault Engineering \
  --entity "$/Shared/Drawings" \
  --recursive \
  --extension dwg \
  --output ./drawings
```

Tips:

* Combine `VGet` with scheduled tasks or CI jobs to ensure manufacturing and downstream teams always have the latest exports.
* When scripting, capture the exit code so pipelines can fail fast if a download or authentication step breaks.
* For large syncs, pair `VGet` with checksum verification (e.g., `Get-FileHash` on Windows) to confirm downstream consumers receive identical files.

## Support Playbooks

Convert the traditional question lists into reusable checklists that can be pasted into GitHub Issues, ServiceNow incidents, or GitBook troubleshooting guides. Copy the relevant Markdown block whenever an investigation begins and fill in the checkboxes as information is collected.

### General Intake Checklist

```markdown
- [ ] Problem statement (single sentence):
- [ ] Key information still needed:
- [ ] Information currently available:
- [ ] Business impact / justification:
- [ ] Items explicitly ruled out (solutions already tried):
- [ ] Reproduction steps documented (screenshots/diagrams/video links attached):
- [ ] Constraints noted (OS, SQL, Vault version, deadlines):
```

### Supportability Checklist

```markdown
- [ ] Reviewed Autodesk hotfix pages (Vault / Inventor / AutoCAD / other):
- [ ] Searched public forums or engines for the exact error text:
- [ ] Confirmed issue persists after server/client restart:
- [ ] Confirmed maintenance/subscription plan status:
- [ ] Captured start date + recent changes (Windows updates, policy changes, network tweaks):
- [ ] Validated environment against [Vault System Requirements](https://www.autodesk.com/support/technical/article/caas/sfdcarticles/sfdcarticles/System-requirements-for-Autodesk-Vault-products.html) (OS, SQL, server, CAD client language/version):
- [ ] Reinstall attempts recorded (server, CAD apps, Vault client):
- [ ] Patch level documented for server, CAD apps, and Vault client (with readme instructions followed):
- [ ] Backup restore test performed on alternate server:
```

### Troubleshooting Checklist

```markdown
- [ ] Issue scope defined (server vs. client; number of affected clients):
- [ ] Reproduced in a fresh Vault on the same server:
- [ ] Tested in latest release:
- [ ] Behavior when not logged into Vault documented:
- [ ] Tested with alternate users (Windows Auth + Vault Auth) and permission levels:
- [ ] Proxy server involvement verified:
- [ ] Local Vault profile reset:
- [ ] Installation source verified (media vs. network deployment integrity):
- [ ] Tested with AV/Firewall disabled on client, server, and both:
- [ ] Collected log bundles (vlogs, ADMSConsole, JobProcessor, Event Viewer):
- [ ] All assumptions challenged / outstanding hypotheses listed:
```
