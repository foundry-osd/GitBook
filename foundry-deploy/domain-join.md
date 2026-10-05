# Domain Join during deployment

Use media configured for [Interactive](../foundry-osd/domain-join/interactive.md) or [Zero-touch](../foundry-osd/domain-join/zero-touch.md) Domain Join. Obtain the intended domain, destination and unique computer name from the administrator. Installed Windows needs domain DNS, controller access and the appropriate account permissions.

## Review the target and Windows

1. Confirm the [target disk and final computer name](target.md). Native naming uses Foundry's validated name. A custom answer file must contain exactly one valid concrete `ComputerName` in an applicable `Microsoft-Windows-Shell-Setup` `specialize` component, and no `Microsoft-Windows-UnattendedJoin` component. Missing, wildcard, invalid or multiple names cannot supply this identity.
2. Select the intended Windows image and edition. Known Home-family editions skip Domain Join with a warning while installation continues; unfamiliar or missing edition metadata is deferred to image/native inspection.
3. Review the wizard summary's authored domain and default destination. It precedes deployment-time input and does not reflect an OU chosen later in the launch dialog.
4. Start deployment to prepare domain input before **Confirm disk erase**.

The dialog preparation does not contact AD. Valid syntax and successful unlock do not prove that the supplied account can join or place the computer.

## Supply interactive input or select a destination

For Interactive mode, **Domain join** asks for **Domain name**, **Account (DOMAIN\user or user@domain)** and **Password**. A prefilled domain remains editable.

For Zero-touch, use the existing Protected deployment unlock. The encrypted account/password are checked against their authored domain/account context. There is no new password prompt; the dialog appears only for enabled destination selection.

| Destination policy | Technician action |
| --- | --- |
| Compatible catalog with selection enabled | Choose a listed **Organizational unit**. The authored default is preselected; **Continue** requires a selected row. |
| Compatible catalog with selection disabled | No destination editor. The configured default is fixed; without a default, use Windows' default destination. |
| Interactive without a compatible catalog | Enter **OU distinguished name (optional)** inside the submitted domain, or leave it empty. |
| Zero-touch without selection | No dialog; use the authored default or Windows' default destination. |

Changing an interactive domain clears destination input and suppresses incompatible catalog choices. A compatible catalog does not allow arbitrary typed destinations. Select **Continue**, then review the actual domain and destination in **Confirm disk erase**. Account and password are not included in the summary or confirmation. **Cancel** leaves deployment unstarted.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-deploy-domain-join-01-interactive-destination.png`
- **Capture:** Show the Domain join dialog and optional no-catalog DN editor using sanitized demonstration data, with credential fields empty.
{% endhint %}

## Follow staging and first boot

Deploy stages the operation-owned credential payload and execution records. Its success page confirms preparation, not AD membership. Known edition or applied-image composition incompatibilities can skip domain work and preserve a warning in Deploy's summary/logs. Runtime, journal-integrity or protected-publication failures still follow their existing deployment policy.

In installed Windows, PostInstall runs joining and placement after deferred drivers and networking, before the remaining built-ins and custom actions. When joining succeeds, it requests a controlled restart even if OU placement fails, then verifies the expected local name and domain without credentials.

A selected destination is used for a new account. A safely identified reused account is moved within the same domain without deletion/recreation, preserving its GUID. If the worker cannot prove the object's identity or destination, it reports a separate placement warning rather than moving an unproven account. Without a destination, it does not relocate a reused account.

## Confirm the result

Check [deployment verification](verify-deployment.md#domain-join) and [domain outcome evidence](../troubleshooting/logs-and-support.md#domain-join-evidence). Joining, OU placement/directory readback, local membership, restart and credential cleanup have independent outcomes.

Join or placement failure, controlled interruption and domain-only cleanup failure warn and continue. **Unknown** means mutation may have occurred; Foundry does not automatically repeat joining or moving. Membership can later succeed while the earlier mutation remains Unknown. Resolve warnings and pending credentials with the administrator before handoff; see [Domain Join troubleshooting](../troubleshooting/domain-join.md).
