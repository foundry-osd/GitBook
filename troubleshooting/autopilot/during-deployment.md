# Windows Autopilot troubleshooting during deployment

Use this page for what Foundry Deploy shows at the **Autopilot** step of the wizard and at the Autopilot step of the deployment. For Foundry OSD, see [Windows Autopilot troubleshooting](../autopilot.md); for the upload window and the tenant, see [After the restart](after-the-restart.md).

In **Steps**, the Autopilot step is named **Copy Autopilot profile**, **Register Autopilot device** or **Prepare Autopilot assistant**. The **Deployment failed** screen names it "Provision Autopilot". A skipped step has a skipped icon next to its name: point at it to read the reason in a tooltip.

| What you see | Go to |
| --- | --- |
| "This answer file conflicts with the configured Autopilot enrollment mode. Choose another file or change the media configuration." | [A message under Answer file](../deployment/before-deployment-starts.md#answer-file-rejected), in Windows deployment troubleshooting |
| "No embedded Autopilot profiles were found on this media." | [No profile on the media](#no-embedded-profiles) |
| A group tag is missing from the **Group tag** list | [A group tag you expect is not offered](../autopilot.md#group-tag-not-offered) |
| "Hardware hash upload unavailable", or "Autopilot hardware hash upload skipped because ..." | [Upload unavailable](#hardware-hash-upload-unavailable) |
| "Failed step: Provision Autopilot" and, under **View error details**, "Autopilot hardware hash capture failed: ..." | [Capture failed](#hardware-hash-capture-failed) |
| "Failed step: Provision Autopilot" with another reason under **View error details** | [Another reason](#step-fails-other-reason) |
| **Register Autopilot device** is skipped with "Autopilot hardware hash capture failed: ..." | [Capture failed](#hardware-hash-capture-failed) |
| "Autopilot hardware hash upload skipped: ..." | [Upload skipped](#hardware-hash-upload-skipped) |
| "Imported Autopilot device did not appear ...", "Autopilot hardware hash import failed: ...", "The hardware hash is visible in Windows Autopilot, but ..." or "Multiple Windows Autopilot devices matched ..." | [Other results of the upload](#hardware-hash-upload-skipped) |

The files named under **Collect** are described in [Evidence files](#evidence-files); `FoundryDeploy.log` is in [Log locations](../logs-and-support.md#log-locations).

## "No embedded Autopilot profiles were found on this media." <a href="#no-embedded-profiles" id="no-embedded-profiles"></a>

- **Where:** **Autopilot** step of the wizard, JSON profile mode. **Next** stays unavailable.
- **Cause:** Foundry Deploy found no profile on the media.
- **Fix:** the administrator checks **Imported profiles** and **Default profile** on **Windows Autopilot > JSON profile**, then creates the media again.
- **Collect:** `FoundryDeploy.log`.

## "Hardware hash upload unavailable" <a href="#hardware-hash-upload-unavailable" id="hardware-hash-upload-unavailable"></a>

- **Where:** **Autopilot** step of the wizard, and **Hardware hash upload status** with **Warning details** on **Summary**. Zero-touch mode.
- **Cause:**
  - "The Autopilot certificate has expired. Deployment can continue without uploading the hardware hash.": the **Certificate expiration** shown under **Configuration details** is in the past for the clock of the device.
  - "Required certificate information is missing. Deployment can continue without uploading the hardware hash.": the media was created from an incomplete configuration.
- **Fix:**
  1. Check the date and time of the device.
  2. Ask the administrator to create a new certificate and to create or update the media.
- **Collect:** the **Certificate expiration** value.

If you deploy anyway, **Register Autopilot device** is skipped with the reason "Autopilot hardware hash upload skipped because the embedded certificate has expired." or "Autopilot hardware hash upload skipped because required media certificate information is incomplete."

## "Autopilot hardware hash capture failed: \<reason>" <a href="#hardware-hash-capture-failed" id="hardware-hash-capture-failed"></a>

- **Where:** step **Register Autopilot device**. For the first three reasons the deployment stops on **Deployment failed**, with "Failed step: Provision Autopilot". For the others the step is skipped and the deployment completes. Windows is already on the disk in both cases.
- **Cause:**

| Reason | Meaning |
| --- | --- |
| "Required support library was not found at '\<path>\PCPKsp.dll'." | The installed Windows image does not hold this file. The deployment stops. |
| "Required support library could not be copied to '\<path>': ..." | The file could not be copied into Windows PE. The deployment stops. |
| "OA3Tool exited with code \<n>: ..." naming `PCPKsp.dll` or a provider that could not be loaded | The tool could not load the file. The deployment stops. |
| "OA3Tool was not found at '\<path>'." | The media does not carry the tool. |
| "OA3Tool exited with code \<n>: ..." | The tool could not read the hardware. |
| "OA3Tool did not create OA3.xml." or "OA3 report XML is invalid: ..." | The tool produced no usable report. |
| "OA3 report does not contain a serial number." | The firmware reports no serial number, or one of the placeholders "None", "To Be Filled By O.E.M." and "System Serial Number". |
| "OA3 report does not contain a hardware hash." | The device returned no hardware hash. |

- **Fix:**
  1. For `PCPKsp.dll`, check that the Windows image you deploy contains `Windows\System32\PCPKsp.dll`, or deploy another image.
  2. For a missing tool, the administrator creates the media again.
  3. For the other reasons, read `OA3.log`. The interactive mode reads the hardware hash in installed Windows instead of Windows PE.
- **Collect:** `autopilot-hash-upload-status.json` (`uploadState` is `CaptureFailed`, and `failureCode` names the case), `OA3.log` and `OA3.xml`.

## The Autopilot step fails for another reason <a href="#step-fails-other-reason" id="step-fails-other-reason"></a>

- **Where:** **Deployment failed**, "Failed step: Provision Autopilot", in any of the three modes. Select **View error details** to read the reason. Windows is already on the disk.
- **Cause:**
  - "Selected Autopilot profile file was not found: '\<path>'." (JSON profile mode): the profile chosen in the wizard is no longer on the media.
  - "Autopilot is enabled but no profile was selected." (JSON profile mode).
  - "Failed to resolve the target Autopilot directory." (JSON profile mode).
  - "Target Windows partition is unavailable for Autopilot staging.", "... for Autopilot hardware hash upload." or "... for interactive Autopilot registration assistant staging.": Foundry Deploy has no Windows partition to write to. The message says no more; the likely cause is that the target disk changed or was disconnected after Windows was applied.
- **Fix:**
  1. Check that the deployment media is still connected, then deploy again.
  2. For a profile message, the administrator checks **Imported profiles** and **Default profile** on **Windows Autopilot > JSON profile**, then creates the media again.
  3. For a partition message, see [Deploy again after a failure](../deployment.md#deploy-again).
- **Collect:** the text of **View error details** and `FoundryDeploy.log`.

## "Autopilot hardware hash upload skipped: \<reason>" <a href="#hardware-hash-upload-skipped" id="hardware-hash-upload-skipped"></a>

- **Where:** step **Register Autopilot device**, skipped. The deployment completes and the device is not registered.
- **Cause:**

| Reason | Likely cause |
| --- | --- |
| "Microsoft Entra token request failed with status code 401: ..." | Microsoft Entra refused the certificate: it was removed from the app registration, it has expired, or the clock of the device is wrong. |
| "Microsoft Entra token request failed with status code 400: ..." or 403 | Microsoft Entra refused the sign-in of the application. The text that follows, which starts with `AADSTS`, names the reason. |
| "Microsoft Graph request failed with status code 403: ..." | The permission or the admin consent of `Foundry OSD Autopilot Registration` was removed. |
| "Microsoft Graph request failed with status code 429: ..." or a code from 500 | Microsoft Graph or Intune was busy or unavailable. |
| A network error without a status code | The device cannot reach the hosts of [Network endpoints](../../reference/network-endpoints.md): DNS, proxy or firewall. |
| "The embedded PFX certificate could not be loaded.", "... does not match the configured app certificate thumbprint.", "... does not contain private key material." or "Encrypted certificate material is missing from the Autopilot upload configuration." | The media was created from an inconsistent configuration. |
| "Deployment secrets are not unlocked." or "Deploy secret key was not found in the boot media configuration." | Foundry Deploy could not read the protected content of the media. |

- **Fix:**
  1. Check the network, the date and the time of the device, then deploy again. Before it gives up on a network error or a busy service, Foundry Deploy already tried again 5 times, 10 seconds apart.
  2. For a refused certificate, permission or consent, the administrator reconnects the tenant on **Windows Autopilot > Zero-Touch**, corrects what the page reports, and creates or updates the media.
  3. For the last two rows, the administrator creates the media again.
  4. To register this device without deploying again, import `AutopilotHWID.csv` in your tenant.
- **Collect:** `AutopilotUploadResult.json` (`failureCode` and `message`) and `FoundryDeploy.log`.

**Other results of the upload.** The same step is also skipped with one of these reasons after the import request was sent. The upload window of the interactive mode shows the same messages, with a wait of 15 minutes instead of 10.

| Reason | Meaning | Fix |
| --- | --- | --- |
| "Imported Autopilot device did not appear in Windows Autopilot devices before the timeout." | The device was not listed in the tenant within 10 minutes. | Look for its serial number in your tenant a little later: the import can finish after Foundry stopped waiting. |
| "Autopilot hardware hash import failed: \<error>." In the window: "Autopilot import failed. \<error>" | Your tenant refused the import. \<error> is the error name returned by Intune. | Search the error name in the Microsoft documentation of Windows Autopilot device import, correct the cause, then deploy again. |
| "The hardware hash is visible in Windows Autopilot, but group tag assignment failed: \<reason>", "... but computer name and device property assignment failed: \<reason>" or "... was not confirmed before the timeout." | The device is registered. Only its group tag or its name is missing. | Check the device in your tenant, then set the group tag or the name there. |
| "Multiple Windows Autopilot devices matched the captured serial number; device property reconciliation was skipped to avoid updating the wrong device." In the window: "... group tag reconciliation was skipped ..." | Several Windows Autopilot devices of the tenant have this serial number. Foundry changed none of them. | Delete the outdated record in your tenant, then deploy again. |

For these four, collect `AutopilotUploadResult.json`: its `failureCode` names the case, for example `AutopilotDeviceTimedOut` or `AutopilotDeviceAmbiguous`.

## Evidence files <a href="#evidence-files" id="evidence-files"></a>

Never publish a tenant identifier, a certificate or a hardware hash in a support request. `AutopilotHWID.csv` and `OA3.xml` contain the hardware hash of the device.

<details>

<summary>Files of the zero-touch mode on the deployed device</summary>

After the restart, they are in `C:\Windows\Temp\Foundry\Logs\AutopilotHash`. Before the restart, in Windows PE, the folder is on the Windows volume of the device, which may have another letter than `C:`. Nothing removes this folder.

| File | Content |
| --- | --- |
| `autopilot-hash-upload-status.json` | Result of the step: `uploadState`, `message`, `failureCode`, serial number and group tag |
| `AutopilotUploadResult.json` | Result of the upload: `message` and `failureCode` |
| `AutopilotHWID.csv` | Serial number and hardware hash, in the format of a Windows Autopilot import |
| `OA3.xml`, `OA3.log` | Report and trace of the tool that reads the hardware hash |
| `OA3.cfg`, `input.xml` | Input of that tool, kept when the capture failed |

</details>
