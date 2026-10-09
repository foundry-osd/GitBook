# Windows Autopilot troubleshooting

Start from where the problem shows. This page covers what the administrator meets in Foundry OSD while preparing the media; two other pages cover the deployment and what follows the restart.

| Where the problem shows | Go to |
| --- | --- |
| Foundry OSD: a Windows Autopilot page, **Start**, or media creation | [The index of this page](#on-this-page) |
| Foundry Deploy: the **Autopilot** step of the wizard, or the Autopilot step in **Steps** | [During deployment](autopilot/during-deployment.md) |
| Windows setup after the restart, or your tenant once the device is registered | [After the restart](autopilot/after-the-restart.md) |

To know which mode is in use, read the **Windows Autopilot** card on **Start** in Foundry OSD, or **Provisioning method** under **Autopilot** on **Summary** in Foundry Deploy.

## On this page <a href="#on-this-page" id="on-this-page"></a>

**Collect** names a line of the Foundry OSD log: see [Log locations](logs-and-support.md#log-locations). Never send a PFX file, its password or a tenant identifier.

| What you see | Go to |
| --- | --- |
| Buttons or whole cards of a Windows Autopilot page are unavailable | Select **Enable** in the header of the page, then **Connect tenant** on **Zero-Touch**. See the [rules for the three modes](../foundry-osd/autopilot/README.md). |
| "Autopilot import failed" | [Import](#autopilot-import-failed) |
| "Autopilot download failed" or "No Autopilot profiles found" | [Download](#autopilot-download-failed) |
| "Tenant onboarding failed" | [Connection failed](#tenant-onboarding-failed) |
| "Tenant onboarding requires attention", or **Status** stays **Not ready** | [Connection incomplete](#tenant-onboarding-requires-attention) |
| "Certificate creation failed", "Certificate removal failed", or a PFX password you did not keep | [Certificates](#certificate-creation-failed) |
| A group tag is missing from a list, in Foundry OSD, Foundry Deploy or the upload window | [Group tags](#group-tag-not-offered) |
| On **Start**, a sentence that starts with "Hardware hash upload is enabled but", "Autopilot JSON profile mode is enabled but" or "Autopilot uses an unsupported" | [Start](#start-blocked) |
| A line under **Boot media certificate** other than "Certificate ready for boot media generation." | [Start](#start-blocked) |
| "Deploy configuration generation is not ready." while Windows Autopilot and local accounts are configured | [Local accounts](#local-accounts) |
| "OA3Tool executable was not found for the selected WinPE architecture." | [OA3Tool](#oa3tool-not-found) |

## "Autopilot import failed" <a href="#autopilot-import-failed" id="autopilot-import-failed"></a>

- **Where:** **Windows Autopilot > JSON profile**, after **Import profile**.
- **Cause:** the dialog gives the reason.
  - "The selected Autopilot JSON file is empty."
  - "The selected Autopilot JSON file contains non-ASCII characters.": the file holds a character such as an accented letter, for example in a comment or a name.
  - A message from the JSON reader: the file is not valid JSON, for example because it is cut short.
  - A file access message: the file could not be read.
- **Fix:** export the profile again, replace every non-ASCII character, then import the file again.
- **Collect:** the line "Autopilot profile import failed."

Foundry does not check that the file is an Autopilot profile: a valid JSON file of another kind is imported without a message.

## "Autopilot download failed" <a href="#autopilot-download-failed" id="autopilot-download-failed"></a>

- **Where:** **Windows Autopilot > JSON profile**, after **Download from tenant**.
- **Cause:**
  - "Autopilot profile download failed: Microsoft Graph request failed for '\<request>' with status code 403.": the account you signed in with may not read the Windows Autopilot deployment profiles (`DeviceManagementServiceConfig.Read.All`). Another status code means that Microsoft Graph refused the request or was unavailable.
  - "Autopilot profile download failed: Microsoft Graph did not return a verified domain for the signed-in tenant.": Foundry needs a verified domain of the tenant to write the profile.
  - A dialog titled "No Autopilot profiles found" ("No Autopilot profiles were returned by the tenant."): the tenant has no Windows Autopilot deployment profile, or you signed in to another tenant than intended.
- **Fix:**
  1. Sign in to the right tenant with an account that can read its Windows Autopilot deployment profiles.
  2. Check that the workstation reaches the hosts of [Network endpoints](../reference/network-endpoints.md).
  3. Otherwise, use **Import profile** with a profile file.
- **Collect:** the line "Autopilot tenant download failed."

## "Tenant onboarding failed" <a href="#tenant-onboarding-failed" id="tenant-onboarding-failed"></a>

- **Where:** **Windows Autopilot > Zero-Touch**, after **Connect tenant**. The page stays **Not connected**.
- **Cause:** the dialog reads "Foundry could not configure the Autopilot app registration. \<reason>", where the reason is usually "Microsoft Graph \<method> request failed with status code \<code>. ErrorCode=\<error>." The account may not read the organization, list or create app registrations, or create the enterprise application; the sign-in did not complete; or the workstation cannot reach Microsoft Graph.
- **Fix:**
  1. Select **Connect tenant** again and sign in with an account that can manage app registrations and enterprise applications.
  2. Check that the workstation reaches the hosts of [Network endpoints](../reference/network-endpoints.md).
- **Collect:** the line "Autopilot hardware hash tenant onboarding failed."

## "Tenant onboarding requires attention" <a href="#tenant-onboarding-requires-attention" id="tenant-onboarding-requires-attention"></a>

- **Where:** **Windows Autopilot > Zero-Touch**, after **Connect tenant**. The page is **Connected** and **Status** reads **Not ready**.
- **Cause and fix:** the dialog gives one of these messages. To reconnect, select **Disconnect tenant**, then **Connect tenant**.
  - "The managed app registration is missing the required Microsoft Graph application permission. Reconnect with an account that can update app registrations.": your account could not add the permission. Reconnect with an account that can.
  - "The managed service principal is missing admin consent for the required Microsoft Graph permission. Grant admin consent and reconnect.": your account could not grant it. In the Microsoft Entra admin center, grant admin consent for `DeviceManagementServiceConfig.ReadWrite.All` to `Foundry OSD Autopilot Registration`, then reconnect.
  - "The managed service principal is missing or disabled. Reconnect with an account that can create or manage enterprise applications.": the enterprise application of the app registration is disabled. Enable it, then reconnect.
  - "The previously selected certificate no longer exists in the managed app registration. Select a valid PFX that matches a provisioned certificate.": the certificate was removed and no other valid one is left. Select **Create certificate**.
  - "The selected certificate has expired. Create a new certificate, save its PFX and password, then select it for boot media.": select **Create certificate**.
  - **Status** reads **Not ready** and no dialog opened: the app registration has no valid certificate yet. Select **Create certificate**.
- **Collect:** the line that starts with "Autopilot hardware hash tenant onboarding updated. Status=".

## "Certificate creation failed" <a href="#certificate-creation-failed" id="certificate-creation-failed"></a>

- **Where:** **Windows Autopilot > Zero-Touch**, after **Create certificate**. After **Remove certificate**, the dialog is titled "Certificate removal failed".
- **Cause:** the dialog reads "Foundry could not create the app registration certificate. \<reason>" or "Foundry could not remove the selected app registration certificate. \<reason>".
  - Microsoft Graph refused the change. A PFX file that was just written is then deleted.
  - The PFX file could not be written to the folder you chose.
  - "Connect to the tenant before managing Autopilot certificates.": the connection of the session is gone.
- **Fix:** select **Disconnect tenant**, then **Connect tenant**, and try again. Save the PFX to a folder you can write to.
- **Collect:** the line "Autopilot hardware hash certificate creation failed." or "Autopilot hardware hash certificate retirement failed."

**If you did not keep the PFX password:** Foundry shows it once and does not store it. With the page connected, select the certificate in **Provisioned certificates**, then **Remove certificate**, then **Create certificate**, and store the PFX file and its password before you close the dialog.

## A group tag you expect is not offered <a href="#group-tag-not-offered" id="group-tag-not-offered"></a>

- **Where:** **Default group tag** on **Windows Autopilot > Zero-Touch**, which then offers only **None**; **Group tag** in the **Autopilot** step of Foundry Deploy; **Group tag** in the upload window of the interactive mode, which then offers only **None** and **Custom**.
- **Cause:**
  - No Windows Autopilot device of the tenant carries the tag. The lists hold only tags already in use. Only the upload window accepts a new tag, through **Custom**.
  - Foundry could not read the tags of the tenant. Foundry OSD then shows only **None**, without a message. Foundry Deploy reads the tags for 30 seconds when it starts and otherwise keeps those saved on the media.
  - In Foundry OSD, a default tag that no device carries any more is reset to **None** at the next connection.
- **Fix:**
  1. Give the tag to at least one Windows Autopilot device in your tenant.
  2. In Foundry OSD, select **Disconnect tenant**, then **Connect tenant**, choose the tag and update the media.
  3. In Foundry Deploy, restore the network and start the device from the media again before you deploy.
  4. In the upload window, choose **Custom** and type the tag. With **Custom** and an empty box, the device is uploaded without a tag.
- **Collect:** the line that starts with "Autopilot group tag discovery failed" in the Foundry OSD log, with "Optional Autopilot group tag discovery" in `FoundryDeploy.log`, or with "Group tag discovery failed" in `registration.log`.

## Start blocks media creation for a Windows Autopilot reason <a href="#start-blocked" id="start-blocked"></a>

- **Where:** **Start**, on the row of the active mode in the **Windows Autopilot** card, and in the **ISO creation is blocked** or **USB creation is blocked** dialog.
- **Cause and fix:** find the message in the first table. The fixes of the zero-touch messages are done on **Windows Autopilot > Zero-Touch**, where the line under **Boot media certificate** says more about a PFX: see the second table.
- **Collect:** the Foundry OSD log, if the message stays after the fix.

| Message on **Start** | Cause | Fix |
| --- | --- | --- |
| "Autopilot uses an unsupported provisioning mode." | The saved configuration names a mode that this version of Foundry OSD does not know. | Open the Windows Autopilot page of the mode you want and select the button of its header until it reads **Disable**. |
| "Autopilot JSON profile mode is enabled but no valid default profile is selected." | **Imported profiles** is empty, or nothing is chosen in **Default profile**. | On **JSON profile**, import or download a profile and choose it in **Default profile**. |
| "Hardware hash upload is enabled but settings are missing." | Nothing is configured on **Zero-Touch** yet. | Select **Connect tenant**, then create a certificate. |
| "Hardware hash upload is enabled but the tenant connection is missing. Connect to the tenant from Autopilot." | The page was never connected to a tenant. | Select **Connect tenant**, then create a certificate. |
| "Hardware hash upload is enabled but the app registration is not configured. Connect to the tenant from Autopilot." | Foundry OSD knows the tenant but holds no app registration for it. | Reconnect: select **Disconnect tenant** if the page is connected, then **Connect tenant**. |
| "Hardware hash upload is enabled but the app client ID is missing. Reconnect to the tenant from Autopilot." | The saved app registration has no **Client ID**. | Reconnect: select **Disconnect tenant** if the page is connected, then **Connect tenant**. |
| "Hardware hash upload is enabled but the app service principal is missing or not ready." | Foundry OSD holds no enterprise application for the app registration. | Reconnect: select **Disconnect tenant** if the page is connected, then **Connect tenant**. If a dialog opens, see ["Tenant onboarding requires attention"](#tenant-onboarding-requires-attention). |
| "Hardware hash upload is enabled but no boot media PFX is selected." | No PFX is selected for this session. This is normal after you reopen Foundry OSD, select **Disconnect tenant** or change the certificate: the PFX path and its password are not kept. | Select **Select PFX** and type **PFX password**. If **Boot media certificate** is not shown, select **Connect tenant** first. |
| "Hardware hash upload is enabled but the boot media PFX password is missing." | A PFX is selected and **PFX password** is empty. | Type **PFX password**. |
| "Hardware hash upload is enabled but the boot media PFX has not been validated." | The selected PFX could not be opened: the password is wrong, the file is missing or it is not a usable PFX. | Read the line under **Boot media certificate** and find it in the second table. |
| "Hardware hash upload is enabled but the selected PFX does not match the active certificate." | The PFX opens, but it is not the PFX of a certificate listed for the app registration, or not the one used in the previous session. | Select the PFX saved when the certificate was created, or create a new certificate. |
| "Hardware hash upload is enabled but the boot media PFX expiration could not be validated." | Foundry OSD holds no expiration date for the selected PFX. | Select **Select PFX** and choose the file again. |
| "Hardware hash upload is enabled but the selected boot media PFX has expired." | The certificate in the PFX is past its expiration. | With the page connected, create a new certificate and remove the expired one. |
| "Hardware hash upload is enabled but the selected PFX does not match an app registration certificate." | Foundry OSD has no record of the certificate that the PFX belongs to. | Reconnect, then select the PFX again. If **Provisioned certificates** does not list its certificate, create a new one. |
| "Hardware hash upload is enabled but the selected certificate thumbprint is missing." | The saved record of the selected certificate is incomplete. | Reconnect, then select the PFX again. |
| "Hardware hash upload is enabled but the selected certificate expiration is missing." | The saved record of the selected certificate has no expiration date. | Reconnect, then select the PFX again. |
| "Hardware hash upload is enabled but the selected certificate has expired. Select a valid certificate before creating boot media." | The certificate registered in the app registration is past its expiration. | With the page connected, create a new certificate and remove the expired one. Then create or update every media built with it. |

| Line under **Boot media certificate** | Cause | Fix |
| --- | --- | --- |
| "Select the matching password-protected PFX before creating boot media." | No PFX is selected. | Select **Select PFX** and type **PFX password**. |
| "The selected PFX file no longer exists." | The file was moved, or its drive is not connected. | Select the file again. |
| "Enter the PFX password." | The password box is empty. | Type the password stored with this PFX. |
| "The selected PFX could not be opened with the provided password." | The password is wrong, or the file is not a PFX. | Type the password stored with this PFX, or select the right file. |
| "The selected PFX does not contain private key material." | The file holds the certificate without its private key. | Select the PFX saved when the certificate was created. |
| "The PFX certificate thumbprint does not match the selected app registration certificate." | The PFX belongs to none of the certificates in **Provisioned certificates**. A certificate you uploaded yourself is listed only when its description is exactly `Foundry OSD Autopilot Registration`. | Select the PFX of a listed certificate, or correct the description in Microsoft Entra and reconnect. |
| "Select a PFX that matches an app registration certificate." | No certificate is chosen for the media yet. | Select the PFX of a listed certificate. |
| "The selected certificate has expired. Create or select a valid certificate before creating boot media." | The certificate is past its expiration. | Create a new certificate. |

## Media creation is blocked by additional local accounts <a href="#local-accounts" id="local-accounts"></a>

- **Where:** **Start**. The blocked dialog lists "Deploy configuration generation is not ready.", the **OOBE** row reads **Needs attention**, and the **OOBE** page shows "Autopilot cannot be combined with additional local accounts."
- **Cause:** a Windows Autopilot mode is enabled and **OOBE** has additional local accounts.
- **Fix:** remove the additional accounts on [OOBE](../foundry-osd/customization/oobe.md), or select **Disable** on the Windows Autopilot page.
- **Collect:** nothing. The same line in the dialog has other causes: see [The button stays unavailable](media-creation.md#button-unavailable).

## "OA3Tool executable was not found for the selected WinPE architecture." <a href="#oa3tool-not-found" id="oa3tool-not-found"></a>

- **Where:** **Start**, when media creation ends, after "Final media creation failed." and before "Expected OA3Tool under ADK Deployment Tools for '\<architecture>'." Zero-touch mode only.
- **Cause:** `oa3tool.exe` is missing from the Deployment Tools of the Windows ADK for the architecture of the media.
- **Fix:** install or repair the Deployment Tools from [ADK](../foundry-osd/adk.md), then create the media again.
- **Collect:** the Foundry OSD log.
