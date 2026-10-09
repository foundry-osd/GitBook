# Domain Join

**Domain Join** joins the target device to an Active Directory domain during deployment, before the first sign-in. Foundry performs the join in installed Windows, after the restart, with a join account: an Active Directory account allowed to add computers to the domain.

Two modes differ in who supplies the join account.

| | [Zero-touch](zero-touch.md) | [Interactive](interactive.md) |
| --- | --- | --- |
| Join account | Stored on the media, encrypted with the Deployment password | Typed by the technician at each deployment |
| [Password protection](../general.md#password-protection) | Required | Not required |
| Domains listed in Foundry OSD | At least one | Optional; with none, the technician types the domain name |
| Organizational unit (OU) typed by the technician | Never | Allowed for a domain that lists no OU |

Both modes share the same lists of [domains and OUs](domains-and-ous.md). The technician's side is the [Domain Join step](../../foundry-deploy/domain-join.md) of Foundry Deploy; failures are in [Domain Join troubleshooting](../../troubleshooting/domain-join.md).

Domain Join and [Windows Autopilot](../autopilot/README.md) exclude each other: enabling one mode asks you to confirm before it replaces the active one.

## Before you start

These requirements apply to both modes.

- **Network at join time.** The join runs in installed Windows, not in Windows PE. The device must then reach the domain's DNS servers and a domain controller. The Internet check in Foundry Connect does not test this.
- **Join account.** Ask your Active Directory administrator for an account that may add computers to the domain, reuse an existing computer account when a device is redeployed under the same name, and move computer accounts to the OUs you list. Foundry does not check these permissions.
- **Computer name.** Each device needs its own name, because the name identifies its computer account. See [Machine naming](../customization/machine-naming.md).
- **Windows edition.** Windows Home editions cannot join a domain: Foundry skips the join and the installation continues. See [Supported versions](../../reference/supported-versions.md).
- **Custom answer file.** It must meet extra rules when Domain Join is active. See [Unattend](../customization/unattend.md#what-a-custom-answer-file-overrides).

Test the join on one device first: a wrong account or an unreachable domain only shows after Windows is installed.
