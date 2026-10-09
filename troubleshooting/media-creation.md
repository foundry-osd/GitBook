# Media creation troubleshooting

Use this page when Foundry OSD does not let you create an ISO or a USB drive, when a creation ends with "Final media creation failed.", or when the media does not start a device. For the ADK page, the proxy, updates and the options of **General**, see [Foundry OSD application troubleshooting](foundry-osd.md).

A failure shows in a dialog titled **ISO creation is blocked** or **USB creation is blocked**, before anything is written, or in the **Operation complete** dialog, as "Final media creation failed." followed by the reason. Most reasons are in English whatever the language of Foundry OSD, and some are followed by the output of a Windows tool.

The log is `%ProgramData%\Foundry\Logs\Foundry.log`; its line "Final boot media operation failed" names the failed step. Windows image errors are also in `%SystemRoot%\Logs\DISM\dism.log`. See [Log locations](logs-and-support.md#log-locations).

## Find your symptom

| What you see | Go to section |
| --- | --- |
| **Create ISO** or **Create USB** cannot be selected, or a dialog lists reasons | [The button stays unavailable](#button-unavailable) |
| A dialog offering **Create anyway** | [Update Foundry OSD](#update-advisory) |
| "Not enough disk space to create the boot image..." | [Not enough disk space](#not-enough-disk-space) |
| "WinPE servicing is blocked..." | [Image cleanup not confirmed](#servicing-blocked) |
| "Windows blocked access to the WinPE image files...", "Failed to mount boot.wim." | [Image files are locked](#access-blocked) |
| "Failed to create WinPE workspace using copype.cmd.", a component "was not found", "PCA2023 requires..." | [The ADK lacks a component](#adk-component) |
| "Failed to retrieve...", "Failed to download...", "The transfer timed out..." | [A download fails](#download-failed) |
| A Windows source or driver package that is refused | [A downloaded package is refused](#package-refused) |
| "Custom drivers total at least...", "The USB BOOT partition needs approximately..." | [Drivers or boot files are too large](#too-large) |
| "Foundry could not replace the ISO file...", "Unexpected failure while creating WinPE ISO media." | [The ISO cannot be written](#iso-failed) |
| The USB drive is not in the list | [The USB drive is not listed](#usb-not-listed) |
| "The USB drive identity is missing, ambiguous or has changed..." | [The USB drive changed](#usb-identity) |
| "Failed to partition and format the USB disk." or another USB write failure | [The USB drive cannot be written](#usb-write) |
| "Selected USB media is not a Foundry USB media." | [The USB drive cannot be updated](#usb-update) |
| "Custom Windows image media preparation failed." | [Content cannot be copied to the media](#media-content) |
| The device does not start from the media | [The device does not start](#does-not-boot) |
| After a PXE start, "Post-installation preparation failed..." | [PXE start](#pxe) |

## The button stays unavailable <a href="#button-unavailable" id="button-unavailable"></a>

**Where:** **Start**, **Create media** card. No message is shown.

**Cause:**

- A row is marked **Needs attention**; the bar at the top reads **Readiness items needing action:** with a number.
- For **Create ISO**: the **ISO output** path is empty or does not end with `.iso`.
- For **Create USB**: no drive is selected, or the drive is smaller than 16 GB.

**Fix:**

1. In each group, select the **Review** button of every row marked **Needs attention** and complete that page.
2. Type a path that ends with `.iso`, or select **Browse**.
3. Select a USB drive of 16 GB or more.

If something changes between your click and the start, a dialog titled **ISO creation is blocked** or **USB creation is blocked** lists the same reasons, for example "ISO output path must end with .iso." or "Deploy configuration generation is not ready.". For the latter with Windows Autopilot, see [Windows Autopilot troubleshooting](autopilot.md).

**Collect:** Nothing.

## "Update Foundry OSD before creating boot media" <a href="#update-advisory" id="update-advisory"></a>

**Where:** Dialog shown when you select **Create ISO**, **Create USB** or **Update USB**.

**Cause:** A newer Foundry OSD is available. This is advice, not a failure.

**Fix:** Select **Apply update**, or **View update** while the update is not ready, then create the media with the new version. **Create anyway** uses the current version.

**Collect:** Nothing.

## "Not enough disk space to create the boot image. At least 20 GB of free space is required on \<volume\>. Available: \<size\>. Free up space and try again." <a href="#not-enough-disk-space" id="not-enough-disk-space"></a>

**Where:** Dialog **ISO creation is blocked** or **USB creation is blocked**. A second form is "The available disk space for boot image creation could not be verified on \<volume\>. Check that the location is accessible and try again."

**Cause:** Before every creation, Foundry OSD requires 20 GB free on each drive that holds `%ProgramData%\Foundry`, `%LocalAppData%\Foundry`, `%TEMP%` or `%SystemRoot%\Temp`, and on the drive of the ISO file.

**Fix:** Free space on the volume named in the message. For an ISO, you can also choose another output folder, or check that the network share answers.

**Collect:** `Foundry.log`, line "Boot media creation was blocked by local storage validation".

## "WinPE servicing is blocked because an earlier image cleanup has no confirmed completion." <a href="#servicing-blocked" id="servicing-blocked"></a>

**Where:** **Operation complete** dialog, at the start of a creation, followed by "Retained operation: '\<folder\>'. Cleanup marker: '\<file\>'. Preserve this workspace until cleanup can be verified." and a numbered procedure.

**Cause:** During an earlier creation, Windows did not confirm that the boot image was unmounted: the DISM command exceeded 15 minutes, or Foundry OSD was closed while it ran. Foundry OSD keeps that working folder and services no other image until the old one is released.

Each creation first tries to recover: the block is lifted if Windows no longer reports the image as mounted; otherwise Foundry OSD discards the image once, which can take 15 minutes. You see the message only when both fail, so try once more first.

**Fix:** The dialog gives the procedure with the real path. If another Foundry media operation is still running, let it finish and start the operation again. Otherwise, recover manually:

1. Restart Windows.
2. From an elevated command prompt, run: `dism /Unmount-Image /MountDir:"<mount path>" /Discard`
3. Run: `dism /Cleanup-Mountpoints`
4. Start the operation again.

When the dialog says that the cleanup marker does not name a mount directory, it shows other steps: after the restart, run `dism /Get-MountedImageInfo`, discard each mount directory listed inside the retained operation, run `dism /Cleanup-Mountpoints`, delete the cleanup marker file, then start again.

Do not delete the retained folder yourself.

**Collect:** The full text of the dialog, `Foundry.log` and `dism.log`.

## "Windows blocked access to the WinPE image files. Security software or another tool may be locking them. Add a security software exclusion for \<folder\>, close tools that mount images, restart the computer, and try again. Details are in \<log\> and \<log\>." <a href="#access-blocked" id="access-blocked"></a>

**Where:** **Operation complete** dialog. Related forms, followed by the output of DISM: "Failed to mount boot.wim.", "Failed to commit mounted boot.wim changes." and "Failed to discard mounted boot.wim changes."

**Cause:**

- Antivirus or endpoint protection software scans or locks files under `%ProgramData%\Foundry`.
- Another program has the working folder open or an image mounted.

**Fix:**

1. Add an exclusion for `%ProgramData%\Foundry` in your security software.
2. Close imaging tools, terminals and File Explorer windows opened in that folder.
3. Restart the workstation and create the media again. If it then reports "WinPE servicing is blocked...", see [the previous entry](#servicing-blocked).

**Collect:** `Foundry.log` and `dism.log`.

## "Failed to create WinPE workspace using copype.cmd." <a href="#adk-component" id="adk-component"></a>

**Where:** **Operation complete** dialog. Other forms: "WinPE workspace was created but boot.wim was not found.", "The selected WinPE language pack was not found.", "The WinPE optional components folder was not found.", "The required '\<name\>' WinPE optional component was not found.", "OA3Tool executable was not found for the selected WinPE architecture.", "PCA2023 requires /bootex support in the WinPE workspace." and "PCA2023 USB creation requires BootEx EFI binaries in the WinPE workspace."

**Cause:**

- A file of the Windows ADK or of the Windows PE add-on is missing or damaged for the architecture selected in **General**.
- For the two PCA2023 messages: **Secure Boot** is on in **General**, and the installed ADK cannot produce media signed with **PCA 2023**.

**Fix:**

1. Open the [ADK](../foundry-osd/adk.md) page and repair or reinstall what it reports. The accepted version is in [Windows ADK](../reference/supported-versions.md#windows-adk).
2. For the PCA2023 messages, you can instead turn **Secure Boot** off in [General](../foundry-osd/general.md) to sign with **PCA 2011**.

**Collect:** `Foundry.log`, and the text shown after the message.

## "Failed to retrieve the WinPE driver catalog." <a href="#download-failed" id="download-failed"></a>

**Where:** **Operation complete** dialog. Other forms: "Failed to parse the WinPE driver catalog.", "Driver package download failed.", "Failed to download driver package.", "Failed to download the operating system catalog.", "Failed to acquire a verified Windows source package.", "Failed to prepare Foundry runtime payloads.", "Failed to provision Foundry runtime payloads." and "The transfer timed out. Check your connection and try again."

**Cause:** The workstation cannot reach a host this build needs, or a download received no data for two minutes.

- The [catalogs](../reference/catalog.md) on `raw.githubusercontent.com` are needed only with **Dell** or **HP** drivers, Wi-Fi or `arm64`. A build can therefore fail the day after you turn on Wi-Fi.
- The other hosts are GitHub and the download servers of Dell, HP and Microsoft.

**Fix:**

1. Open **Settings** > **Proxy**, set the proxy of your network, select **Test connection**, then **Apply**.
2. Allow the workstation hosts listed in [Network endpoints](../reference/network-endpoints.md).
3. Create the media again. Files already downloaded are reused.

**Collect:** `Foundry.log`.

## "Failed to prepare boot image dependencies from every matching operating system source." <a href="#package-refused" id="package-refused"></a>

**Where:** **Operation complete** dialog. Other forms: "No Windows 11 24H2 Windows source matched the requested architecture and language.", "The cached Windows source package failed hash validation.", "Failed to extract driver package with bundled 7-Zip.", "Executable driver package was extracted with 7-Zip but no INF files were found.", "Unsupported driver package format." and "Failed to inject driver package into the mounted image."

**Cause:**

- Media with Wi-Fi or `arm64` takes files from a Windows 11 package of several GB, chosen for the **Architecture** and the **WinPE boot language** of **General**. No package exists for that language, or the downloaded one is damaged.
- A downloaded **Dell** or **HP** driver package is damaged.
- A driver of your **Custom driver folder** is refused by Windows PE.

**Fix:**

1. For "No Windows 11 24H2 Windows source matched...", choose a **WinPE boot language** in which Windows 11 is published.
2. Otherwise, close Foundry OSD, delete `%ProgramData%\Foundry\Cache\WindowsSources` or `%ProgramData%\Foundry\Cache\WinPeDrivers`, and create the media again.
3. If a custom driver is refused, remove the driver named in `dism.log` from the folder.

**Collect:** `Foundry.log` and `dism.log`.

## "Custom drivers total at least \<size\>; the maximum is \<size\>. Select only the network and storage drivers needed by Windows PE." <a href="#too-large" id="too-large"></a>

**Where:** **Operation complete** dialog. Other forms: "Custom driver snapshots exceed the entry limit or contain a reparse point.", "The USB BOOT partition needs approximately \<size\>, but its capacity is \<size\>. Nothing has been erased or formatted. Reduce customizations or drivers, or create an ISO.", "The USB BOOT partition capacity could not be verified. Nothing has been erased or formatted. Check and reconnect the USB drive, then retry. Also check that the source files are accessible." and "A boot media file is \<size\>; FAT32 supports at most \<size\> per file. Reduce the boot image size or create an ISO."

**Cause:**

- The **Custom driver folder**, subfolders included, exceeds 2 GiB or 10,000 files and folders, or contains a junction or a symbolic link. The size shown is what was counted when the check stopped.
- On a USB drive, the boot files do not fit the **BOOT** partition, which is 2 GiB whatever the size of the drive.

**Fix:**

1. Point **Custom driver folder** to a plain folder with only the network and storage drivers Windows PE needs, and turn off the **Driver options** you do not need.
2. For the **BOOT** partition messages, you can instead [create an ISO](../foundry-osd/media/create-iso.md).
3. For "could not be verified", reconnect the drive, select **Refresh** and start again.

**Collect:** Nothing.

## "Foundry could not replace the ISO file at \<path\>. The file may be mounted, attached to a virtual machine, open in another program, or read-only. Unmount or close it, or choose another output path, then try again." <a href="#iso-failed" id="iso-failed"></a>

**Where:** **Operation complete** dialog, at the end of an ISO creation. Another form is "Unexpected failure while creating WinPE ISO media.", followed by a technical text: read its first line.

**Cause:**

- First message: the previous ISO of the same name is in use and could not be replaced. It is intact.
- "Insufficient free space for custom-image ISO staging and atomic output publication.": the working drive or the output drive lacks room. The wording mentions custom images even when you use none.
- "The custom-image ISO exceeds the FAT32 output file-size limit.": the ISO exceeds 4 GiB and the output drive is FAT32.
- "The ADK Oscdimg tool is required to create media containing custom images.": a tool of the ADK is missing.

**Fix:**

1. Eject the ISO in File Explorer or detach it from the virtual machine, or type another path in **ISO output**.
2. Free space in `%ProgramData%\Foundry\Workspaces` and in the output folder.
3. Save the ISO on an NTFS drive or a network share.
4. Repair the ADK from the [ADK](../foundry-osd/adk.md) page.

**Collect:** The full text of the dialog and `Foundry.log`.

## The USB drive is not listed <a href="#usb-not-listed" id="usb-not-listed"></a>

**Where:** **Start**, **USB target** card, which reads "No USB drives were found.", "USB targets can be refreshed after ADK readiness is complete.", "USB target refresh failed." or "A required PowerShell USB query command failed."

**Cause:**

- The list was not refreshed after the drive was connected.
- The drive is not connected through USB, is the system disk, or is reported by Windows as not removable, as some USB hard disks and SSD enclosures are.
- The ADK is not ready, or the Windows storage commands failed.

**Fix:** Select **Refresh**. Use a removable USB flash drive, on another port if needed. If the card names the ADK, open the [ADK](../foundry-osd/adk.md) page.

**Collect:** `Foundry.log` when the card reports a failure.

## "The USB drive identity is missing, ambiguous or has changed. Refresh the USB drive list and select the drive again." <a href="#usb-identity" id="usb-identity"></a>

**Where:** Dialog **USB creation is blocked**, or **Operation complete** dialog. A related form is "The selected USB drive is no longer safe to modify. Refresh the USB drive list and select another USB drive."

**Cause:** Foundry OSD checks the drive again before it writes, and stops when it is not sure to hold the drive you selected. Nothing has been erased.

- The drive was unplugged, swapped or reconnected.
- Two identical drives without a serial number are connected.
- Second message: the disk is no longer a removable USB disk of 16 GB or more.

**Fix:** Disconnect other USB drives of the same model, select **Refresh**, select the drive again, and keep it connected until the end.

**Collect:** `Foundry.log`.

## "Failed to partition and format the USB disk." <a href="#usb-write" id="usb-write"></a>

**Where:** **Operation complete** dialog, followed by the output of the Windows storage commands, such as "Timed out waiting for BOOT volume X: to become available." Other forms: "Failed to format the USB BOOT partition.", "Failed to copy WinPE media files to USB BOOT partition." and "USB verification failed: boot.wim not found.", with "BCD" or "EFI boot file" in place of "boot.wim".

**Cause:**

- The drive is write-protected by a switch, or is failing.
- A program uses the drive, such as a File Explorer window or an antivirus scan.
- Windows has no free drive letter to assign.

**Fix:**

1. Close what uses the drive, check its write-protection switch, and free a drive letter if all are in use.
2. Start again: a drive being created is created again, a drive being updated is [updated](../foundry-osd/media/update-usb.md) again.
3. If it fails again, use another USB port, then another drive.

**Collect:** The full text of the dialog and `Foundry.log`.

## "Selected USB media is not a Foundry USB media." <a href="#usb-update" id="usb-update"></a>

**Where:** **Operation complete** dialog, during **Update USB**. A related form is "USB provisioning did not return assigned drive letters."

**Cause:**

- First message: the **BOOT** volume was renamed or reformatted.
- Second message: the **Foundry Cache** volume has no drive letter in Windows.

**Fix:**

1. For the second message, assign a drive letter to **Foundry Cache** in Windows Disk Management, select **Refresh**, and update again.
2. For the first, delete the partitions of the drive in Windows Disk Management and [create the drive](../foundry-osd/media/create-usb.md) again. Its downloads are lost.

**Collect:** The full text of the dialog.

## "Custom Windows image media preparation failed." <a href="#media-content" id="media-content"></a>

**Where:** **Operation complete** dialog of a USB operation, followed by a second line. The message is used for custom images and for post-installation content alike, even when you use no custom image.

**Cause and fix, by second line:**

| Second line | Cause | Fix |
| --- | --- | --- |
| "The USB disk has insufficient capacity for BOOT, custom images and runtime payloads." | The drive is too small for what you included. | Use a larger drive, or include fewer [custom images](../foundry-osd/customization/custom-windows-images.md) |
| "The data volume has insufficient free space for the custom images and runtime payloads." or `PreOobe.InsufficientMediaSpace` | During an update, the cache partition lacks room. Content of earlier builds is never removed. | Free space on **Foundry Cache**, or create the drive again |
| "A media input is on the target disk or its physical disk could not be verified." | A source file is stored on the USB drive being written. | Keep the sources on the workstation |
| "A custom image input no longer matches its verified length and SHA256." or `PreOobe.PackageContentChanged` | A file of the local library changed after its import. | Import the image again, or the content again in **Edit action** of [Post-installation](../foundry-osd/customization/post-installation.md) |

**Collect:** The full text of the dialog and `Foundry.log`.

## The device does not start from the media <a href="#does-not-boot" id="does-not-boot"></a>

**Where:** Target device. The creation succeeded, but the device ignores the media, shows a Secure Boot error, or returns to its boot menu. Foundry shows nothing at this stage.

**Cause:** Foundry cannot tell which one applies:

- The **Architecture** of the media does not match the device: `x64` media on an ARM64 device, or the reverse.
- The device has Secure Boot turned on and its firmware does not yet trust the **PCA 2023** certificate that signs the boot files. See **Secure Boot** in [General](../foundry-osd/general.md).
- The firmware does not offer a USB drive with the chosen **USB partition style**.
- A USB update was cancelled or failed, and **BOOT** is incomplete.

**Fix:**

1. Compare the device with **General** > **Architecture and signature**.
2. Turn **Secure Boot** off in **General** to sign with **PCA 2011**, then update the USB drive or create the ISO again.
3. Delete the partitions of the USB drive in Windows Disk Management and create it again with the other **USB partition style**.
4. Run **Update USB** again until it ends with "USB boot partition was updated successfully."

If a console titled **Foundry Bootstrap** appears, the device did start from the media: continue with [Windows PE startup troubleshooting](windows-pe-startup.md).

**Collect:** The model of the device, its firmware mode and Secure Boot state, and the values of **Architecture**, **Secure Boot** and **USB partition style**.

## PXE: "Post-installation preparation failed. Check the deployment log for details." <a href="#pxe" id="pxe"></a>

**Where:** Foundry Deploy, on a device started from a PXE server such as Windows Deployment Services, before the disk is erased. The deployment log contains "The referenced post-installation media generation is unavailable."

**Cause:** A PXE server delivers only `sources\boot.wim`. The deployment has a Post-installation action that uses imported files, and those files stayed on the ISO.

**Fix:**

1. Attach the complete ISO the boot image was copied from, and start the deployment again.
2. Or disable the actions that use imported files in Foundry OSD, create the ISO again and import its new `sources\boot.wim` in the PXE server.

See [Deploy with PXE](../foundry-osd/media/pxe-deployment.md).

**Collect:** The Foundry Deploy log. See [Logs and support information](logs-and-support.md).
