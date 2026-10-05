# Domain Join during deployment

Use media configured for [Interactive](../foundry-osd/domain-join/interactive.md) or [Zero-touch](../foundry-osd/domain-join/zero-touch.md) Domain Join. Obtain the intended domain, OU and unique computer name from the administrator. Installed Windows needs domain DNS, controller access and the appropriate account permissions.

## Review the target and Windows

1. Confirm the [target disk and final computer name](target.md). Native naming uses Foundry's validated name. A custom answer file must contain exactly one valid concrete `ComputerName` in an applicable `Microsoft-Windows-Shell-Setup` `specialize` component, and no `Microsoft-Windows-UnattendedJoin` component. Missing, wildcard, invalid or multiple names cannot supply this identity.
2. Select the intended Windows image and edition. Known Home-family editions skip Domain Join with a warning while installation continues; unfamiliar or missing edition metadata is deferred to image/native inspection.
3. Complete the **Domain join** step when the wizard shows it, as described below.
4. On **Summary**, review the **Domain join** category: it shows the domain name and the OU the join will use. Choose its edit action to return to the step.

The wizard does not contact AD. Valid syntax and a successful unlock do not prove that the supplied account can join or place the computer.

## Complete the Domain join step

The **Domain join** step sits between **Drivers** and **Summary**. It appears only when there is something to enter or choose, and **Next** stays unavailable until the inputs are valid.

For Interactive mode, enter **Domain name**, **Account (DOMAIN\user or user@domain)** and **Password**. A domain name set in Foundry OSD is prefilled and remains editable. An invalid domain name or account is flagged under its field.

For Zero-touch, use the existing Protected deployment unlock. The encrypted account and password are checked against the domain and account they were saved for. There is no password prompt; the step appears only when technician OU choice is enabled, and then shows the domain name as read-only with the OU choice.

| OU configuration | Technician action |
| --- | --- |
| OU list with technician choice enabled | Choose a listed **Organizational unit**. The default OU is preselected; **Next** requires a selection. |
| OU list with technician choice disabled | No OU field. The configured default is fixed; without a default, use the domain's default location. |
| Interactive without a usable OU list | Enter **OU distinguished name (optional)** inside the entered domain, or leave it empty. |
| Zero-touch without technician choice | No step; the default OU or the domain's default location is used. |

Changing the domain in Interactive mode hides listed OUs that belong to another domain and offers the typed OU field instead. When an OU list applies, a typed OU is not accepted.

The account and password never appear in the summary or in **Confirm disk erase**. The password is kept only in memory while you finish the wizard and is cleared once deployment starts. Known Home-family editions skip the step; the summary then says the edition cannot join a domain.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-deploy-domain-join-01-interactive-ou.png`
- **Capture:** Show the Domain join wizard step in Interactive mode with the OU choice, using sanitized demonstration data and empty credential fields.
{% endhint %}

## Follow staging and first boot

Deploy stages the operation-owned credential payload and execution records. Its success page confirms preparation, not AD membership. Known edition or applied-image composition incompatibilities can skip domain work and preserve a warning in Deploy's summary/logs. Runtime, journal-integrity or protected-publication failures still follow their existing deployment policy.

In installed Windows, PostInstall runs joining and placement after deferred drivers and networking, before the remaining built-ins and custom actions. When joining succeeds, it requests a controlled restart even if OU placement fails, then verifies the expected local name and domain without credentials.

A selected OU is used for a new account. A safely identified reused account is moved within the same domain without deletion/recreation, preserving its GUID. If the worker cannot prove the object's identity or the OU, it reports a separate placement warning rather than moving an unproven account. Without an OU, it does not relocate a reused account.

## Confirm the result

Check [deployment verification](verify-deployment.md#domain-join) and [domain outcome evidence](../troubleshooting/logs-and-support.md#domain-join-evidence). Joining, OU placement/directory readback, local membership, restart and credential cleanup have independent outcomes.

Join or placement failure, controlled interruption and domain-only cleanup failure warn and continue. **Unknown** means mutation may have occurred; Foundry does not automatically repeat joining or moving. Membership can later succeed while the earlier mutation remains Unknown. Resolve warnings and pending credentials with the administrator before handoff; see [Domain Join troubleshooting](../troubleshooting/domain-join.md).
