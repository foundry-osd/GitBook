# Windows Autopilot troubleshooting

Find what you see in the tables below, then go to its section. Sections follow the order of a deployment: the Windows Autopilot pages of Foundry OSD, **Start**, Foundry Deploy, then Windows setup after the restart.

To know which mode is in use, read the **Windows Autopilot** card on **Start** in Foundry OSD, or **Provisioning method** under **Autopilot** on **Summary** in Foundry Deploy. The log files named under **Collect** are listed in [Evidence files](#evidence-files); the other logs are in [Log locations](logs-and-support.md#log-locations).

**On a Windows Autopilot page of Foundry OSD**

| What you see | Go to |
| --- | --- |
| Buttons or whole cards are unavailable | [Unavailable controls](#the-controls-of-a-windows-autopilot-page-are-unavailable) |
| "Change provisioning mode?" | [Mode change](#change-provisioning-mode) |
| "Autopilot import failed" | [Import](#autopilot-import-failed) |
| "Autopilot download failed" | [Download](#autopilot-download-failed) |
| "No Autopilot profiles found" | [No profile](#no-autopilot-profiles-found) |
| "Tenant onboarding failed" | [Connection failed](#tenant-onboarding-failed) |
| "Tenant onboarding requires attention" | [Connection incomplete](#tenant-onboarding-requires-attention) |
| **Status** reads **Not ready** and no dialog opened | [Not ready](#status-stays-not-ready-and-no-dialog-opens) |
| "Certificate creation failed" or "Certificate removal failed" | [Certificate actions](#certificate-creation-failed) |
| You did not keep the PFX password | [Lost password](#the-pfx-password-was-not-saved) |
| A line under **Boot media certificate** other than "Certificate ready for boot media generation." | [Boot media certificate](#boot-media-certificate-is-not-ready) |
| **Default group tag** offers only **None** | [Group tags](#default-group-tag-offers-only-none) |

**On Start, in the Windows Autopilot card or in the "ISO creation is blocked" or "USB creation is blocked" dialog**

The zero-touch messages all start with "Hardware hash upload is enabled"; the table shows how each one ends.

| What you see | Go to |
| --- | --- |
| "Deploy configuration generation is not ready." while Autopilot and local accounts are configured | [Local accounts](#media-creation-is-blocked-by-additional-local-accounts) |
| "Autopilot uses an unsupported provisioning mode." | [Unsupported mode](#start-unsupported-mode) |
| "Autopilot JSON profile mode is enabled but no valid default profile is selected." | [No default profile](#start-json-profile-missing) |
| "... but settings are missing." | [Settings missing](#start-settings-missing) |
| "... but the tenant connection is missing. ..." | [Tenant missing](#start-tenant-missing) |
| "... but the app registration is not configured. ..." | [App registration](#start-app-registration-missing) |
| "... but the app client ID is missing. ..." | [Client ID](#start-client-id-missing) |
| "... but the app service principal is missing or not ready." | [Service principal](#start-service-principal-missing) |
| "... but no boot media PFX is selected." | [No PFX](#start-pfx-missing) |
| "... but the boot media PFX password is missing." | [No password](#start-pfx-password-missing) |
| "... but the boot media PFX has not been validated." | [PFX not validated](#start-pfx-not-validated) |
| "... but the selected PFX does not match the active certificate." | [PFX mismatch](#start-pfx-mismatch) |
| "... but the boot media PFX expiration could not be validated." | [PFX expiration unknown](#start-pfx-expiration-missing) |
| "... but the selected boot media PFX has expired." | [PFX expired](#start-pfx-expired) |
| "... but the selected PFX does not match an app registration certificate." | [No matching certificate](#start-certificate-missing) |
| "... but the selected certificate thumbprint is missing." | [Thumbprint missing](#start-certificate-thumbprint-missing) |
| "... but the selected certificate expiration is missing." | [Expiration missing](#start-certificate-expiration-missing) |
| "... but the selected certificate has expired. ..." | [Certificate expired](#start-certificate-expired) |
| "OA3Tool executable was not found for the selected WinPE architecture." during media creation | [OA3Tool](#oa3tool-not-found) |

**In Foundry Deploy**

| What you see | Go to |
| --- | --- |
| An answer file is refused because it conflicts with Autopilot | [What a custom answer file overrides](../foundry-osd/customization/unattend.md#what-a-custom-answer-file-overrides) |
| "No embedded Autopilot profiles were found on this media." | [No profile on the media](#no-embedded-profiles) |
| A group tag is missing from the **Group tag** list | [Missing tag](#a-group-tag-is-missing-in-foundry-deploy) |
| "Hardware hash upload unavailable" | [Upload unavailable](#hardware-hash-upload-unavailable) |
| "Autopilot hardware hash upload skipped because ..." | [Upload unavailable](#hardware-hash-upload-unavailable) |
| "Autopilot hardware hash capture failed: ..." | [Capture failed](#hardware-hash-capture-failed) |
| "Autopilot hardware hash upload skipped: ..." | [Upload skipped](#hardware-hash-upload-skipped) |
| "Imported Autopilot device did not appear in Windows Autopilot devices before the timeout." | [Device not listed](#device-did-not-appear) |
| "Autopilot hardware hash import failed: ..." or "Autopilot import failed. ..." | [Import refused](#hardware-hash-import-failed) |
| "The hardware hash is visible in Windows Autopilot, but ..." | [Tag or name not set](#visible-but-not-assigned) |
| "Multiple Windows Autopilot devices matched the captured serial number ..." | [Duplicate serial number](#multiple-devices-matched) |

**In Windows setup, after the restart (interactive mode)**

| What you see | Go to |
| --- | --- |
| The upload window does not open | [No window](#the-upload-window-does-not-open-after-the-restart) |
| "Waiting for network connectivity." | [No network](#waiting-for-network-connectivity) |
| "Authentication failed. Check logs for details." | [Sign-in refused](#authentication-failed) |
| "Upload failed. Check logs for details." | [Upload failed](#upload-failed) |
| **Group tag** offers only **None** and **Custom** | [No tag listed](#the-window-lists-only-none-and-custom) |

**In your tenant, after the registration**

| What you see | Go to |
| --- | --- |
| The group tag of a device has disappeared | [Tag removed](#the-group-tag-of-a-registered-device-was-removed) |
| The computer name is not assigned | [Name](#the-computer-name-is-not-assigned) |
| The device is registered but Windows setup applies no Autopilot profile | [No profile applied](#the-device-is-registered-but-no-autopilot-profile-applies) |
| A device whose motherboard was replaced still has its old registration | [Motherboard](#the-motherboard-was-replaced) |

## The controls of a Windows Autopilot page are unavailable

- **Where:** **Windows Autopilot > JSON profile**, **Zero-Touch** or **Interactive**.
- **Cause:**
  - The mode is not enabled: the page stays unavailable until you select **Enable** in its header.
  - On **Zero-Touch**, **Tenant readiness**, **Certificate actions**, **Provisioned certificates** and **Default group tag** are shown only while the page is connected in the current session. **Remove certificate** also needs a selected row.
  - On **Zero-Touch**, **Boot media certificate** is hidden while the page is not connected and no valid certificate is saved.
- **Fix:**
  1. Select **Enable**.
  2. On **Zero-Touch**, select **Connect tenant**.
- **Collect:** nothing.

## "Change provisioning mode?" <a href="#change-provisioning-mode" id="change-provisioning-mode"></a>

- **Where:** any Windows Autopilot or Domain Join page, when you select **Enable**.
- **Cause:** another Windows Autopilot mode or a Domain Join mode is active. The dialog reads "\<active mode> is currently active. Continuing will disable it and enable \<new mode>."
- **Fix:** select **Change mode** to switch, or cancel to keep the active mode. The settings of the mode you leave are kept. Create or update the media afterwards.
- **Collect:** nothing.

## "Autopilot import failed" <a href="#autopilot-import-failed" id="autopilot-import-failed"></a>

- **Where:** **Windows Autopilot > JSON profile**, after **Import profile**.
- **Cause:** the dialog gives one of these reasons.
  - "The selected Autopilot JSON file is empty."
  - "The selected Autopilot JSON file contains non-ASCII characters.": the file holds a character such as an accented letter, for example in a comment or a name.
  - A message from the JSON reader: the file is not valid JSON, for example because it is cut short.
  - A file access message: the file could not be read.
- **Fix:**
  1. Export the profile again and check that the file is complete.
  2. Replace every non-ASCII character in the file, then import it again.
- **Collect:** the Foundry OSD log line "Autopilot profile import failed."

Foundry does not check that the file is an Autopilot profile: a valid JSON file of another kind is imported without a message.

## "Autopilot download failed" <a href="#autopilot-download-failed" id="autopilot-download-failed"></a>

- **Where:** **Windows Autopilot > JSON profile**, after **Download from tenant**.
- **Cause:** the dialog reads "Autopilot profile download failed: \<reason>".
  - "Microsoft Graph request failed for '\<request>' with status code 403.": the account you signed in with may not read the Windows Autopilot deployment profiles (`DeviceManagementServiceConfig.Read.All`).
  - Another status code: Microsoft Graph refused the request or was unavailable.
  - "Microsoft Graph did not return a verified domain for the signed-in tenant.": Foundry needs a verified domain of the tenant to write the profile.
- **Fix:**
  1. Sign in with an account that can read the Windows Autopilot deployment profiles of the tenant.
  2. Check that the workstation reaches the hosts of [Network endpoints](../reference/network-endpoints.md), then try again.
  3. Otherwise, use **Import profile** with a profile file.
- **Collect:** the Foundry OSD log line "Autopilot tenant download failed."

## "No Autopilot profiles found" <a href="#no-autopilot-profiles-found" id="no-autopilot-profiles-found"></a>

- **Where:** **Windows Autopilot > JSON profile**, after **Download from tenant**. The dialog reads "No Autopilot profiles were returned by the tenant."
- **Cause:** the tenant you signed in to has no Windows Autopilot deployment profile, or you signed in to another tenant than intended.
- **Fix:** check the account and the tenant, create the profile in your tenant, then select **Download from tenant** again.
- **Collect:** nothing.

## "Tenant onboarding failed" <a href="#tenant-onboarding-failed" id="tenant-onboarding-failed"></a>

- **Where:** **Windows Autopilot > Zero-Touch**, after **Connect tenant**. The page stays **Not connected**.
- **Cause:** the dialog reads "Foundry could not configure the Autopilot app registration. \<reason>", where the reason is usually "Microsoft Graph \<method> request failed with status code \<code>. ErrorCode=\<error>."
  - The account may not read the organization, list or create app registrations, or create the enterprise application.
  - The sign-in did not complete.
  - The workstation cannot reach Microsoft Graph.
- **Fix:**
  1. Select **Connect tenant** again and sign in with an account that can manage app registrations and enterprise applications.
  2. Check that the workstation reaches the hosts of [Network endpoints](../reference/network-endpoints.md).
- **Collect:** the Foundry OSD log line "Autopilot hardware hash tenant onboarding failed."

## "Tenant onboarding requires attention" <a href="#tenant-onboarding-requires-attention" id="tenant-onboarding-requires-attention"></a>

- **Where:** **Windows Autopilot > Zero-Touch**, after **Connect tenant**. The page is **Connected** and **Status** reads **Not ready**.
- **Cause and fix:** the dialog gives one of these messages.
  - "The managed app registration is missing the required Microsoft Graph application permission. Reconnect with an account that can update app registrations.": your account could not add the permission. Select **Disconnect tenant**, then **Connect tenant** with an account that can.
  - "The managed service principal is missing admin consent for the required Microsoft Graph permission. Grant admin consent and reconnect.": your account could not grant it. In the Microsoft Entra admin center, grant admin consent for `DeviceManagementServiceConfig.ReadWrite.All` to `Foundry OSD Autopilot Registration`, then reconnect.
  - "The managed service principal is missing or disabled. Reconnect with an account that can create or manage enterprise applications.": the enterprise application of the app registration is disabled. Enable it, then reconnect.
  - "The previously selected certificate no longer exists in the managed app registration. Select a valid PFX that matches a provisioned certificate.": the certificate was removed and no other valid one is left. Select **Create certificate**.
  - "The selected certificate has expired. Create a new certificate, save its PFX and password, then select it for boot media.": select **Create certificate**.
- **Collect:** the Foundry OSD log line that starts with "Autopilot hardware hash tenant onboarding updated. Status=".

## Status stays Not ready and no dialog opens

- **Where:** **Windows Autopilot > Zero-Touch**, **Tenant readiness**, after a connection without any dialog.
- **Cause:** the app registration has no valid certificate yet. Foundry shows no message for this state.
- **Fix:** select **Create certificate**, or register your own certificate as described in [Zero-touch hardware hash upload](../foundry-osd/autopilot/zero-touch-hardware-hash.md).
- **Collect:** nothing.

## "Certificate creation failed" <a href="#certificate-creation-failed" id="certificate-creation-failed"></a>

- **Where:** **Windows Autopilot > Zero-Touch**, after **Create certificate**. After **Remove certificate**, the dialog is titled "Certificate removal failed".
- **Cause:** the dialog reads "Foundry could not create the app registration certificate. \<reason>" or "Foundry could not remove the selected app registration certificate. \<reason>".
  - Microsoft Graph refused the change. A PFX file that was just written is then deleted.
  - The PFX file could not be written to the folder you chose.
  - "Connect to the tenant before managing Autopilot certificates.": the connection of the session is gone.
- **Fix:**
  1. Select **Disconnect tenant**, then **Connect tenant**.
  2. Create the certificate again and save the PFX to a folder you can write to.
- **Collect:** the Foundry OSD log line "Autopilot hardware hash certificate creation failed." or "Autopilot hardware hash certificate retirement failed."

## The PFX password was not saved

- **Where:** after the **Certificate ready** dialog was closed.
- **Cause:** Foundry shows the generated password once and does not store it.
- **Fix:**
  1. Select **Connect tenant**.
  2. Select the certificate in **Provisioned certificates**, then **Remove certificate**.
  3. Select **Create certificate** and store the PFX file and its password before you close the dialog.
- **Collect:** nothing.

## Boot media certificate is not ready

- **Where:** **Windows Autopilot > Zero-Touch**, the line under **PFX password** in **Boot media certificate**.
- **Cause and fix:**

| Message | Cause | Fix |
| --- | --- | --- |
| "Select the matching password-protected PFX before creating boot media." | No PFX is selected. This is the normal state after you reopen Foundry OSD or select **Disconnect tenant**. | Select **Select PFX** and type **PFX password**. |
| "The selected PFX file no longer exists." | The file was moved, or its drive is not connected. | Select the file again. |
| "Enter the PFX password." | The password box is empty. | Type **PFX password**. |
| "The selected PFX could not be opened with the provided password." | The password is wrong, or the file is not a PFX. | Type the password stored with this PFX. |
| "The selected PFX does not contain private key material." | The file holds the certificate without its private key. | Select the PFX saved when the certificate was created. |
| "The PFX certificate thumbprint does not match the selected app registration certificate." | The PFX belongs to none of the certificates in **Provisioned certificates**. A certificate you uploaded yourself is not listed unless its description is exactly `Foundry OSD Autopilot Registration`. | Select the PFX of a listed certificate, or correct the description and reconnect. |
| "Select a PFX that matches an app registration certificate." | No certificate is chosen for the media yet. | Select the PFX of a listed certificate. |
| "The selected certificate has expired. Create or select a valid certificate before creating boot media." | The certificate is past its expiration. | Select **Create certificate**. |

- **Collect:** nothing. Never send a PFX file or its password.

## Default group tag offers only None

- **Where:** **Windows Autopilot > Zero-Touch**, **Default group tag**.
- **Cause:**
  - No Windows Autopilot device of the tenant carries a group tag. The list holds only tags already in use, and a tag cannot be typed.
  - Foundry could not read the tags during the connection. It then continues without them and shows no message.
  - A default tag that no device carries any more is reset to **None** at the next connection.
- **Fix:**
  1. Give the tag to at least one Windows Autopilot device in your tenant.
  2. Select **Disconnect tenant**, then **Connect tenant**.
  3. For a tag that does not exist yet, use [Interactive hardware hash upload](../foundry-osd/autopilot/interactive-hardware-hash.md), where the technician can type it.
- **Collect:** the Foundry OSD log line that starts with "Autopilot group tag discovery failed and will be skipped."

## Media creation is blocked by additional local accounts

- **Where:** **Start**. The **ISO creation is blocked** or **USB creation is blocked** dialog lists "Deploy configuration generation is not ready.", the **OOBE** row reads **Needs attention**, and the **OOBE** page shows "Autopilot cannot be combined with additional local accounts."
- **Cause:** a Windows Autopilot mode is enabled and **OOBE** has additional local accounts.
- **Fix:** remove the additional accounts on [OOBE](../foundry-osd/customization/oobe.md), or select **Disable** on the Windows Autopilot page.
- **Collect:** nothing. The same line in the dialog has other causes: see [Media creation troubleshooting](media-creation.md).

## "Autopilot uses an unsupported provisioning mode." <a href="#start-unsupported-mode" id="start-unsupported-mode"></a>

- **Where:** **Start**, in the **Windows Autopilot** card and in the blocked dialog.
- **Cause:** the saved configuration names a Windows Autopilot mode that this version of Foundry OSD does not know.
- **Fix:** open the Windows Autopilot page of the mode you want and use the button of its header until it reads **Disable**.
- **Collect:** the [Foundry OSD log](logs-and-support.md#log-locations) if the message stays.

## "Autopilot JSON profile mode is enabled but no valid default profile is selected." <a href="#start-json-profile-missing" id="start-json-profile-missing"></a>

- **Where:** **Start**, on the **JSON profile** row of the **Windows Autopilot** card and in the blocked dialog.
- **Cause:** **JSON profile** is enabled and **Imported profiles** is empty, or no profile is chosen in **Default profile**.
- **Fix:** on **Windows Autopilot > JSON profile**, import or download a profile and choose it in **Default profile**. To deploy without Windows Autopilot, select **Disable**.
- **Collect:** the [Foundry OSD log](logs-and-support.md#log-locations) if the message stays.

## "Hardware hash upload is enabled but settings are missing." <a href="#start-settings-missing" id="start-settings-missing"></a>

- **Where:** **Start**, on the **Zero-Touch** row of the **Windows Autopilot** card and in the blocked dialog.
- **Cause:** **Zero-Touch** is enabled and nothing has been configured on its page yet.
- **Fix:** follow [Zero-touch hardware hash upload](../foundry-osd/autopilot/zero-touch-hardware-hash.md) from **Connect tenant**.
- **Collect:** the [Foundry OSD log](logs-and-support.md#log-locations) if the message stays.

## "Hardware hash upload is enabled but the tenant connection is missing. Connect to the tenant from Autopilot." <a href="#start-tenant-missing" id="start-tenant-missing"></a>

- **Where:** **Start**, on the **Zero-Touch** row of the **Windows Autopilot** card and in the blocked dialog.
- **Cause:** **Zero-Touch** is enabled and the page has never been connected to a tenant.
- **Fix:** on **Windows Autopilot > Zero-Touch**, select **Connect tenant**, then create a certificate.
- **Collect:** the [Foundry OSD log](logs-and-support.md#log-locations) if the message stays.

## "Hardware hash upload is enabled but the app registration is not configured. Connect to the tenant from Autopilot." <a href="#start-app-registration-missing" id="start-app-registration-missing"></a>

- **Where:** **Start**, on the **Zero-Touch** row of the **Windows Autopilot** card and in the blocked dialog.
- **Cause:** Foundry OSD knows the tenant but holds no app registration for it.
- **Fix:** on **Windows Autopilot > Zero-Touch**, select **Connect tenant**. If a dialog opens, see ["Tenant onboarding failed"](#tenant-onboarding-failed).
- **Collect:** the [Foundry OSD log](logs-and-support.md#log-locations) if the message stays.

## "Hardware hash upload is enabled but the app client ID is missing. Reconnect to the tenant from Autopilot." <a href="#start-client-id-missing" id="start-client-id-missing"></a>

- **Where:** **Start**, on the **Zero-Touch** row of the **Windows Autopilot** card and in the blocked dialog.
- **Cause:** the saved app registration has no **Client ID**.
- **Fix:** on **Windows Autopilot > Zero-Touch**, select **Disconnect tenant** if the page is connected, then **Connect tenant**.
- **Collect:** the [Foundry OSD log](logs-and-support.md#log-locations) if the message stays.

## "Hardware hash upload is enabled but the app service principal is missing or not ready." <a href="#start-service-principal-missing" id="start-service-principal-missing"></a>

- **Where:** **Start**, on the **Zero-Touch** row of the **Windows Autopilot** card and in the blocked dialog.
- **Cause:** Foundry OSD holds no enterprise application for the app registration.
- **Fix:** on **Windows Autopilot > Zero-Touch**, connect again with an account that can create or manage enterprise applications. See ["Tenant onboarding requires attention"](#tenant-onboarding-requires-attention).
- **Collect:** the [Foundry OSD log](logs-and-support.md#log-locations) if the message stays.

## "Hardware hash upload is enabled but no boot media PFX is selected." <a href="#start-pfx-missing" id="start-pfx-missing"></a>

- **Where:** **Start**, on the **Zero-Touch** row of the **Windows Autopilot** card and in the blocked dialog.
- **Cause:** no PFX is selected for this session. Foundry OSD does not keep the PFX path or its password when it closes, when you select **Disconnect tenant**, or when the selected certificate changes. This is the message you get most often after reopening the app.
- **Fix:** on **Windows Autopilot > Zero-Touch**, select **Select PFX**, choose the file and type **PFX password**. If **Boot media certificate** is not shown, select **Connect tenant** first.
- **Collect:** nothing.

## "Hardware hash upload is enabled but the boot media PFX password is missing." <a href="#start-pfx-password-missing" id="start-pfx-password-missing"></a>

- **Where:** **Start**, on the **Zero-Touch** row of the **Windows Autopilot** card and in the blocked dialog.
- **Cause:** a PFX is selected and **PFX password** is empty.
- **Fix:** on **Windows Autopilot > Zero-Touch**, type **PFX password**.
- **Collect:** nothing.

## "Hardware hash upload is enabled but the boot media PFX has not been validated." <a href="#start-pfx-not-validated" id="start-pfx-not-validated"></a>

- **Where:** **Start**, on the **Zero-Touch** row of the **Windows Autopilot** card and in the blocked dialog.
- **Cause:** Foundry OSD could not open the selected PFX with the password: the password is wrong, the file is missing or it is not a usable PFX.
- **Fix:** read the line under **Boot media certificate** and follow [Boot media certificate is not ready](#boot-media-certificate-is-not-ready).
- **Collect:** nothing.

## "Hardware hash upload is enabled but the selected PFX does not match the active certificate." <a href="#start-pfx-mismatch" id="start-pfx-mismatch"></a>

- **Where:** **Start**, on the **Zero-Touch** row of the **Windows Autopilot** card and in the blocked dialog.
- **Cause:** the PFX opens, but it is not the PFX of a certificate of the app registration, or not the one used in the previous session.
- **Fix:** select the PFX saved when the certificate was created. If you no longer have it, select **Connect tenant** and create a new certificate.
- **Collect:** nothing.

## "Hardware hash upload is enabled but the boot media PFX expiration could not be validated." <a href="#start-pfx-expiration-missing" id="start-pfx-expiration-missing"></a>

- **Where:** **Start**, on the **Zero-Touch** row of the **Windows Autopilot** card and in the blocked dialog.
- **Cause:** Foundry OSD holds no expiration date for the selected PFX.
- **Fix:** on **Windows Autopilot > Zero-Touch**, select **Select PFX** and choose the file again.
- **Collect:** the [Foundry OSD log](logs-and-support.md#log-locations) if the message stays.

## "Hardware hash upload is enabled but the selected boot media PFX has expired." <a href="#start-pfx-expired" id="start-pfx-expired"></a>

- **Where:** **Start**, on the **Zero-Touch** row of the **Windows Autopilot** card and in the blocked dialog.
- **Cause:** the certificate in the PFX is past its expiration.
- **Fix:** on **Windows Autopilot > Zero-Touch**, select **Connect tenant**, create a new certificate and remove the expired one.
- **Collect:** nothing.

## "Hardware hash upload is enabled but the selected PFX does not match an app registration certificate." <a href="#start-certificate-missing" id="start-certificate-missing"></a>

- **Where:** **Start**, on the **Zero-Touch** row of the **Windows Autopilot** card and in the blocked dialog.
- **Cause:** Foundry OSD has no record of the certificate of the app registration that the PFX belongs to.
- **Fix:** on **Windows Autopilot > Zero-Touch**, select **Connect tenant**, then select the PFX again. If **Provisioned certificates** does not list its certificate, create a new one.
- **Collect:** the [Foundry OSD log](logs-and-support.md#log-locations) if the message stays.

## "Hardware hash upload is enabled but the selected certificate thumbprint is missing." <a href="#start-certificate-thumbprint-missing" id="start-certificate-thumbprint-missing"></a>

- **Where:** **Start**, on the **Zero-Touch** row of the **Windows Autopilot** card and in the blocked dialog.
- **Cause:** the saved record of the selected certificate is incomplete.
- **Fix:** on **Windows Autopilot > Zero-Touch**, select **Connect tenant**, then select the PFX again.
- **Collect:** the [Foundry OSD log](logs-and-support.md#log-locations) if the message stays.

## "Hardware hash upload is enabled but the selected certificate expiration is missing." <a href="#start-certificate-expiration-missing" id="start-certificate-expiration-missing"></a>

- **Where:** **Start**, on the **Zero-Touch** row of the **Windows Autopilot** card and in the blocked dialog.
- **Cause:** the saved record of the selected certificate has no expiration date.
- **Fix:** on **Windows Autopilot > Zero-Touch**, select **Connect tenant**, then select the PFX again.
- **Collect:** the [Foundry OSD log](logs-and-support.md#log-locations) if the message stays.

## "Hardware hash upload is enabled but the selected certificate has expired. Select a valid certificate before creating boot media." <a href="#start-certificate-expired" id="start-certificate-expired"></a>

- **Where:** **Start**, on the **Zero-Touch** row of the **Windows Autopilot** card and in the blocked dialog.
- **Cause:** the certificate registered in the app registration is past its expiration.
- **Fix:** on **Windows Autopilot > Zero-Touch**, select **Connect tenant**, create a new certificate and remove the expired one. Then create or update every media built with the old certificate.
- **Collect:** nothing.

## "OA3Tool executable was not found for the selected WinPE architecture." <a href="#oa3tool-not-found" id="oa3tool-not-found"></a>

- **Where:** **Start**, when media creation ends, after "Final media creation failed." and before "Expected OA3Tool under ADK Deployment Tools for '\<architecture>'." Zero-touch mode only.
- **Cause:** `oa3tool.exe` is missing from the Deployment Tools of the Windows ADK for the architecture of the media.
- **Fix:** install or repair the Deployment Tools from [ADK](../foundry-osd/adk.md), then create the media again.
- **Collect:** the [Foundry OSD log](logs-and-support.md#log-locations).

## "No embedded Autopilot profiles were found on this media." <a href="#no-embedded-profiles" id="no-embedded-profiles"></a>

- **Where:** Foundry Deploy, **Autopilot** step, JSON profile mode. **Next** stays unavailable.
- **Cause:** Foundry Deploy found no profile on the media.
- **Fix:** the administrator checks **Imported profiles** and **Default profile** on **Windows Autopilot > JSON profile**, then creates the media again.
- **Collect:** `FoundryDeploy.log`.

## A group tag is missing in Foundry Deploy

- **Where:** Foundry Deploy, **Autopilot** step, **Group tag**, zero-touch mode.
- **Cause:**
  - The list holds **None**, the tags saved on the media and the tags read from the tenant when Foundry Deploy starts. That reading has 30 seconds; when it fails or runs out of time, Foundry Deploy opens with the saved tags only.
  - No Windows Autopilot device of the tenant carries the tag yet.
- **Fix:**
  1. Restore the network, then start the device from the media again before you deploy.
  2. For a tag that no device carries, the administrator gives it to one device in the tenant, or uses the interactive mode.
- **Collect:** the `FoundryDeploy.log` line "Optional Autopilot group tag discovery timed out. Deploy will keep the configured group tags and default." or "Optional Autopilot group tag discovery failed. ..."

An opened wizard does not prove that the upload can reach Microsoft: it is attempted only at the end of the deployment.

## "Hardware hash upload unavailable" <a href="#hardware-hash-upload-unavailable" id="hardware-hash-upload-unavailable"></a>

- **Where:** Foundry Deploy, **Autopilot** step, and **Hardware hash upload status** with **Warning details** on **Summary**.
- **Cause:**
  - "The Autopilot certificate has expired. Deployment can continue without uploading the hardware hash.": the **Certificate expiration** shown under **Configuration details** is in the past for the clock of the device.
  - "Required certificate information is missing. Deployment can continue without uploading the hardware hash.": the media was created from an incomplete configuration.
- **Fix:**
  1. Check the date and time of the device.
  2. Ask the administrator to create a new certificate and to create or update the media.
- **Collect:** the **Certificate expiration** value.

If you deploy anyway, **Register Autopilot device** is skipped with "Autopilot hardware hash upload skipped because the embedded certificate has expired." or "Autopilot hardware hash upload skipped because required media certificate information is incomplete."

## "Autopilot hardware hash capture failed: \<reason>" <a href="#hardware-hash-capture-failed" id="hardware-hash-capture-failed"></a>

- **Where:** Foundry Deploy, step **Register Autopilot device**. The deployment fails for the first three reasons below and continues with the step skipped for the others. Windows is already on the disk in both cases.
- **Cause:**

| Reason | Meaning |
| --- | --- |
| "Required support library was not found at '\<path>\PCPKsp.dll'." | The installed Windows image does not hold this file. The deployment fails. |
| "Required support library could not be copied to '\<path>': ..." | The file could not be copied into Windows PE. The deployment fails. |
| "OA3Tool exited with code \<n>: ..." naming `PCPKsp.dll` or a provider that could not be loaded | The tool could not load the file. The deployment fails. |
| "OA3Tool was not found at '\<path>'." | The media does not carry the tool. |
| "OA3Tool exited with code \<n>: ..." | The tool could not read the hardware. |
| "OA3Tool did not create OA3.xml." or "OA3 report XML is invalid: ..." | The tool produced no usable report. |
| "OA3 report does not contain a serial number." | The firmware reports no serial number, or one of the placeholders "None", "To Be Filled By O.E.M." and "System Serial Number". |
| "OA3 report does not contain a hardware hash." | The device returned no hardware hash. |

- **Fix:**
  1. For `PCPKsp.dll`, check that the Windows image you deploy contains `Windows\System32\PCPKsp.dll`, or deploy another image.
  2. For a missing tool, the administrator creates the media again.
  3. For the other reasons, read `OA3.log`. A device that cannot give its hardware hash in Windows PE may give it in installed Windows, where the interactive mode reads it.
- **Collect:** `autopilot-hash-upload-status.json` (`uploadState` is `CaptureFailed`, and `failureCode` names the case), `OA3.log` and `OA3.xml`.

## "Autopilot hardware hash upload skipped: \<reason>" <a href="#hardware-hash-upload-skipped" id="hardware-hash-upload-skipped"></a>

- **Where:** Foundry Deploy, step **Register Autopilot device**, shown as skipped. Point at the step to read the reason. The deployment completes and the device is not registered.
- **Cause:**

| Reason | Likely cause |
| --- | --- |
| "Microsoft Entra token request failed with status code 401: ..." | Microsoft Entra refused the certificate: it was removed from the app registration, it has expired, or the clock of the device is wrong. |
| "Microsoft Entra token request failed with status code 400: ..." or 403 | Microsoft Entra refused the sign-in of the application. The text that follows, which starts with `AADSTS`, gives the reason, for example a Conditional Access policy. |
| "Microsoft Graph request failed with status code 403: ..." | The permission or the admin consent of `Foundry OSD Autopilot Registration` was removed. |
| "Microsoft Graph request failed with status code 429: ..." or a code from 500 | Microsoft Graph or Intune was busy or unavailable. |
| A network error without a status code | The device cannot reach the hosts of [Network endpoints](../reference/network-endpoints.md): DNS, proxy or firewall. |
| "The embedded PFX certificate could not be loaded.", "... does not match the configured app certificate thumbprint.", "... does not contain private key material." or "Encrypted certificate material is missing from the Autopilot upload configuration." | The media was created from an inconsistent configuration. |
| "Deployment secrets are not unlocked." or "Deploy secret key was not found in the boot media configuration." | Foundry Deploy could not read the protected content of the media. |

Before it gives up on a network error or a busy service, Foundry Deploy tries again 5 times, 10 seconds apart.

- **Fix:**
  1. Check the network, the date and the time of the device, then deploy again.
  2. For a refused certificate, permission or consent, the administrator connects the tenant again on **Windows Autopilot > Zero-Touch**, corrects what the page reports, and creates or updates the media.
  3. For the last two rows, the administrator creates the media again.
  4. To register this device without deploying again, import `AutopilotHWID.csv` in your tenant.
- **Collect:** `AutopilotUploadResult.json` (`failureCode` and `message`) and `FoundryDeploy.log`.

## "Imported Autopilot device did not appear in Windows Autopilot devices before the timeout." <a href="#device-did-not-appear" id="device-did-not-appear"></a>

- **Where:** Foundry Deploy, step **Register Autopilot device**, shown as skipped; or the interactive upload window.
- **Cause:** Microsoft accepted the hardware hash, but the device was not listed in the tenant within 10 minutes in Foundry Deploy, or 15 minutes in the upload window.
- **Fix:**
  1. Look for the device by its serial number in your tenant a little later: the import can finish after Foundry stopped waiting.
  2. In the upload window, select **Upload** again.
  3. If the device never appears, check the import in your tenant. After a zero-touch deployment, `AutopilotHWID.csv` can be imported by hand.
- **Collect:** `AutopilotUploadResult.json` (`failureCode` is `AutopilotDeviceTimedOut`), or `registration-result.json` and `graph.log` for the window.

## "Autopilot hardware hash import failed: \<error>." <a href="#hardware-hash-import-failed" id="hardware-hash-import-failed"></a>

- **Where:** Foundry Deploy, step **Register Autopilot device**, shown as skipped. The interactive upload window reads "Autopilot import failed. \<error>".
- **Cause:** your tenant refused the import. \<error> is the error name returned by Intune.
- **Fix:**
  1. Search the error name in the Microsoft documentation of Windows Autopilot device import.
  2. Correct the cause in your tenant, then deploy again, or select **Upload** again in the window.
- **Collect:** `AutopilotUploadResult.json` (`failureCode` holds the Intune error code), or `registration-result.json` and `graph.log` for the window.

## "The hardware hash is visible in Windows Autopilot, but ..." <a href="#visible-but-not-assigned" id="visible-but-not-assigned"></a>

- **Where:** Foundry Deploy, step **Register Autopilot device**, shown as skipped; or the interactive upload window.
- **Cause:** the device is registered. Only the group tag or the computer name is missing. The message ends with one of these:
  - "... group tag assignment failed: \<reason>" or "... computer name and device property assignment failed: \<reason>": Microsoft Graph refused the change.
  - "... was not confirmed before the timeout.": the change was sent but not seen within 10 minutes in Foundry Deploy, or 15 minutes in the window.
- **Fix:**
  1. Check the device in your tenant: the change may have been applied after Foundry stopped waiting.
  2. Otherwise set the group tag or the device name in your tenant, or select **Upload** again in the window.
- **Collect:** `AutopilotUploadResult.json`, or `registration-result.json` and `graph.log` for the window.

## "Multiple Windows Autopilot devices matched the captured serial number; device property reconciliation was skipped to avoid updating the wrong device." <a href="#multiple-devices-matched" id="multiple-devices-matched"></a>

- **Where:** Foundry Deploy, step **Register Autopilot device**, shown as skipped. The interactive upload window reads "... group tag reconciliation was skipped to avoid updating the wrong device."
- **Cause:** more than one Windows Autopilot device of the tenant has the serial number of this device. Foundry changed none of them.
- **Fix:** delete the outdated record in your tenant, then deploy again or select **Upload** again in the window.
- **Collect:** `AutopilotUploadResult.json` (`failureCode` is `AutopilotDeviceAmbiguous`), or `registration-result.json` for the window.

## The upload window does not open after the restart

- **Where:** Windows setup, after a deployment in interactive mode.
- **Cause:** read the files of `C:\Windows\Temp\Foundry\Logs\AutopilotRegistration`. In Windows setup, Shift+F10 opens a command prompt.
  - `oobe-waiter.log` reads "Autopilot registration is already completed.": the device was registered by an earlier run on this installation. The window opens once.
  - `foreground.log` reads "Foreground wrapper failed. Timed out while waiting for OOBE foreground access.": the window comes to the front through the command prompt of Windows setup, and that prompt did not open within 2 minutes.
  - `oobe-waiter.log` reads "Timed out while waiting for active console session.": Windows setup did not show a screen within 20 minutes.
  - `OOBE.log` does not exist: Windows setup did not run Foundry's command.
  - The media does not use the interactive mode: **Steps** had no **Prepare Autopilot assistant** step.
- **Fix:**
  1. Wait 2 minutes on the first Windows setup screen before you conclude.
  2. If the command prompt of Windows setup is disabled in your image, use another image or the zero-touch mode.
  3. Otherwise register the device another way and send the files below.
- **Collect:** `OOBE.log`, `oobe-launcher.log`, `oobe-waiter.log`, `oobe-sessiondiag.log` and `foreground.log`.

## "Waiting for network connectivity." <a href="#waiting-for-network-connectivity" id="waiting-for-network-connectivity"></a>

- **Where:** the interactive upload window, with "Retrying Microsoft sign-in request in 10 seconds." or "Waiting for network connectivity. Retrying authentication."
- **Cause:** installed Windows cannot reach the Microsoft sign-in host. The network that worked in Windows PE is not necessarily available in Windows.
- **Fix:** connect the device to a network that reaches the hosts of [Network endpoints](../reference/network-endpoints.md). The window retries by itself and then shows a code.
- **Collect:** `registration.log`.

## "Authentication failed. Check logs for details." <a href="#authentication-failed" id="authentication-failed"></a>

- **Where:** the interactive upload window, in place of the code.
- **Cause:** Microsoft refused the sign-in. `registration.log` holds "Device code authentication failed. \<reason>" with Microsoft's own text, which starts with `AADSTS`. For example, the sign-in was declined on the other device, the account belongs to another tenant, the tenant does not allow this application, or a policy of the tenant refuses a sign-in with a code.
- **Fix:**
  1. Read the reason in `registration.log`.
  2. Have the tenant administrator correct what the reason names.
  3. The window offers no new attempt after this message. Deploy the device again, or register it another way.
- **Collect:** `registration.log` and `registration-result.json`. An expired code is not an error: the window replaces it by itself ("Requesting a new Microsoft sign-in code.").

## "Upload failed. Check logs for details." <a href="#upload-failed" id="upload-failed"></a>

- **Where:** the interactive upload window, after **Upload**.
- **Cause:** the upload stopped before the wait began. `registration.log` holds the reason:
  - "The device serial number could not be read." or "The Autopilot hardware hash could not be read.": Windows returned no value for this device.
  - "Microsoft Graph request failed: \<reason>": with status 403, the account you signed in with may not import Windows Autopilot devices.
- **Fix:**
  1. **Upload** is available again: once the cause is corrected, select it again.
  2. For a refused account, the tenant administrator must allow it to import Windows Autopilot devices. The window keeps the account you signed in with: to use another account, deploy the device again.
- **Collect:** `registration.log` and `graph.log`.

## The window lists only None and Custom

- **Where:** the interactive upload window, **Group tag**.
- **Cause:** no Windows Autopilot device of the tenant carries a group tag, or the window could not read the tags (`registration.log`: "Group tag discovery failed. Manual entry remains available. ...").
- **Fix:** choose **Custom** and type the tag in **Custom group tag**. With **Custom** and an empty box, the device is uploaded without a tag.
- **Collect:** `registration.log`.

## The group tag of a registered device was removed

- **Where:** your tenant, after a deployment of a device that was already registered.
- **Cause:** **None** was chosen as the group tag, in Foundry Deploy or in the upload window. For a device that is already registered, Foundry applies the choice, and **None** clears the existing tag.
- **Fix:**
  1. Set the tag again on the device in your tenant.
  2. For the next deployments, the administrator sets **Default group tag** on **Windows Autopilot > Zero-Touch**, or the technician chooses the tag at each deployment.
- **Collect:** `autopilot-hash-upload-status.json` or `registration-result.json`, which record the group tag that was sent.

## The computer name is not assigned

- **Where:** your tenant, after a deployment with one of the two upload modes.
- **Cause:**
  - **Upload computer name to Autopilot** is off on **Machine naming**, or the media was not updated after it was turned on.
  - A custom answer file was selected in Foundry Deploy. Foundry then skips the name and still uploads the hash; `FoundryDeploy.log` reads "Autopilot computer name assignment skipped because no final Foundry Deploy computer name is available. Hardware hash upload continues."
  - The assignment failed after the registration: see ["The hardware hash is visible in Windows Autopilot, but ..."](#visible-but-not-assigned).
- **Fix:** check the three points above. [Machine naming](../foundry-osd/customization/machine-naming.md) lists the conditions of this option.
- **Collect:** `FoundryDeploy.log` and the files of the mode in [Evidence files](#evidence-files).

## The device is registered but no Autopilot profile applies

- **Where:** Windows setup, after a registration that succeeded.
- **Cause:**
  - Foundry waits for the device to be listed in the tenant, not for a profile to be assigned to it. Your tenant can need more time.
  - In interactive mode the device restarts 10 seconds after the upload, so Windows setup can start again before the profile is assigned.
  - The group tag is missing, wrong or was removed, and your tenant uses it to assign the profile.
  - A custom answer file was used with the zero-touch mode. Foundry Deploy then warns "Hash upload does not guarantee Autopilot enrollment."
- **Fix:**
  1. In your tenant, check the device, its group tag and whether a profile is assigned.
  2. Let nobody go through Windows setup until the profile is assigned.
  3. With the zero-touch mode, the administrator can turn automatic restart off on [General](../foundry-osd/general.md), so that you check the tenant before the device restarts.
- **Collect:** the files of the mode in [Evidence files](#evidence-files).

## The motherboard was replaced

- **Where:** your tenant, for a device registered before the repair.
- **Cause:** the repaired device has a new hardware hash. For a serial number that is already registered, Foundry updates the group tag and the name but does not replace the stored hash, so the old registration stays.
- **Fix:**
  1. Delete the old Windows Autopilot registration of the device. See [Windows Autopilot motherboard replacement](https://learn.microsoft.com/en-us/autopilot/autopilot-motherboard-replacement).
  2. Deploy the device again and check its new registration.
- **Collect:** nothing.

## Evidence files

Never publish a tenant identifier, a sign-in code, a certificate or a hardware hash in a support request. `AutopilotHWID.csv` and `OA3.xml` contain the hardware hash of the device.

<details>

<summary>Files written on the deployed device</summary>

Zero-touch mode, in `C:\Windows\Temp\Foundry\Logs\AutopilotHash`:

| File | Content |
| --- | --- |
| `autopilot-hash-upload-status.json` | Result of the step: `uploadState`, `message`, `failureCode`, serial number and group tag |
| `AutopilotUploadResult.json` | Result of the upload: `message` and `failureCode` |
| `AutopilotHWID.csv` | Serial number and hardware hash, in the format of a Windows Autopilot import |
| `OA3.xml`, `OA3.log` | Report and trace of the tool that reads the hardware hash |
| `OA3.cfg`, `input.xml` | Input of that tool, kept when the capture failed |

Interactive mode, in `C:\Windows\Temp\Foundry\Logs\AutopilotRegistration`:

| File | Content |
| --- | --- |
| `registration.log` | What the upload window did, with the reason of a failure |
| `graph.log` | Microsoft Graph requests and their errors |
| `OOBE.log`, `oobe-launcher.log`, `oobe-waiter.log`, `oobe-sessiondiag.log`, `foreground.log`, `launcher.log` | How the window was started from Windows setup |

Interactive mode, in `C:\Windows\Temp\Foundry\State\AutopilotRegistration`:

| File | Content |
| --- | --- |
| `registration-result.json` | `status` (`completed` or `failed`), `message` and, on success, the serial number and the group tag |
| `registration-state.json` | The stage reached: `authentication`, `hardware`, `import` or `deviceProperties` |

Before the restart, in Windows PE, the folders are on the Windows volume of the device, which may have another letter than `C:`.

</details>
