# Domain Join

Use **Domain Join** to join the computer to an Active Directory domain during deployment, before the first sign-in. Two modes are available:

| Mode | Who supplies the join account | Use it when |
| --- | --- | --- |
| [Zero-touch](zero-touch.md) | The media, protected by the technician password | Deployments must run without typing domain credentials. |
| [Interactive](interactive.md) | The technician, during each deployment | The media is shared and must not carry domain credentials. |

Only one of Zero-touch Domain Join, Interactive Domain Join and the [Windows Autopilot](../autopilot/README.md) modes can be active. Open the page you want and choose **Enable**; Foundry asks for confirmation when it replaces another active mode. **Disable** removes the join from new media and keeps your settings.

<figure>
  <img src="../../.gitbook/assets/foundry-osd-domain-join-01-overview-empty.png" alt="Zero-touch Domain Join page before it is enabled, with the Enable button and empty Domains and Organizational units cards">
  <figcaption>A Domain Join page before it is enabled: choose Enable, then list the domains and their OUs.</figcaption>
</figure>

## Before configuring

- **Network.** The join happens in installed Windows and needs the domain at that moment: the computer must reach the domain's DNS and a domain controller. Listing OUs in advance does not make an offline join possible, and the Internet check in Foundry Connect does not test access to the domain.
- **Join account.** Ask your Active Directory administrator for an account allowed to add computers to the domain, to reuse an existing computer account when a machine is redeployed, and to move computers to the OUs you list. Foundry does not check these permissions.
- **Computer name.** Give each computer a unique name with [Machine naming](../customization/machine-naming.md). With a [custom answer file](../customization/unattend.md), the file must set exactly one fixed computer name in its `specialize` pass and must not contain a `Microsoft-Windows-UnattendedJoin` component.
- **Windows edition.** Windows Home editions cannot join a domain: Foundry skips the join with a warning and the installation continues. See [supported editions](../../reference/supported-versions.md#domain-join).

## List the domains

Both pages share the same lists. One media can serve several domains; during deployment, one domain and one OU are used for the computer.

Under **Domains**:

1. Choose **Add**, enter the **Domain name** (a DNS name such as `corp.contoso.com`), then choose **Add domain**. In Zero-touch the dialog also asks which account joins that domain; see [Zero-touch Domain Join](zero-touch.md). You can list up to 32 domains.
2. The first domain you add becomes the default. To change it, select another domain and choose **Set as default**.

What happens during deployment depends on how many domains you list:

| Domains listed | Result |
| --- | --- |
| None (Interactive only) | The technician types the domain name. |
| One | Every deployment joins that domain. |
| Several | The technician chooses the domain. The default is preselected. |

**Edit** changes the selected domain. Its name can be changed only while it lists no OUs; remove them first, or add a new domain. **Remove** deletes the selected domain together with its OUs; if it was the default, the first remaining domain becomes the default.

## List the OUs of a domain

Select a domain under **Domains**. The **Organizational units** card then shows the OUs of that domain only.

1. Choose **Add**, enter a **Display name** such as `Workstations` and a **Distinguished name** such as `OU=Workstations,DC=corp,DC=contoso,DC=com`, then choose **Add OU**. The distinguished name must be an organizational unit of the selected domain, so it starts with `OU=`; a container such as `CN=Computers` is not accepted. If the entry is refused, the dialog explains why so you can correct it. You can list up to 1,024 OUs per domain.
2. Alternatively, [import OUs from the domain](#find-and-import-ous).
3. When a domain lists several OUs, select one and choose **Set as default** to preselect it for technicians. **Clear default** returns to no preselection.

What happens during deployment depends on how many OUs the domain lists:

| OUs listed for the domain | Result |
| --- | --- |
| None | The computer goes to the domain's default location. In Interactive mode the technician may type the distinguished name of an OU instead. |
| One | That OU is always used. It is shown as the default. |
| Several | The technician chooses one of them. The default, if you set one, is preselected; without one the technician must pick. |

**Edit** changes the display name of the selected OU, for example to show technicians a clearer name than the one imported from the directory. The distinguished name stays the same, and a later import keeps your name. **Remove** deletes the selected OUs.

Both tables are sorted by name; select a column header to sort differently.

{% hint style="info" %}
For a Zero-touch deployment that asks the technician nothing, list one domain and at most one OU for it.
{% endhint %}

## Find and import OUs

Instead of typing distinguished names, you can read them from the domain.

1. Select the domain under **Domains**. This computer must be able to reach that domain, and your Windows account must be allowed to read it. This is the case for the computer's own domain and for domains that trust it.
2. Under **Organizational units**, choose **Import from domain**. While the search runs, the same button becomes **Cancel search**.
3. In the dialog, select the OUs to keep, then choose **Add selected OUs**. OUs already listed for that domain are not offered again, and no OU is selected for you.
4. Review the resulting list and the default.

The search uses your Windows account, not the join account, so it does not prove that the join account can use these OUs. When the domain holds more OUs than the search can return, the dialog says that only some of them are listed. If the domain cannot be searched, add the OUs manually.

## Existing computer accounts

When a computer account with the same name already exists in the domain, for example when a machine is redeployed, Foundry reuses it instead of creating a new one. If an OU applies, the existing account is moved to that OU without being deleted, so its group memberships and policies are kept. Without an OU, an existing account stays where it is.

## Create the media and check the result

Resolve the messages shown on the page, then [create or update the media](../media/README.md). The join itself runs after Windows is installed; see [Domain Join during deployment](../../foundry-deploy/domain-join.md) for what the technician does and how to check the outcome, and [Domain Join troubleshooting](../../troubleshooting/domain-join.md) when something goes wrong.
