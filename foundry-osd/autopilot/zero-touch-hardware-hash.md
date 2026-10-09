# Zero-touch hardware hash upload

**Zero-touch hardware hash upload** registers each device in Windows Autopilot during the deployment, with nobody signing in on the device. The deployment media carries a certificate that lets Foundry Deploy upload the hardware hash to your tenant.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-autopilot-zero-touch-01-readiness.png`
- **Capture:** Show the **Zero-touch hardware hash upload** page of a release build, connected, with **Status** at **Ready**, one certificate in **Provisioned certificates** and "Certificate ready for boot media generation." under **Boot media certificate**. Use demonstration identifiers for the tenant and the certificate, and hide the whole **Tenant ID** and **Client ID** values, the **Thumbprint** and the **Certificate ID**.
{% endhint %}

## Before you start

- **An account for the sign-in on the workstation.** With it, Foundry OSD creates an app registration and an enterprise application in your tenant and grants admin consent for one permission. Check with your tenant administrator which account may do this: the permissions the sign-in asks for are listed under [Windows Autopilot credentials](../../reference/security-and-credentials.md#windows-autopilot-credentials).
- **The Deployment Tools of the Windows ADK.** Media creation takes `oa3tool.exe` from them. See [ADK](../adk.md).
- **A safe place for the certificate file and its password**, such as a password vault.
- **Password protection, recommended.** This mode works without it, but anyone who holds the media can then use the certificate it carries. See [Password protection](../general.md#password-protection).
- **Network in Windows PE** to the Microsoft sign-in and Microsoft Graph hosts of [Network endpoints](../../reference/network-endpoints.md).

## Configure zero-touch upload

1. Open **Windows Autopilot > Zero-Touch** and select **Enable**.
2. Select **Connect tenant** and complete the sign-in in the browser. Foundry OSD asks nothing else: it finds or creates the app registration `Foundry OSD Autopilot Registration` in your tenant and gives it one Microsoft Graph application permission, `DeviceManagementServiceConfig.ReadWrite.All`.
3. Under **Certificate actions**, choose a validity of **1 month**, **3 months**, **6 months** (the default) or **12 months**, select **Create certificate** and choose where to save the `.pfx` file.
4. In the **Certificate ready** dialog, store the **PFX file** and the **PFX password** in your safe place before you close it. **Copy password** copies the password.
5. Check that **Status** reads **Ready** in **Tenant readiness** and that **Boot media certificate** reads "Certificate ready for boot media generation." The new certificate is selected for you.
6. In **Default group tag**, choose the tag that Foundry Deploy preselects, or keep **None**.
7. Create or update the deployment media from [Start](../media/README.md).

{% hint style="warning" %}
Store the password while the **Certificate ready** dialog shows it. Foundry OSD keeps it with your configuration only when **Remember passwords** is on: see [what is remembered](../deployment-profiles.md#what-is-remembered). If you lose it, remove the certificate and create another one.
{% endhint %}

**Tenant readiness** has four rows:

| Row | Shows |
| --- | --- |
| **Managed app registration** | "Foundry OSD Autopilot Registration is configured." |
| **Tenant ID** | The tenant you signed in to |
| **Client ID** | The application ID of the app registration |
| **Status** | **Ready**, or **Not ready** while the app registration has no valid certificate or another requirement is missing |

A permission, consent or enterprise application problem has no row of its own: a dialog titled **Tenant onboarding requires attention** names it after the sign-in.

## If the certificate is no longer selected

Foundry OSD keeps the tenant, the app registration and the default group tag. While **Remember passwords** is on, which is the default, it also saves a copy of the PFX and its password with the configuration, so the certificate is still selected when you start the app again: see [What is remembered](../deployment-profiles.md#what-is-remembered).

The certificate is no longer selected after **Disconnect tenant**, when **Remember passwords** is off, or in a configuration that came from another PC without its passwords and confidential files. Then open **Windows Autopilot > Zero-Touch**, select **Select PFX**, choose the file and type **PFX password**. You do not sign in again while the certificate is valid.

<details>

<summary>Renew, remove or bring your own certificate</summary>

These actions need a connected page: the button of **Tenant connection** reads **Disconnect tenant** when it is. Otherwise select **Connect tenant**.

**Renew.** A certificate cannot be extended. Before it expires, create a new certificate, then create or update every media that must keep uploading.

**Remove.** To withdraw a certificate, for example after losing media, select its row in **Provisioned certificates**, then **Remove certificate**, and confirm. Media built with a removed or expired certificate no longer uploads; the deployment itself still completes.

**Bring your own certificate.**

1. Do steps 1 and 2 of the procedure above so that the app registration exists.
2. In the Microsoft Entra admin center, upload the public certificate (`.cer`, `.pem` or `.crt`) to the app registration `Foundry OSD Autopilot Registration`, with exactly `Foundry OSD Autopilot Registration` as its description. Foundry lists only certificates with this description. See [Add and manage application credentials in Microsoft Entra ID](https://learn.microsoft.com/en-us/entra/identity-platform/how-to-add-credentials).
3. In Foundry OSD, reconnect: select **Disconnect tenant** if the page is connected, then **Connect tenant**. The certificate appears in **Provisioned certificates**.
4. Select **Select PFX**, choose the password-protected PFX that holds the private key and type **PFX password**.

Foundry checks that the PFX opens with the password, contains a private key, has not expired and matches a listed certificate. Never upload the PFX itself to Microsoft Entra.

</details>

## What the technician sees

Foundry Deploy shows an **Autopilot** step where the technician can change the group tag. The upload runs near the end of the deployment, in the step **Register Autopilot device**, and waits up to 10 minutes for the device to appear in your tenant. Most failures of the hardware hash capture or upload only skip that step, and the deployment still completes. Three capture failures, about a Windows file named `PCPKsp.dll` that is missing or unusable, stop the deployment. See [Windows Autopilot step](../../foundry-deploy/autopilot.md).

## Check the result

- On **Start**, the **Zero-Touch** row of the **Windows Autopilot** card reads "Enabled: zero-touch hardware hash upload".
- After a deployment, find the device by its serial number among the Windows Autopilot devices of your tenant, with its group tag. Foundry waits for the device to be listed, not for a profile to be assigned to it.

## Limits

- **Group tags come from the tenant.** The lists in Foundry OSD and Foundry Deploy hold only tags already carried by a Windows Autopilot device of your tenant. Nobody can type a new tag in this mode.
- **None clears a tag.** A device already registered with a group tag loses it when it is deployed with **None**.
- **An existing registration keeps its hash.** For a serial number that is already registered, Foundry updates the group tag and the name, not the stored hardware hash. After a motherboard replacement, delete the old registration first: see [Windows Autopilot motherboard replacement](https://learn.microsoft.com/en-us/autopilot/autopilot-motherboard-replacement).
- **The hardware hash stays on the device** after the deployment, in the folder described under [Evidence files](../../troubleshooting/autopilot/during-deployment.md#evidence-files).
- To send the computer name as well, turn on **Upload computer name to Autopilot** on [Machine naming](../customization/machine-naming.md).

## Related

- [Windows Autopilot step](../../foundry-deploy/autopilot.md)
- [Windows Autopilot](README.md), for the rules shared by the three modes
- [Security and credentials](../../reference/security-and-credentials.md)
- [Windows Autopilot troubleshooting](../../troubleshooting/autopilot.md)
