# Domain Join

{% hint style="warning" %}
**Unreleased draft** — These guides describe the Domain Join implementation awaiting a supporting release and native deployment acceptance. Validate it in a disposable domain before rollout.
{% endhint %}

Use **Domain Join** to join installed Windows to an Active Directory domain before OOBE. Choose [Interactive Domain Join](interactive.md) when a technician supplies credentials for each deployment, or [Zero Touch Domain Join](zero-touch.md) when protected media supplies them.

Exactly one Domain Join or [Windows Autopilot](../autopilot/README.md) mode can be active. Open the intended page and choose **Activate**. Confirm replacement if another mode is active. **Deactivate** excludes joining from new media while retaining nonsecret configuration. Credentials are cleared when their domain, account or active-mode ownership changes.

## Before configuring

- Update Foundry OSD and create or update the boot media to include the Domain Join configuration and current runtimes.
- Arrange domain DNS and writable domain-controller access from installed Windows. A saved OU catalog can be authored offline; it does not make joining available offline. Connect's Internet readiness does not establish AD readiness.
- Ask the AD administrator to provide an account with the required join, reuse, directory-read and destination-placement permissions. Discovery and configuration validation do not audit those permissions.
- Choose a unique [concrete computer name](../customization/machine-naming.md). A [custom answer file](../customization/unattend.md) must supply exactly one valid applicable `specialize` computer name and contain no `Microsoft-Windows-UnattendedJoin` component.
- Review [edition and runtime requirements](../../reference/supported-versions.md#domain-join-unreleased). Known Windows Home-family editions skip joining with a warning while Windows installation continues.

## Configure destinations

Both pages support the same optional catalog. Use a DNS domain such as `corp.example.test`, a display label such as `Workstations`, and a distinguished name such as `OU=Workstations,DC=corp,DC=example,DC=test`.

1. Enter **Target DNS domain**. It is required for Zero Touch and for a catalog; Interactive without a catalog may leave it for the technician.
2. Under **Add a destination manually**, enter **Display label** and **OU distinguished name**, then choose **Add destination**. The DN must belong to the target domain. The catalog holds at most 1,024 destinations.
3. Alternatively, use [explicit discovery and import](#discover-and-import-destinations).
4. Choose **Default destination (optional)**, or **Clear default** to leave the destination unset.
5. Enable **Allow destination selection during deployment** if technicians should choose among the catalog rows. A default is preselected; without one, a listed choice is required before continuing.

With selection disabled, the configured default is fixed. With no default, Windows uses its default domain account location; a reused account is left in its current location. A compatible catalog restricts technicians to authored choices. Interactive deployment without a compatible catalog instead offers an optional typed DN. Zero Touch does not offer freeform destinations.

Changing the authored domain clears the default and disables selection, but retains catalog rows. Remove or correct rows from the previous domain before creating media. Removing the default row clears the default; removing the last row also clears catalog selection.

A retained catalog that no longer matches the target domain is an editing draft. Repair the mismatch before saving a named-profile checkpoint, exporting the profile or creating media. Activating a named profile restores its last valid checkpoint.

## Discover and import destinations

1. Use an authoring computer already joined to the intended AD domain and connect it to that directory.
2. Under **Import from the computer domain**, choose **Discover destinations**.
3. Review the displayed computer domain and preview. Check the rows to retain, then choose **Import selected destinations**. Discovery alone does not change the saved catalog.
4. Review the merged catalog and default. Existing manual rows and the default are preserved; duplicate DNs are not added again. An empty target domain is populated from the import; a different target/catalog domain must be corrected first.

Discovery selects the computer's actual AD domain, using your current Windows identity to read it. The signed-in user's domain is not the domain selector. Requests and preview size are bounded; an incomplete preview is labelled as such. Use **Cancel discovery** if needed. Manual destinations remain available when discovery is unavailable.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-domain-join-01-destination-catalog.png`
- **Capture:** Show the destination catalog, optional default, selection checkbox and explicitly selected discovery preview using sanitized demonstration data.
{% endhint %}

## Build and verify

Resolve the active page's readiness messages and [create or update media](../media/README.md). Follow [Domain Join in Deploy](../../foundry-deploy/domain-join.md) through staging, installed-Windows joining, controlled restart and verification. A selected destination applies to new accounts and to safely identified reused accounts; an existing account is moved without deleting or recreating it, preserving its object GUID.

Joining, OU placement and later local membership are separate results. Review [domain warnings and uncertain outcomes](../../troubleshooting/domain-join.md) before handoff.
