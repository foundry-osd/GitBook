# Windows Autopilot troubleshooting after the restart

Use this page for the upload window of the interactive mode, which opens in Windows setup (OOBE) after the restart, and for what you find in your tenant once a device is registered. For Foundry OSD, see [Windows Autopilot troubleshooting](../autopilot.md); for Foundry Deploy, see [During deployment](during-deployment.md).

| What you see | Go to |
| --- | --- |
| The upload window does not open | [No window](#the-upload-window-does-not-open-after-the-restart) |
| "Waiting for network connectivity." | [No network](#waiting-for-network-connectivity) |
| "Authentication failed. Check logs for details." | [Sign-in refused](#authentication-failed) |
| **Group tag** offers only **None** and **Custom** | [A group tag you expect is not offered](../autopilot.md#a-group-tag-you-expect-is-not-offered) |
| "Upload failed. Check logs for details.", or another message after **Upload** | [Upload failed](#upload-failed) |
| The computer name is not assigned in your tenant | [Name](#the-computer-name-is-not-assigned) |
| The device is registered but Windows setup applies no Autopilot profile, or its group tag has disappeared | [No profile applied](#the-device-is-registered-but-no-autopilot-profile-applies) |
| A device whose motherboard was replaced keeps its old registration | [Limits of the zero-touch mode](../../foundry-osd/autopilot/zero-touch-hardware-hash.md) |

The files named under **Collect** are described in [Evidence files](#evidence-files). In Windows setup, Shift+F10 opens a command prompt to read them.

## The upload window does not open after the restart

- **Where:** Windows setup, after a deployment in interactive mode.
- **Cause:** the files of `C:\Windows\Temp\Foundry\Logs\AutopilotRegistration` tell which case applies.
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

- **Where:** the upload window, with "Retrying Microsoft sign-in request in 10 seconds." or "Waiting for network connectivity. Retrying authentication."
- **Cause:** installed Windows cannot reach the Microsoft sign-in host. The network that worked in Windows PE is not necessarily available in Windows.
- **Fix:** connect the device to a network that reaches the hosts of [Network endpoints](../../reference/network-endpoints.md). The window retries by itself and then shows a code.
- **Collect:** `registration.log`.

## "Authentication failed. Check logs for details." <a href="#authentication-failed" id="authentication-failed"></a>

- **Where:** the upload window, in place of the code.
- **Cause:** Microsoft refused the sign-in. `registration.log` holds "Device code authentication failed. \<reason>", where Microsoft's own text, which starts with `AADSTS`, names the reason.
- **Fix:**
  1. Read the reason in `registration.log` and give it to your tenant administrator.
  2. The window offers no new attempt after this message. Once the cause is corrected, deploy the device again, or register it another way.
- **Collect:** `registration.log` and `registration-result.json`.

An expired code is not an error: the window replaces it by itself and reads "Requesting a new Microsoft sign-in code." meanwhile.

## "Upload failed. Check logs for details." <a href="#upload-failed" id="upload-failed"></a>

- **Where:** the upload window, after **Upload**.
- **Cause:** the upload stopped before the wait began. `registration.log` holds the reason:
  - "The device serial number could not be read." or "The Autopilot hardware hash could not be read.": Windows returned no value for this device.
  - "Microsoft Graph request failed: \<reason>": Microsoft Graph refused the request or could not be reached.
- **Fix:**
  1. **Upload** is available again: once the cause is corrected, select it again.
  2. If Microsoft Graph refused the request with status 403, ask your tenant administrator whether this account may register Windows Autopilot devices. The window keeps the account you signed in with: to use another account, deploy the device again.
- **Collect:** `registration.log` and `graph.log`.

After the wait began, the window shows the reason itself and **Upload** becomes available again. These messages ("Imported Autopilot device did not appear ...", "Autopilot import failed. ...", "The hardware hash is visible in Windows Autopilot, but ...", "Multiple Windows Autopilot devices matched ...") are explained under [Other results of the upload](during-deployment.md#hardware-hash-upload-skipped). For them, collect `registration-result.json` and `graph.log`.

## The computer name is not assigned

- **Where:** your tenant, after a deployment with one of the two upload modes.
- **Cause:**
  - **Upload computer name to Autopilot** is off on **Machine naming**, or the media was not updated after it was turned on.
  - A custom answer file was selected in Foundry Deploy. Foundry then skips the name and still uploads the hash; `FoundryDeploy.log` reads "Autopilot computer name assignment skipped because no final Foundry Deploy computer name is available. Hardware hash upload continues."
  - The assignment failed after the registration: the message starts with "The hardware hash is visible in Windows Autopilot, but".
- **Fix:** check the three points above. [Machine naming](../../foundry-osd/customization/machine-naming.md) lists the conditions of this option.
- **Collect:** `FoundryDeploy.log`, and `AutopilotUploadResult.json` or `registration-result.json`.

## The device is registered but no Autopilot profile applies

- **Where:** Windows setup, after a registration that succeeded.
- **Cause:**
  - Foundry waits for the device to be listed in the tenant, not for a profile to be assigned to it. Your tenant can need more time.
  - In interactive mode the device restarts 10 seconds after the upload, so Windows setup can start again before the profile is assigned.
  - The group tag is missing or wrong, and your tenant uses it to assign the profile. A device that was already registered loses its tag when **None** is chosen in Foundry Deploy or in the upload window.
  - A custom answer file was used with the zero-touch mode. Foundry Deploy then warns "Hash upload does not guarantee Autopilot enrollment."
- **Fix:**
  1. In your tenant, check the device, its group tag and whether a profile is assigned. Set the group tag again if it was removed.
  2. Let nobody go through Windows setup until the profile is assigned.
  3. With the zero-touch mode, the administrator can turn automatic restart off on [General](../../foundry-osd/general.md), so that you check the tenant before the device restarts.
- **Collect:** `autopilot-hash-upload-status.json` or `registration-result.json`, which record the group tag that was sent.

## Evidence files

Never publish a tenant identifier, a sign-in code or a hardware hash in a support request.

<details>

<summary>Files of the interactive mode on the deployed device</summary>

In `C:\Windows\Temp\Foundry\Logs\AutopilotRegistration`:

| File | Content |
| --- | --- |
| `registration.log` | What the upload window did, with the reason of a failure |
| `graph.log` | Microsoft Graph requests and their errors |
| `OOBE.log`, `oobe-launcher.log`, `oobe-waiter.log`, `oobe-sessiondiag.log`, `foreground.log`, `launcher.log` | How the window was started from Windows setup |

In `C:\Windows\Temp\Foundry\State\AutopilotRegistration`:

| File | Content |
| --- | --- |
| `registration-result.json` | `status` (`completed` or `failed`), `message` and, on success, the serial number and the group tag |
| `registration-state.json` | The stage reached: `authentication`, `hardware`, `import` or `deviceProperties` |

The files of the zero-touch mode are listed in [During deployment](during-deployment.md#evidence-files).

</details>
