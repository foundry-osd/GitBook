# Interactive Domain Join

Choose this mode for shared deployment media when technicians should enter a domain account and password for each target.

## Prepare the media

1. Open **Domain Join > Interactive** and choose **Enable**. If prompted, confirm replacement of the active Domain Join or Autopilot mode.
2. Optionally [list the domains](README.md#list-the-domains) technicians may join. With at least one domain listed, the technician joins one of them and cannot type another; with none, the technician types the domain during deployment.
3. Optionally [add or import OUs](README.md#list-the-ous-of-a-domain) for each domain. A single OU is always used; with several, technicians choose one and the default is preselected.
4. Resolve readiness messages, then [create or update media](../media/README.md).

Interactive joining introduces no media-password prerequisite and stores no join password in the media. Accounts and passwords entered on the Zero-touch page stay in the profile for a later switch back, but are never written to Interactive media. Other enabled options, including custom answer files, may independently require [Protected deployment](../general.md#protected-deployment).

Choose **Disable** to exclude joining from newly created media. Nonsecret settings remain available for later activation.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-domain-join-interactive-01-configuration.png`
- **Capture:** Show the enabled Interactive Domain Join page with the Domains card and the Organizational units card of the selected domain, using sanitized demonstration data.
{% endhint %}

## Deploy a target

The Deploy wizard shows a **Domain join** step before **Summary**. Choose the **Domain name** when the media lists several; otherwise the domain is shown, or typed when the media lists none. Enter **Account (DOMAIN\user or user@domain)** and **Password**. For example, an administrator may supply `CORP\deployment-join` for `corp.example.test`; obtain the password through the approved credential process.

If the retained domain lists several OUs, select a listed **Organizational unit**. That domain's default, if it has one, starts selected, and **Next** requires a selection. A domain that lists a single OU uses it without asking.

For a domain without listed OUs, **OU distinguished name (optional)** accepts an OU inside that domain. Leave it empty to use the domain's default location. Changing the domain replaces the OU choices with those of the new domain and keeps the account and password you typed.

Review the domain name and OU in the summary's **Domain join** category before starting. See [technician steps and outcome verification](../../foundry-deploy/domain-join.md).

Installed Windows must reach the domain controller when PostInstall runs. A successful WinPE deployment only confirms staging. Keep [safe outcome evidence](../../troubleshooting/logs-and-support.md#domain-join-evidence) if joining or placement reports a warning.
