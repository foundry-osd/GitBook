# USB drive and device start troubleshooting

Use this page for a USB drive that Foundry OSD does not list, cannot write or cannot update, for media that a device does not start from, and for a start from a PXE server. For a creation that fails before the drive is written, see [Media creation troubleshooting](../media-creation.md).

Messages appear in the **Operation complete** dialog of Foundry OSD, as "Final media creation failed." followed by the reason. The log is `%ProgramData%\Foundry\Logs\Foundry.log`: see [Log locations](../logs-and-support.md#log-locations).

## Find your symptom

| What you see | Go to section |
| --- | --- |
| The USB drive is not in the list of the **USB target** card | [The USB drive is not listed](#usb-not-listed) |
| An external disk is in the list, or the **Format USB target** dialog lists volumes you do not recognize | [A disk you did not expect is offered](#unexpected-disk) |
| "The USB drive identity is missing, ambiguous or has changed..." or "The selected USB drive is no longer safe to modify..." | [The USB drive changed](#usb-identity) |
| "Failed to partition and format the USB disk." or another failure while the drive is written | [The USB drive cannot be written](#usb-write) |
| "Selected USB media is not a Foundry USB media." or "USB provisioning did not return assigned drive letters." | [The USB drive cannot be updated](#usb-update) |
| "Custom Windows image media preparation failed." | [Content cannot be copied to the media](#media-content) |
| The device ignores the media, shows a Secure Boot error, or returns to its boot menu | [The device does not start from the media](#does-not-boot) |
| After a start from a PXE server, "Post-installation preparation failed..." | [PXE start](#pxe) |

## The USB drive is not listed <a href="#usb-not-listed" id="usb-not-listed"></a>

**Where:** Foundry OSD, **Start**, **USB target** card, which reads "No USB drives were found.", "USB targets can be refreshed after ADK readiness is complete.", "USB target refresh failed." or "A required PowerShell USB query command failed."

**Cause:**

- The list was not refreshed after the drive was connected.
- The drive is not connected through USB, or it is the Windows system or boot disk. Foundry OSD lists every other disk connected through USB, external hard disks and SSDs included.
- The ADK is not ready, or the Windows storage commands failed.

**Fix:**

1. Select **Refresh**.
2. Connect the drive directly to a USB port of the workstation, another one if needed.
3. If the card names the ADK, open the [ADK](../../foundry-osd/adk.md) page.

A drive smaller than 16 GB is listed, but **Create USB** stays unavailable while it is selected.

**Collect:** `Foundry.log` when the card reports a failure.

## A disk you did not expect is offered <a href="#unexpected-disk" id="unexpected-disk"></a>

**Where:** Foundry OSD, **Start**: the list of the **USB target** card, or the **Format USB target** dialog, whose "Volumes on this disk:" lines show a letter, a label or an amount of data you do not recognize.

**Cause:** Foundry OSD offers every disk connected through USB that is not the Windows system or boot disk. An external hard disk or SSD, such as a backup disk, is a valid target and is erased like a flash drive.

**Fix:**

1. In the dialog, select **Cancel**. It is the default button, and nothing has been erased.
2. Disconnect the disks you do not want to erase, select **Refresh**, and select the drive again.
3. Read the dialog again before you select **Format and create USB**. [Create a USB drive](../../foundry-osd/media/create-usb.md) explains each line.

**Collect:** Nothing.

## "The USB drive identity is missing, ambiguous or has changed." <a href="#usb-identity" id="usb-identity"></a>

**Where:** Dialog **USB creation is blocked**, or **Operation complete** dialog. The full text is "The USB drive identity is missing, ambiguous or has changed. Refresh the USB drive list and select the drive again." A related form is "The selected USB drive is no longer safe to modify. Refresh the USB drive list and select another USB drive."

**Cause:** Foundry OSD checks the disk again before it writes, and stops when it is not sure to hold the disk you selected. Nothing has been erased.

- The drive was unplugged, swapped or reconnected.
- Two identical drives without a serial number are connected.
- Second message: the disk is no longer a USB disk of 16 GB or more, or has become the system or boot disk.

**Fix:** Disconnect other USB drives of the same model, select **Refresh**, select the drive again, and keep it connected until the end.

**Collect:** `Foundry.log`.

## "Failed to partition and format the USB disk." <a href="#usb-write" id="usb-write"></a>

**Where:** **Operation complete** dialog. This entry covers these messages:

| Message | When |
| --- | --- |
| "Failed to partition and format the USB disk." | While a new drive is created. It is followed by the output of the Windows storage commands, such as "Timed out waiting for BOOT volume X: to become available." |
| "Failed to format the USB BOOT partition." | While a drive is updated |
| "Failed to copy WinPE media files to USB BOOT partition." | While the boot files are copied |
| "USB verification failed: boot.wim not found.", "USB verification failed: BCD not found." or "USB verification failed: EFI boot file not found." | When Foundry OSD checks the drive after writing it |

**Cause:**

- The drive is write-protected by a switch, or is failing.
- A program uses the drive, such as a File Explorer window or an antivirus scan.
- Windows has no free drive letter to assign.

**Fix:**

1. Close what uses the drive, check its write-protection switch, and free a drive letter if all are in use.
2. Start again: a drive being created is created again, a drive being updated is [updated](../../foundry-osd/media/update-usb.md) again.
3. If it fails again, use another USB port, then another drive.

**Collect:** The full text of the dialog and `Foundry.log`.

## "Selected USB media is not a Foundry USB media." <a href="#usb-update" id="usb-update"></a>

**Where:** **Operation complete** dialog, during **Update USB**. A related form is "USB provisioning did not return assigned drive letters."

**Cause:**

- First message: the **BOOT** volume was renamed or reformatted.
- Second message: the **Foundry Cache** volume has no drive letter in Windows.

**Fix:**

1. For the second message, assign a drive letter to **Foundry Cache** in Windows Disk Management, select **Refresh**, and update again.
2. For the first, delete the partitions of the drive in Windows Disk Management and [create the drive](../../foundry-osd/media/create-usb.md) again. Its downloads are lost.

**Collect:** The full text of the dialog.

## "Custom Windows image media preparation failed." <a href="#media-content" id="media-content"></a>

**Where:** **Operation complete** dialog of a USB operation, followed by a second line. The message is used for custom images and for post-installation content alike, even when you use no custom image.

**Cause and fix, by second line:**

| Second line | Cause | Fix |
| --- | --- | --- |
| "The USB disk has insufficient capacity for BOOT, custom images and runtime payloads." | The drive is too small for what you included. | Use a larger drive, or include fewer [custom images](../../foundry-osd/customization/custom-windows-images.md) |
| "The data volume has insufficient free space for the custom images and runtime payloads." or `PreOobe.InsufficientMediaSpace` | During an update, the cache partition lacks room. Content of earlier builds is never removed. | Free space on **Foundry Cache**, or create the drive again |
| "A media input is on the target disk or its physical disk could not be verified." | A source file is stored on the USB drive being written. | Keep the sources on the workstation |
| "A custom image input no longer matches its verified length and SHA256." or `PreOobe.PackageContentChanged` | A file of the local library changed after its import. | Import the image again, or the content again in **Edit action** of [Post-installation](../../foundry-osd/customization/post-installation.md) |

**Collect:** The full text of the dialog and `Foundry.log`.

## The device does not start from the media <a href="#does-not-boot" id="does-not-boot"></a>

**Where:** Target device. The creation succeeded, but the device ignores the media, shows a Secure Boot error, or returns to its boot menu.

**Cause:** Foundry shows nothing at this stage and cannot tell which of these applies. The first two follow from how Foundry builds the media; the last two depend on the firmware of the device and are possibilities to test, not facts Foundry establishes.

- The **Architecture** of the media does not match the device: `x64` media on an ARM64 device, or the reverse.
- A USB update was cancelled or failed, and **BOOT** is incomplete.
- On a device with Secure Boot turned on in its firmware, the firmware may not yet trust the certificate that signs the boot files, **PCA 2023** by default. Microsoft's guidance is linked from [General](../../foundry-osd/general.md).
- Some firmware may not list a USB drive with the chosen **USB partition style**.

**Fix:**

1. Compare the device with **Architecture and signature** in **General**.
2. Run **Update USB** again until it ends with "USB boot partition was updated successfully."
3. As a test, turn off the **Secure Boot** switch of **General**, which then reads **PCA 2011**, and create the ISO or update the USB drive again. This changes how the media is signed, not the firmware of the device.
4. As a last test, delete the partitions of the USB drive in Windows Disk Management and create it again with the other **USB partition style**. This erases the drive and its downloads. With `arm64`, only **GPT** exists.

If a console titled **Foundry Bootstrap** appears, the device did start from the media: continue with [Windows PE startup troubleshooting](../windows-pe-startup.md).

**Collect:** The model of the device, whether Secure Boot is on in its firmware, and the values of **Architecture**, the **Secure Boot** switch and **USB partition style** used for the media.

## PXE: "Post-installation preparation failed. Check the deployment log for details." <a href="#pxe" id="pxe"></a>

**Where:** Foundry Deploy, on a device started from a PXE server such as Windows Deployment Services, before the disk is erased. The deployment log contains "The referenced post-installation media generation is unavailable."

**Cause:** A PXE server delivers only `sources\boot.wim`. The deployment has a Post-installation action that uses imported files, and those files stayed on the ISO.

**Fix:**

1. Attach the complete ISO the boot image was copied from, and start the deployment again.
2. Or disable the actions that use imported files in Foundry OSD, create the ISO again and import its new `sources\boot.wim` in the PXE server.

See [Deploy with PXE](../../foundry-osd/media/pxe-deployment.md).

**Collect:** The Foundry Deploy log. See [Logs and support information](../logs-and-support.md).
