# Windows Autopilot troubleshooting after the restart

Use this page for the **Foundry Autopilot Interactive** window, which opens full screen during Windows setup after the restart, in front of the **Foundry Post-installation** console, and for what you find in your tenant once a device is registered. For Foundry OSD, see [Windows Autopilot troubleshooting](../autopilot.md); for Foundry Deploy, see [During deployment](during-deployment.md).

| What you see | Go to |
| --- | --- |
| The registration window does not open | [No window](#no-upload-window) |
| "The network is not available. Trying again every 10 seconds..." | [No network](#waiting-for-network-connectivity) |
| A sign-in error with **Retry**: "The sign-in was declined.", "... Your organization may block device code sign-in ...", "A secure connection to Microsoft could not be established. ..." or "The sign-in failed because Microsoft returned an unexpected response. ..." | [Sign-in refused](#authentication-failed) |
| "Your session expired. Sign in again with the new code." | [Sign-in refused](#authentication-failed) |
| **Group tag** offers only **None** and **Custom**, with "Existing group tags could not be loaded." | [A group tag you expect is not offered](../autopilot.md#group-tag-not-offered) |
| An error message after **Register**, or "Several Windows Autopilot devices have this serial number. ..." | [Registration failed](#upload-failed) |
| "The time allowed for this step is over.", or Windows setup continued after **Skip registration** | [Windows setup continued without registering](#skipped-or-timed-out) |
| The computer name, group tag or user is not assigned in your tenant | [Name](#name-not-assigned) |
| The device is registered but Windows setup applies no Autopilot profile, its group tag has disappeared, or your tenant reports "Self-Deploying mode requires TPM 2.0 hardware" | [No profile applied](#no-profile-applied) |
| A device whose motherboard was replaced keeps its old registration | [Limits of the zero-touch mode](../../foundry-osd/autopilot/zero-touch-hardware-hash.md#limits) |

The files named under **Collect** are described in [Evidence files](#evidence-files). In Windows setup, Shift+F10 opens a command prompt to read them.

## The registration window does not open after the restart <a href="#no-upload-window" id="no-upload-window"></a>

- **Where:** Windows setup, after a deployment in interactive mode.
- **Cause:** the line `Upload hardware hash to Windows Autopilot` of the **Foundry Post-installation** console tells which case applies.
  - The line shows `[Succeeded]` at once: the device was registered by an earlier run on this installation. The window does not ask again.
  - The line shows `[Failed]` and `failureCode` in `C:\Windows\Temp\Foundry\State\PreOobe\execution-result.json` is `autopilot_assistant_missing`: Foundry Autopilot Interactive was not copied to the device.
  - A message box titled **Foundry Autopilot Interactive** reads "The Autopilot configuration is missing or invalid, so this device cannot be registered.", with a **Close** button, instead of the sign-in page. When you select **Close**, or after a while if Windows setup is close to its time limit, the window ends and Windows setup continues without registering the device.
  - The console has no such line: the media does not use the interactive mode, and **Steps** had no **Prepare Autopilot assistant** step.
  - No console opens at all: see [No Foundry Post-installation console appears](../after-the-restart.md#no-console).
- **Fix:**
  1. For `autopilot_assistant_missing` or an invalid configuration, deploy the device again.
  2. Otherwise register the device another way and send the files below.
- **Collect:** `Foundry.AutopilotInteractive.log`, `C:\Windows\Temp\Foundry\Logs\PreOobe\autopilot-registration\Process\output.log` and `execution-result.json`.

## "The network is not available. Trying again every 10 seconds..." <a href="#waiting-for-network-connectivity" id="waiting-for-network-connectivity"></a>

- **Where:** the sign-in page, in place of the code.
- **Cause:** installed Windows cannot reach the Microsoft sign-in host. The network that worked in Windows PE is not necessarily available in Windows.
- **Fix:** connect the device to a network that reaches the hosts of [Network endpoints](../../reference/network-endpoints.md). The window tries again by itself and then shows a code.
- **Collect:** `Foundry.AutopilotInteractive.log`.

## A sign-in error with Retry <a href="#authentication-failed" id="authentication-failed"></a>

- **Where:** the sign-in page, in place of the code, with a **Retry** button.
- **Cause:**
  - "The sign-in was declined.": the sign-in on the other device was refused or canceled.
  - "The sign-in failed. Your organization may block device code sign-in with a Conditional Access policy.": your tenant does not allow this kind of sign-in. A new code does not help.
  - "A secure connection to Microsoft could not be established. A proxy that inspects HTTPS traffic or a wrong system clock can cause this.": the network inspects secure traffic, or the date and time of the device are wrong.
  - "The sign-in failed because Microsoft returned an unexpected response. A proxy or a captive portal on this network can cause this.": a sign-in page or a proxy answers in place of Microsoft.
- **Fix:**
  1. Select **Retry**: the window asks for a new code.
  2. For a block, give the message to your tenant administrator, who allows the sign-in with a code for this account, or you register the device another way.
  3. For a secure connection error, check the date and time of the device, then use a network that does not inspect the traffic to Microsoft.
- **Collect:** `Foundry.AutopilotInteractive.log`.

An expired code is not an error: the window replaces the code by itself; the QR code stays the same. "Your session expired. Sign in again with the new code." means that Microsoft rejected the sign-in while the device was being registered: sign in again.

## An error message after Register <a href="#upload-failed" id="upload-failed"></a>

- **Where:** the registration page, after **Register**. The message stays in the window and the controls become available again.
- **Cause and fix:**

| Message | Cause | Fix |
| --- | --- | --- |
| "This device is registered in another tenant. The other tenant or the reseller must deregister it before it can be registered here." | The serial number is registered in the Windows Autopilot of another organization. | The other tenant or the reseller deregisters the device, then select **Register** again. |
| "This device is already registered in Windows Autopilot. Check the device in Intune, then try again." | Your tenant already holds this device, or a duplicate of it. | Check the device in your tenant, remove the duplicate, then select **Register** again. |
| "Several Windows Autopilot devices have this serial number. Remove the duplicates in Intune, then try again." | Several records of your tenant share the serial number. Foundry changes none of them. | Delete the outdated records in your tenant, then select **Register** again. |
| "The signed-in account cannot import Windows Autopilot devices. Sign in with an account that has an Intune role allowed to import Autopilot devices." | The account lacks the Intune role. | Select **Use another account**, or ask your tenant administrator for the role. |
| "Microsoft Graph rejected the sign-in. Sign in again, then try again." | The sign-in expired. | Sign in again with the new code. |
| "Microsoft Graph could not be reached. Check the network connection, then try again." | The device lost the network, or a proxy or firewall blocks the hosts of [Network endpoints](../../reference/network-endpoints.md). | Check the network, then select **Register** again. |
| "The registration failed. A proxy or a captive portal on this network can cause this. Details are in the log; try again." | Microsoft answered unexpectedly. | Check the network, then select **Register** again. |
| "The serial number or the hardware hash of this device could not be read. Details are in the log." | Windows returned no value for this device. | Register the device another way, and send the log. |
| "The device did not appear in Windows Autopilot within 15 minutes. Check Intune, then try again." | Your tenant did not list the device in time. The import can finish later. | Look for the serial number in your tenant a little later, before you select **Register** again. |
| "This device is registered in Windows Autopilot, but its group tag, computer name or assigned user could not be applied. Check the user name and Intune, then try again." | The device is registered. Only its group tag, name or user is missing. | Check the user name, then select **Register** again, or set the missing value in your tenant. |
| "Enter a user principal name such as user@contoso.com, or leave the field empty." | The **Assigned user (UPN)** is not valid. | Correct it, or empty the box. |

- **Collect:** `Foundry.AutopilotInteractive.log`. Do not publish the serial number or the account.

## Windows setup continued without registering the device <a href="#skipped-or-timed-out" id="skipped-or-timed-out"></a>

- **Where:** the registration window closed. After **Skip registration**, the console line `Upload hardware hash to Windows Autopilot` shows `[Succeeded]` and the console ends with warnings, because skipping is a choice, not an error. When the time allowed is over, the line shows `[Failed]`.
- **Cause:**
  - **Skip registration** was confirmed, or the window was closed with Alt+F4 and confirmed. If this happened while the registration was in progress, the device may still appear in Windows Autopilot.
  - The window reads "The time allowed for this step is over. Windows setup continues without registering this device.": the step lasts 120 minutes at most.
- **Fix:** check your tenant for the serial number. If the device is not registered, register it another way, or deploy it again.
- **Collect:** `Foundry.AutopilotInteractive.log`, `output.log` and `execution-result.json` of the Post-installation console.

## The computer name is not assigned <a href="#name-not-assigned" id="name-not-assigned"></a>

- **Where:** your tenant, after a deployment with one of the two upload modes.
- **Cause:**
  - **Upload computer name to Autopilot** is off on **Machine naming**, or the media was not updated after it was turned on.
  - A custom answer file was selected in Foundry Deploy. Foundry then skips the name and still uploads the hash; `FoundryDeploy.log` reads "Autopilot computer name assignment skipped because no final Foundry Deploy computer name is available. Hardware hash upload continues."
  - The assignment failed after the registration: the message starts with "The hardware hash is visible in Windows Autopilot, but" (zero-touch mode) or reads "This device is registered in Windows Autopilot, but its group tag, computer name or assigned user could not be applied." (interactive mode).
- **Fix:** check the three points above. [Machine naming](../../foundry-osd/customization/machine-naming.md) lists the conditions of this option.
- **Collect:** `FoundryDeploy.log`, and `AutopilotUploadResult.json` or `Foundry.AutopilotInteractive.log`.

## The device is registered but no Autopilot profile applies <a href="#no-profile-applied" id="no-profile-applied"></a>

- **Where:** Windows setup, after a registration that succeeded.
- **Cause:**
  - In zero-touch mode, Foundry waits for the device to be listed in the tenant, not for a profile to be assigned to it. Your tenant can need more time.
  - In interactive mode the window waits for the profile for up to 20 minutes, then lets Windows setup continue: "The profile is not assigned yet; Windows setup continues." It also continues when **Continue without waiting** is selected, or when it reads "The profile assignment status could not be read." or "Intune reported that the Autopilot profile assignment failed." OOBE can then start before the profile is assigned.
  - The group tag is missing or wrong, and your tenant uses it to assign the profile. A device that was already registered loses its tag when **None** is chosen in Foundry Deploy or in the registration window.
  - A custom answer file was used with the zero-touch mode. Foundry Deploy then warns "Hash upload does not guarantee Autopilot enrollment."
  - The profile assignment fails with "Self-Deploying mode requires TPM 2.0 hardware" although the device has a TPM 2.0: its registered hardware hash does not carry the TPM version. Your tenant keeps that hash until the device is registered again.
- **Fix:**
  1. In your tenant, check the device, its group tag and whether a profile is assigned. Set the group tag again if it was removed.
  2. Let nobody go through Windows setup until the profile is assigned.
  3. With the zero-touch mode, the administrator can turn automatic restart off on [General](../../foundry-osd/general.md), so that you check the tenant before the device restarts.
  4. For "Self-Deploying mode requires TPM 2.0 hardware", delete the device from the Windows Autopilot devices of your tenant, then deploy it again with media created by the current Foundry OSD so that the hash is uploaded again.
- **Collect:** `autopilot-hash-upload-status.json`, or `Foundry.AutopilotInteractive.log` in interactive mode.

## Evidence files <a href="#evidence-files" id="evidence-files"></a>

Never publish a tenant identifier, a sign-in code or a hardware hash in a support request.

<details>

<summary>Files of the interactive mode on the deployed device</summary>

| Location | Content |
| --- | --- |
| `C:\Windows\Temp\Foundry\Logs\AutopilotInteractive\Foundry.AutopilotInteractive.log` | What the window did, with the reason of a failure. A few older logs are kept next to it. |
| `C:\Windows\Temp\Foundry\Logs\PreOobe\autopilot-registration\Process\output.log` | What the window wrote while it ran, written when it closes |
| `C:\Windows\Temp\Foundry\State\AutopilotInteractive\result.json` | Whether the registration completed, failed or was skipped. Once it reads completed, the window does not ask again. |

The log never contains the sign-in code, a token or the hardware hash. The files of the zero-touch mode are listed in [During deployment](during-deployment.md#evidence-files).

</details>
