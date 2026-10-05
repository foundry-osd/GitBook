# Domain Join

Use **Domain Join** to join installed Windows to an Active Directory domain before OOBE. Choose [Interactive Domain Join](interactive.md) when a technician supplies credentials for each deployment, or [Zero-touch Domain Join](zero-touch.md) when protected media supplies them.

Exactly one Domain Join or [Windows Autopilot](../autopilot/README.md) mode can be active. Open the intended page and choose **Enable**. Confirm replacement if another mode is active. **Disable** excludes joining from new media while retaining nonsecret configuration. Credentials are cleared when their domain, account or active-mode ownership changes.

## Before configuring

- Arrange domain DNS and writable domain-controller access from installed Windows. An OU list can be prepared offline; it does not make joining available offline. Connect's Internet readiness does not establish AD readiness.
- Ask the AD administrator to provide an account with the required join, reuse, directory-read and OU-placement permissions. Discovery and configuration validation do not audit those permissions.
- Choose a unique [concrete computer name](../customization/machine-naming.md). A [custom answer file](../customization/unattend.md) must supply exactly one valid applicable `specialize` computer name and contain no `Microsoft-Windows-UnattendedJoin` component.
- Review [edition and runtime requirements](../../reference/supported-versions.md#domain-join). Known Windows Home-family editions skip joining with a warning while Windows installation continues.

## Configure organizational units

Both pages share the same optional OU list. Use a DNS domain such as `corp.example.test`, a display name such as `Workstations`, and a distinguished name such as `OU=Workstations,DC=corp,DC=example,DC=test`.

1. Enter **Domain name**. It is required for Zero-touch and whenever you list OUs; Interactive without listed OUs may leave it for the technician.
2. Expand **Organizational units**. Under **Add an OU manually**, enter **Display name** and **Distinguished name**, then choose **Add OU**. The DN must name an organizational unit inside that domain, so it starts with `OU=`; a container such as `CN=Computers` is not accepted. You can list up to 1,024 OUs.
3. Alternatively, use [search and import](#find-and-import-ous).
4. Choose **Default OU**, or **Clear** to leave no default.
5. Enable **Let technicians choose the OU** if technicians should choose among the listed OUs. A default is preselected; without one, a listed choice is required before continuing.

With selection disabled, the configured default is fixed. With no default, Windows uses its default domain account location; a reused account is left in its current location. When an OU list applies, technicians can only pick from it. Interactive deployment without a usable OU list offers an optional typed DN instead. Zero-touch never accepts a typed OU.

Changing the authored domain clears the default and disables selection, but keeps the listed OUs. Select the OUs from the previous domain under **Organizational units** and choose **Remove selected**, or correct the domain, before creating media. Removing the default OU clears the default; removing the last OU also turns off technician choice.

Listed OUs that no longer match the domain name are an editing draft. While Domain Join is enabled, repair the mismatch before saving a named-profile checkpoint, exporting the profile or creating media. A disabled Domain Join mode does not block them. Activating a named profile restores its last valid checkpoint.

## Find and import OUs

1. Use an authoring computer already joined to the intended AD domain and connect it to that directory.
2. Under **Import OUs from this computer's domain**, choose **Find OUs**.
3. Review the displayed computer domain and the results. Select the OUs to keep, then choose **Add selected OUs**. Searching alone does not change the saved OU list.
4. Review the resulting OU list and default. OUs added manually and the default are preserved; duplicate DNs are not added again. An empty domain name is filled in from the import; if the domain name or the listed OUs belong to another domain, correct that first.

The search uses the computer's actual AD domain, using your current Windows identity to read it. The signed-in user's domain is not the domain selector. Requests and result size are bounded; incomplete results are labelled as such. Use **Cancel search** if needed. You can still add OUs manually when the search is unavailable.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-domain-join-01-organizational-units.png`
- **Capture:** Show the OU list, default OU, technician choice toggle and selected search results using sanitized demonstration data.
{% endhint %}

## Build and verify

Resolve the active page's readiness messages and [create or update media](../media/README.md). Follow [Domain Join in Deploy](../../foundry-deploy/domain-join.md) through staging, installed-Windows joining, controlled restart and verification. A selected OU applies to new accounts and to safely identified reused accounts; an existing account is moved without deleting or recreating it, preserving its object GUID.

Joining, OU placement and later local membership are separate results. Review [domain warnings and uncertain outcomes](../../troubleshooting/domain-join.md) before handoff.
