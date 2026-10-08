# Domain Join

Use **Domain Join** to join installed Windows to an Active Directory domain before OOBE. Choose [Interactive Domain Join](interactive.md) when a technician supplies credentials for each deployment, or [Zero-touch Domain Join](zero-touch.md) when protected media supplies them.

Exactly one Domain Join or [Windows Autopilot](../autopilot/README.md) mode can be active. Open the intended page and choose **Enable**. Confirm replacement if another mode is active. **Disable** excludes joining from new media while retaining the configuration. A join password stays with its account: it is cleared when the account changes or leaves the configuration, not when the mode changes or joining is disabled.

## Before configuring

- Arrange domain DNS and writable domain-controller access from installed Windows. An OU list can be prepared offline; it does not make joining available offline. Connect's Internet readiness does not establish AD readiness.
- Ask the AD administrator to provide an account with the required join, reuse, directory-read and OU-placement permissions. Discovery and configuration validation do not audit those permissions.
- Choose a unique [concrete computer name](../customization/machine-naming.md). A [custom answer file](../customization/unattend.md) must supply exactly one valid applicable `specialize` computer name and contain no `Microsoft-Windows-UnattendedJoin` component.
- Review [edition and runtime requirements](../../reference/supported-versions.md#domain-join). Known Windows Home-family editions skip joining with a warning while Windows installation continues.
- Configure a local account on the [OOBE page](../customization/oobe.md): the built-in Administrator account or an additional local account. Without one, Windows asks to create an account at the end of setup even though the computer has joined the domain. The Domain Join pages show a warning in that case; it does not block media creation.

## List the domains

Both pages share the same lists. One media can serve several domains; at deployment one domain and one OU are retained for the computer.

Under **Domains**:

1. Choose **Add**, enter the **Domain name** (a DNS name such as `corp.example.test`), then confirm. In Zero-touch the dialog also asks which account joins that domain; see [Zero-touch Domain Join](zero-touch.md). You can list up to 32 domains.
2. The first domain you add becomes the default. To change it, select another domain and choose **Set as default**.

With one domain listed, every deployment joins it. With several, technicians choose the domain during deployment and the default is preselected. To keep a deployment free of that choice, list a single domain.

**Edit** changes the selected domain. Its name can be changed only while it lists no OUs; remove them first, or add a new domain. **Remove** deletes the selected domain together with its OUs; removing the default moves the default to the first remaining domain.

Interactive may list no domain at all. The technician then types the domain during deployment.

## List the OUs of a domain

Select a domain under **Domains**. The **Organizational units** card then shows and edits the OUs of that domain only.

1. Choose **Add**, enter a **Display name** such as `Workstations` and a **Distinguished name** such as `OU=Workstations,DC=corp,DC=example,DC=test`, then confirm. The DN must name an organizational unit inside the selected domain, so it starts with `OU=`; a container such as `CN=Computers` is not accepted. A refused entry is explained in the dialog so you can correct it. You can list up to 1,024 OUs per domain.
2. Alternatively, [import OUs from the domain](#find-and-import-ous).
3. When a domain lists several OUs, select one and choose **Set as default** to preselect it for technicians. **Clear default** returns to no preselection.

The number of OUs a domain lists decides what happens during deployment:

| OUs listed for the domain | Result |
| --- | --- |
| None | Windows uses the domain's default location; a reused account is left where it is. In Interactive mode the technician may type an OU DN instead. Zero-touch never accepts a typed OU. |
| One | That OU is always used. It is shown as the default and needs no choice. |
| Several | Technicians choose one of them during deployment. The default, if set, is preselected; without one they must pick. |

To keep a deployment free of that choice, list at most one OU for the domain.

**Edit** changes the display name of the selected OU, for example to show technicians a clearer name than the one imported from the directory. The distinguished name stays the same, the OU stays the default when it was, and a later import keeps your name. **Remove** deletes the selected OUs; removing a default OU clears that domain's default.

Both tables are sorted by name; select a column header to sort differently.

## Find and import OUs

1. Select the domain under **Domains**. The authoring computer must be able to reach that domain, and your Windows account must be allowed to read it: this is the case for the computer's own domain and for domains that trust it.
2. Under **Organizational units**, choose **Import from domain**.
3. In the dialog that lists the OUs found, select the ones to keep, then choose **Add selected OUs**. OUs already listed for that domain are not offered again, no OU is selected by default, and **Cancel** leaves the list unchanged.
4. Review the resulting list and the default.

The search reads the selected domain with your current Windows identity; it does not test the join account's permissions. Requests and result size are bounded; the dialog says when only some of the OUs could be listed. While the search runs, the same button becomes **Cancel search**. You can still add OUs manually when the domain cannot be searched.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-domain-join-01-organizational-units.png`
- **Capture:** Show the Domains card with two domains and the Organizational units card of the selected domain, with their command bars, using sanitized demonstration data.
{% endhint %}

## Build and verify

Resolve the active page's readiness messages and [create or update media](../media/README.md). Follow [Domain Join in Deploy](../../foundry-deploy/domain-join.md) through staging, installed-Windows joining, controlled restart and verification. A selected OU applies to new accounts and to safely identified reused accounts; an existing account is moved without deleting or recreating it, preserving its object GUID.

Joining, OU placement and later local membership are separate results. Review [domain warnings and uncertain outcomes](../../troubleshooting/domain-join.md) before handoff.
