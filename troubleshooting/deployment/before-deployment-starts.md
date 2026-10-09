# Before the deployment starts

Problems met when Foundry Deploy opens and in its wizard, up to the **Deploy** button. Nothing on this page erases the disk: the erase starts only after you accept **Confirm disk erase**.

**Collect** refers to the standard set of [What to collect](../deployment.md#what-to-collect).

| What you see | Go to |
| --- | --- |
| "The password is incorrect. Try again." | [Password refused](#password-incorrect) |
| Foundry Deploy closes as soon as it opens | [Closes at start](#closes-at-start) |
| The **Target disk** list is empty | [No disk](#no-disk) |
| A disk line ending in "Blocked: ..." | [Disk blocked](#disk-blocked) |
| "The disk identity is missing, ambiguous or has changed. ..." | [Disk identity](#disk-identity) |
| **Device inventory** shows **Unavailable** everywhere | [Hardware not detected](#hardware-not-detected) |
| A message under **Computer name** | [Computer name](#computer-name) |
| A message under **Answer file** | [Answer file rejected](#answer-file-rejected) |
| **Next** stays unavailable on **Target device** | [Next unavailable](#next-unavailable) |
| A message under the custom image selectors | [Custom image cannot be selected](#custom-image-selection) |
| "\<manufacturer>: no matching model or version" | [No matching driver pack](#no-matching-driver-pack) |
| **Deploy** stays unavailable, or returns to the summary | [Deploy does not start](#deploy-does-not-start) |

## "The password is incorrect. Try again." <a href="#password-incorrect" id="password-incorrect"></a>

**Where:** the **Protected deployment** window, when Foundry Deploy opens.

**Cause:** the Deployment password typed is not the one set when the media was created. Each failed attempt adds one second of waiting, up to five seconds.

**Fix:**

1. Show the password with the show-or-hide button and check each character: the keyboard layout of Windows PE can differ from the one printed on your keyboard.
2. Ask the administrator for the password of this media. A password cannot be recovered from the media.
3. If the password is lost, the administrator recreates the media. See [Password protection](../../foundry-osd/general.md#password-protection).

**Collect:** nothing. Never write the password in a support request.

## Foundry Deploy closes as soon as it opens <a href="#closes-at-start" id="closes-at-start"></a>

**Where:** right after Foundry Connect, with or without a password prompt. There is no message.

**Cause:** **Cancel** was selected in the **Protected deployment** window, or the deployment configuration on the media cannot be read, or the media carries protected content without the information needed to unlock it.

**Fix:**

1. Restart the device from the deployment media and enter the password.
2. If Foundry Deploy closes without asking for anything, the media is damaged or incomplete: the administrator recreates it.

**Collect:** `X:\Foundry\Logs\FoundryDeploy.log` if you can still reach it, or the `Logs` folder of the USB drive's **Foundry Cache** volume. See [Log locations](../logs-and-support.md#log-locations).

## The Target disk list is empty <a href="#no-disk" id="no-disk"></a>

**Where:** **Target device**, **Target disk**. No message is shown.

**Cause:**

- Windows PE has no driver for the storage controller (Intel VMD or RST, RAID controllers).
- The only disk is connected over USB. Foundry never offers a USB disk as a target.
- Foundry could not read the disk list. The log then contains "Failed to inspect target disks."

**Fix:**

1. Check in the firmware setup that the internal disk is detected.
2. Ask the administrator to add the storage driver to the media, in the driver settings of the [General](../../foundry-osd/general.md) page, then recreate or update the media.
3. Or change the storage controller mode in the firmware setup, for example from RAID or VMD to AHCI, if your organization allows it.
4. Restart from the deployment media: the list is read once, when Foundry Deploy opens.

**Collect:** the standard set, and the storage controller model.

## "Blocked: system disk", "Blocked: boot disk", "Blocked: read-only" or "Blocked: offline" <a href="#disk-blocked" id="disk-blocked"></a>

**Where:** **Target device**, at the end of a line of **Target disk**, and under the list once the disk is selected.

**Cause:** Windows PE reports that it started from this disk, or that the disk is read-only or offline. Foundry does not let you select such a disk.

**Fix:**

1. **Blocked: system disk** or **Blocked: boot disk**: start the device from the USB drive, the ISO or PXE instead of this disk.
2. **Blocked: read-only**: remove the hardware write protection, or clear the attribute with DiskPart (`attributes disk clear readonly`) from a Windows installation or recovery media that gives you a command prompt.
3. **Blocked: offline**: check the controller or RAID configuration, or bring the disk online with DiskPart (`online disk`) in the same way.
4. Restart from the deployment media.

**Collect:** the standard set, and the complete **Target disk** line.

## "The disk identity is missing, ambiguous or has changed. Restart Foundry Deploy and select the disk again." <a href="#disk-identity" id="disk-identity"></a>

**Where:** under **Target disk**; as a **Status** row in the **Target device** category of the summary; or as the error of **Check deployment setup** or **Prepare target disk**. In every case the disk has not been erased.

**Cause:** the disk does not report an identity that separates it from the other disks, or a disk was added, removed or renumbered after you selected the target. Foundry rechecks the disk just before erasing.

**Fix:**

1. Do not connect or disconnect any storage once Foundry Deploy is open.
2. Restart the device from the deployment media and select the disk again.
3. If the message stays, two disks report the same identity. Disconnect the disk you are not deploying to, or use another connection for it.

**Collect:** the standard set, and the list of disks connected to the device.

## Device inventory shows Unavailable in every field <a href="#hardware-not-detected" id="hardware-not-detected"></a>

**Where:** **Target device**, **Device inventory**.

**Cause:** Foundry Deploy could not read the hardware information in Windows PE. A computer name built from the serial number or the model cannot be generated, and the driver source falls back to **Microsoft Update Catalog**.

**Fix:**

1. Restart the device from the deployment media.
2. If it happens again on this model only, type the computer name, choose the driver source by hand and report the model.
3. If it happens on every device, the administrator recreates the media.

**Collect:** the standard set.

## A message under Computer name <a href="#computer-name" id="computer-name"></a>

**Where:** **Target device**, under **Computer name**: "Computer name must contain 1 to 15 valid characters (letters, numbers, or hyphen)." or a line such as "Serial number: Unavailable".

**Cause:** the name is empty, longer than 15 characters, contains another character, or is made of digits only. "\<value>: Unavailable" means the naming rule uses a device value that this device does not report, or reports as a placeholder.

**Fix:**

1. If the field accepts typing, enter a complete valid name. A numeric serial number needs a prefix, for example `PC-`.
2. If the field is read-only, the administrator locked the generated name: they change the rule in [Machine naming](../../foundry-osd/customization/machine-naming.md) and recreate or update the media.

**Collect:** the message and the values shown under **Device inventory**.

## A message under Answer file <a href="#answer-file-rejected" id="answer-file-rejected"></a>

**Where:** **Target device**, under **Answer file**, and as a **Status** row in the summary. **Next** stays unavailable.

**Cause:** it depends on the message.

| Message | Cause |
| --- | --- |
| "The selected answer file is unavailable, invalid, or incompatible with the selected Windows architecture. Choose another file or rebuild the media." | One message for several cases: no part of the file applies to the architecture of the selected Windows image; the copy on the media is missing, damaged or cannot be decrypted; or, on Domain Join media, the file does not set exactly one fixed computer name or contains a `Microsoft-Windows-UnattendedJoin` component |
| "The configured default answer file is missing. Rebuild the boot media." | The media configuration names a default answer file that is not on the media |
| "This answer file conflicts with the configured Autopilot enrollment mode. Choose another file or change the media configuration." | The media uses Windows Autopilot with a JSON profile or the interactive upload, and the file contains a setting that prevents enrollment, such as a local account, automatic logon or a skipped OOBE. See [the settings that block Windows Autopilot](../../foundry-osd/customization/unattend.md#settings-that-block-windows-autopilot) |

**Fix:**

1. To deploy now, select another file in **Answer file**, or **Use Foundry settings** if your organization allows it.
2. First and third message: the administrator corrects the source file, selects **Refresh source** in [Unattend](../../foundry-osd/customization/unattend.md) and recreates the media. For Domain Join media, see [Domain Join troubleshooting](../domain-join.md).
3. Missing default: the administrator chooses a file or **Use Foundry settings** under **Deployment default** on the same page, then recreates the media.

**Collect:** the message and the name of the selected file. Do not attach the answer file: it can contain passwords.

## Next stays unavailable on Target device <a href="#next-unavailable" id="next-unavailable"></a>

**Where:** **Target device**. No message explains it.

**Cause:**

- The Windows catalog could not be downloaded during **Initializing components...**, so there is no Windows image to offer. Media that provides custom images still lets you continue.
- The answer file selection is not valid: see the entry above.

**Fix:**

1. Check the network: cable or Wi-Fi, proxy, firewall, date and time. See [Network and Foundry Connect troubleshooting](../network.md) and [Network endpoints](../../reference/network-endpoints.md).
2. Restart the device from the deployment media. Foundry Deploy has no button to reload the catalog.

**Collect:** the standard set. The log contains "Operating system catalog load failed" with the reason.

## A message under the custom image selectors <a href="#custom-image-selection" id="custom-image-selection"></a>

**Where:** **Operating system**, with **Image source** set to **Custom image**. **Next** stays unavailable until an image and an index are selected.

**Cause and fix:** they depend on the message.

| Message | Cause | Fix |
| --- | --- | --- |
| "No custom images were found. Attach the correct media or add a WIM to the documented USB folder." | The drive that holds the images is not connected | Connect the complete USB drive or ISO created with this boot image, then select **Windows catalog** and **Custom image** again |
| "The custom image manifest is missing, damaged, or does not match this boot media. Recreate the media or choose another source." | The image files and the boot image come from different media builds | The administrator recreates the media |
| "The configured default image or index is unavailable. Choose an image and index explicitly." | The administrator's preferred image or index is not on this media | Select an image and an index yourself |
| "The default image exists on more than one attached volume. Choose the source volume explicitly." | Two connected drives carry the same image | Select the one to use, or disconnect the other drive |
| "The image cannot be read or its selected index or metadata has changed. Select the image again and try again." | The file is damaged, was replaced, or its drive was removed | Select the image again |

**Collect:** the message and the list of drives connected to the device.

## "\<manufacturer>: no matching model or version" <a href="#no-matching-driver-pack" id="no-matching-driver-pack"></a>

**Where:** **Summary**, as a **Status** row in the **Drivers** category. **Deploy** stays unavailable.

**Cause:** **Driver source** is set to a manufacturer whose catalog has no pack for the selected model, Windows version and architecture.

**Fix:**

1. Select **Edit** on **Drivers**.
2. Set **Driver source** to **Microsoft Update Catalog**, which provides storage and network drivers only, or to **None**.
3. Or select another **Model** on purpose, if you know its pack suits the device.

**Collect:** the manufacturer, model and product shown under **Device inventory**, and the Windows version selected.

## Deploy stays unavailable, or returns to the summary <a href="#deploy-does-not-start" id="deploy-does-not-start"></a>

**Where:** **Summary**.

**Cause:** a selection is incomplete or was refused when you selected **Deploy**. Foundry adds a **Status** row to the category concerned. Answering **No** to **Confirm disk erase** also returns to the summary, with nothing changed.

**Fix:** read the **Status** rows, then:

1. **Target device**: correct the computer name, the [answer file](#answer-file-rejected) or the [disk](#disk-identity).
2. **Drivers**: see [the driver pack entry](#no-matching-driver-pack).
3. **Autopilot**: select a profile in the [Windows Autopilot step](../../foundry-deploy/autopilot.md).
4. "The domain join information is missing or not valid. Deployment has not started.": see [Domain Join troubleshooting](../domain-join.md).

**Collect:** the text of each **Status** row.
