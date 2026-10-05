# Interactive Domain Join

Choose this mode for shared deployment media when technicians should enter a domain account and password for each target.

## Prepare the media

1. Open **Domain Join > Interactive** and choose **Enable**. If prompted, confirm replacement of the active Domain Join or Autopilot mode.
2. Enter **Domain name** to prefill the Domain join step in Deploy. You may leave it blank when no OUs are listed.
3. Optionally [add or import OUs](README.md#configure-organizational-units). Listed OUs require the matching domain name. Set a default and decide whether to allow technician selection.
4. Resolve readiness messages, then [create or update media](../media/README.md).

Interactive joining introduces no media-password prerequisite and stores no join password in the media. Other enabled options, including custom answer files, may independently require [Protected deployment](../general.md#protected-deployment).

Choose **Disable** to exclude joining from newly created media. Nonsecret settings remain available for later activation.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-domain-join-interactive-01-configuration.png`
- **Capture:** Show the enabled Interactive Domain Join page, domain name, validation messages and the Organizational units section with sanitized demonstration data.
{% endhint %}

## Deploy a target

The Deploy wizard shows a **Domain join** step before **Summary**. Enter **Domain name**, **Account (DOMAIN\user or user@domain)** and **Password**. For example, an administrator may supply `CORP\deployment-join` for `corp.example.test`; obtain the password through the approved credential process.

If technician choice is enabled, select a listed **Organizational unit**. The configured default starts selected, and **Next** requires a selection. An OU list with technician choice disabled uses its fixed default or the domain's default location.

Without a usable OU list, **OU distinguished name (optional)** accepts an OU inside the entered domain. Leave it empty to use the domain's default location. Changing the domain hides listed OUs from another domain. A typed DN is not offered when an OU list applies.

Review the domain name and OU in the summary's **Domain join** category before starting. See [technician steps and outcome verification](../../foundry-deploy/domain-join.md).

Installed Windows must reach the domain controller when PostInstall runs. A successful WinPE deployment only confirms staging. Keep [safe outcome evidence](../../troubleshooting/logs-and-support.md#domain-join-evidence) if joining or placement reports a warning.
