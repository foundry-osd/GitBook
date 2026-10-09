# Domain Join step

On media prepared for Domain Join, Foundry Deploy shows a **Domain join** step between **Drivers** and **Summary**. You choose the domain and the organizational unit (OU) and, on interactive media, enter the join account. The join itself runs later, in installed Windows, after the restart.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-deploy-domain-join-01-interactive-ou.png`
- **Capture:** Show the **Domain join** step of a release build on interactive media, with all four fields filled with demonstration values and **Next** available. The menu bar must not show a Debug menu.
{% endhint %}

## Before you start

- On interactive media, have the join account and its password.
- Check the computer name on [Target device](target.md): it identifies the computer account, and a device redeployed under the same name reuses its account.

## Complete the step

1. **Domain name**: choose the domain when the media lists several; the default is preselected. A single listed domain cannot be changed. On interactive media that lists no domain, type its DNS name, such as `corp.contoso.com`.
2. **Account (DOMAIN\user or user@domain)** and **Password**: on interactive media, enter the join account and its password. Zero-touch media carries the account and does not show these fields.
3. **Organizational unit**: choose the OU when the domain lists several; the default, if there is one, is preselected. A domain with a single OU uses it without asking.
4. **OU distinguished name (optional)**: shown on interactive media for a domain without listed OUs. Type an OU of that domain, starting with `OU=`, or leave it empty to use the domain's default location.
5. Select **Next**. It stays unavailable while a required field is empty or a field shows a message.

Changing the domain replaces the OU choices and keeps the account and password you typed.

{% hint style="info" %}
Foundry Deploy does not contact the domain. A mistyped account or password is accepted here and only shows after the restart, as a failed join.
{% endhint %}

## When the step is not shown

- Zero-touch media with one domain and at most one OU for it leaves nothing to choose.
- A Windows Home edition cannot join a domain. **Summary** and **Confirm disk erase** then say: "This Windows edition cannot join a domain. Windows installation continues without it."

## Review on Summary

The **Domain join** category shows the **Domain name** and the **Organizational unit**; **Domain default location** means no OU applies. When the wizard has a **Domain join** step, **Edit** returns to it. The account and the password are never shown.

If the deployment does not start, see [Domain Join troubleshooting](../troubleshooting/domain-join.md). Otherwise continue with [Review and deploy](review-and-deploy.md), and come back to the checks below after the restart.

## Before you hand over the device

A deployment that succeeds in Windows PE has prepared the join, not performed it.

1. Keep power and network connected: installed Windows must reach a domain controller, and it restarts once more after a successful join.
2. Read the **Foundry Post-installation** console, which appears on screen by itself after the restart. Its two lines that start with `Domain -` and `Restart:` report the join. [Domain Join lines](after-the-restart.md#domain-join-lines) shows what they read when the join is complete, and when to read them.
3. Look at the first screen after setup. What it proves depends on the media; the administrator who created it knows which case applies.
    - No local account and no custom answer file: the Windows sign-in screen means the join is confirmed, and **Who's going to use this device?** means it failed or could not be confirmed.
    - A local account configured on [OOBE](../foundry-osd/customization/oobe.md), or a custom answer file: the first screen proves nothing. Rely on the console lines.
4. Sign in with a domain account, and have your Active Directory administrator confirm that the computer account is in the intended OU.

For any other value or a yellow line under the two lines, see [Domain Join troubleshooting](../troubleshooting/domain-join.md).
