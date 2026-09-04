# Out-of-box experience

Use OOBE settings to control the Windows out-of-box experience after deployment.

## Configure OOBE behavior

1. Open **Customization > OOBE**.
2. Review each available OOBE option.
3. Enable only settings approved by the organization.
4. Return to **Start** and confirm customization readiness.

<figure>
  <img src="../../.gitbook/assets/foundry-osd-customization-oobe-01-options.png" alt="Foundry OSD Windows out-of-box experience options">
  <figcaption>Select the OOBE behavior approved for the deployment standard.</figcaption>
</figure>

Test the result with the same Windows edition and provisioning method used in production. Windows release changes can alter OOBE behavior.

## Configure local accounts

Foundry can prepare local accounts as part of the unattended Windows setup. These settings are not available when Windows Autopilot is configured for the same deployment.

### Built-in Administrator

Enable **Built-in Administrator** to activate the Windows built-in Administrator account. The **Set a password** option is enabled by default when the account is activated.

- Keep **Set a password** enabled and enter the same value in both fields to assign a predefined password.
- Turn **Set a password** off to assign an intentional blank password. Turning it off clears any password entered during the current session.

{% hint style="danger" %}
A blank Administrator password provides substantially less protection. Use it only when the deployment standard explicitly requires it and the device is secured by other controls.
{% endhint %}

### Additional local accounts

Select **Add account** for each additional local account required on the device, then configure:

- A unique Windows local username.
- The account type: **Standard user** or **Administrator**.
- Whether to set a predefined password. **Set a password** is enabled by default for new accounts; turn it off to assign an intentional blank password.

Edit or remove an account from the account list before creating the deployment media.

## Account creation during OOBE

When at least one additional local account is configured, Foundry automatically skips the OOBE account-creation flow. The indicator is read-only because this behavior is required for unattended account provisioning.

Enabling only the built-in Administrator account does not guarantee that Windows client OOBE will skip account creation. In that configuration, Windows may still show its normal account-creation experience.

Foundry does not configure AutoLogon and does not request account passwords during deployment. After deployment and OOBE processing, the device remains at the Windows sign-in screen.

## Password protection

Non-empty local account passwords require [Protected deployment](../general.md#protected-deployment). Foundry keeps authoring passwords only for the current session and encrypts them in the deployment configuration written to the media.

If a predefined account password is enabled while Protected deployment is disabled, Foundry blocks media creation and marks both **OOBE** and **Password protection** as needing attention. Enable Protected deployment and configure its technician password, or turn off the predefined account password to use an intentional blank password.

Foundry saves whether each account requires a predefined password, but it never saves the password itself in the authoring configuration. After restarting Foundry OSD, re-enter and confirm every required account password before creating deployment media. Foundry marks the OOBE configuration as needing attention until those passwords are available again.

Blank passwords do not require Protected deployment because no password secret is stored. Apply the organization's password and device-access policies before using this option.
