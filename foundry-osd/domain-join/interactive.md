# Interactive Domain Join

Choose this mode for shared deployment media when technicians should enter a domain account and password for each target.

## Prepare the media

1. Open **Domain Join > Interactive** and choose **Enable**. If prompted, confirm replacement of the active Domain Join or Autopilot mode.
2. Enter **Domain name** to prefill the deployment dialog. You may leave it blank when no catalog is configured.
3. Optionally [add or import destinations](README.md#configure-destinations). A catalog requires its matching target domain. Set a default and decide whether to allow technician selection.
4. Resolve readiness messages, then [create or update media](../media/README.md).

Interactive joining introduces no media-password prerequisite and stores no join password in the media. Other enabled options, including custom answer files, may independently require [Protected deployment](../general.md#protected-deployment).

Choose **Disable** to exclude joining from newly created media. Nonsecret settings remain available for later activation.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-domain-join-interactive-01-configuration.png`
- **Capture:** Show the enabled Interactive Domain Join page, target DNS domain, readiness and destination policy with sanitized demonstration data.
{% endhint %}

## Deploy a target

Before disk-erasure confirmation, Deploy opens **Domain join**. Enter **Domain name**, **Account (DOMAIN\user or user@domain)** and **Password**. For example, an administrator may supply `CORP\deployment-join` for `corp.example.test`; obtain the password through the approved credential process.

If the compatible catalog picker is enabled, select a listed **Organizational unit**. The configured default starts selected, and **Continue** requires a selection. A catalog with selection disabled uses its fixed default or the default domain destination.

Without a compatible catalog, **OU distinguished name (optional)** accepts a destination inside the entered domain. Leave it empty to use Windows' default destination. Changing the domain clears prior destination input and disables incompatible catalog choices. Typed DNs are not offered alongside a compatible catalog.

Select **Continue**, then review the actual domain and destination in **Confirm disk erase**. **Cancel** returns without starting deployment. See [technician steps and outcome verification](../../foundry-deploy/domain-join.md).

Installed Windows must reach the domain controller when PostInstall runs. A successful WinPE deployment only confirms staging. Keep [safe outcome evidence](../../troubleshooting/logs-and-support.md#domain-join-evidence) if joining or placement reports a warning.
