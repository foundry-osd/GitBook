# Media creation troubleshooting

Use this page when Foundry OSD does not let you create an ISO or a USB drive, when a creation ends with "Final media creation failed.", or when the media does not start a device. For the ADK, the proxy, updates and the options of **General**, see [Foundry OSD application troubleshooting](foundry-osd.md).

A failure shows in one of two places:

- A dialog titled **ISO creation is blocked** or **USB creation is blocked**, before anything is written.
- The **Operation complete** dialog, as "Final media creation failed." followed by the reason. Most reasons are in English whatever the language of Foundry OSD, and some are followed by the raw output of a Windows tool.

Foundry OSD writes its log to `%ProgramData%\Foundry\Logs\Foundry.log`; the line "Final boot media operation failed" carries the failed step. Windows image errors are also in `%SystemRoot%\Logs\DISM\dism.log`. See [Log locations](logs-and-support.md#log-locations).

## Find your symptom

| What you see | Go to section |
| --- | --- |
| **Create ISO** or **Create USB** cannot be selected | [The button stays unavailable](#button-unavailable) |
| A dialog offering **Create anyway** | [Update Foundry OSD before creating boot media](#update-advisory) |
| "Not enough disk space to create the boot image..." | [Not enough disk space](#not-enough-disk-space) |
| "WinPE servicing is blocked..." | [An earlier image cleanup has no confirmed completion](#servicing-blocked) |
| "Windows blocked access to the WinPE image files..." | [Windows blocked access](#access-blocked) |
| "Failed to create WinPE workspace using copype.cmd.", or a component "was not found" | [An ADK component is missing](#adk-component) |
| "PCA2023 requires /bootex support..." | [PCA2023 requires /bootex support](#bootex) |
| "Failed to mount boot.wim." or "Failed to commit..." | [The boot image cannot be mounted or saved](#mount-failed) |
| "Failed to retrieve...", "Failed to download...", "Failed to prepare Foundry runtime payloads." | [A catalog or a download cannot be reached](#download-failed) |
| "The transfer timed out..." | [The transfer timed out](#timed-out) |
| A message about the Windows source for a Wi-Fi or `arm64` build | [The Windows package for the boot image is refused](#windows-source) |
| A driver package that cannot be extracted or injected | [A driver cannot be added](#driver-failed) |
| "Custom drivers total at least..." or "...exceed the entry limit..." | [The custom driver folder is too large](#drivers-too-large) |
| "Foundry could not replace the ISO file..." | [The ISO file cannot be replaced](#iso-locked) |
| "Unexpected failure while creating WinPE ISO media." | [Unexpected failure while creating the ISO](#iso-unexpected) |
| The USB drive is not in the list | [The USB drive is not listed](#usb-not-listed) |
| "The USB drive identity is missing, ambiguous or has changed..." or "...no longer safe to modify..." | [The USB drive changed](#usb-identity) |
| "The USB BOOT partition needs approximately..." | [The boot files do not fit](#boot-capacity) |
| "Failed to partition and format the USB disk." or another USB write failure | [The USB drive cannot be written](#usb-write) |
| "Selected USB media is not a Foundry USB media." or "...did not return assigned drive letters." | [The USB drive cannot be updated](#usb-update) |
| "Custom Windows image media preparation failed." | [Content cannot be copied to the USB drive](#usb-content) |
| The device does not start from the media | [The device does not start from the media](#does-not-boot) |
| After a PXE start, "Post-installation preparation failed..." | [PXE: post-installation preparation failed](#pxe) |

## The button stays unavailable <a href="#button-unavailable" id="button-unavailable"></a>

**Where:** **Start**, **Create media** card. No message is shown.

**Cause:**

- A row is marked **Needs attention**. The bar at the top reads **Readiness items needing action:** with a number.
- For **Create ISO**: the **ISO output** path is empty or does not end with `.iso`.
- For **Create USB**: no drive is selected, or the drive is smaller than 16 GB.

**Fix:**

1. Expand each group and select the **Review** button of every row marked **Needs attention**. For a **Windows Autopilot** row, see [Windows Autopilot troubleshooting](autopilot.md); for a **General** row, see [Foundry OSD application troubleshooting](foundry-osd.md).
2. Type a path that ends with `.iso`, or select **Browse**.
3. Select a USB drive of 16 GB or more.

If the state changes between your click and the start, the same causes appear in a dialog titled **ISO creation is blocked** or **USB creation is blocked**, for example "ISO output path must end with .iso." or "The selected USB target is too small. Use a USB key of at least 16 GB.".

**Collect:** Nothing.

## "Update Foundry OSD before creating boot media" <a href="#update-advisory" id="update-advisory"></a>

**Where:** Dialog shown when you select **Create ISO**, **Create USB** or **Update USB**. It names the version in use and the version available.

**Cause:** A newer Foundry OSD is available. This is advice, not a failure.

**Fix:** Select **Apply update** to install it and restart Foundry OSD, then create the media. The button reads **View update** while the update is not ready to be applied; it opens the update page of **Settings**. **Create anyway** creates the media with the current version; **Cancel** does nothing.

**Collect:** Nothing.

## "Not enough disk space to create the boot image. At least 20 GB of free space is required on \<volume\>. Available: \<size\>. Free up space and try again." <a href="#not-enough-disk-space" id="not-enough-disk-space"></a>

**Where:** Dialog **ISO creation is blocked** or **USB creation is blocked**. A second form is "The available disk space for boot image creation could not be verified on \<volume\>. Check that the location is accessible and try again."

**Cause:** Before every creation, Foundry OSD requires 20 GB free on each drive that holds one of these locations:

- `%ProgramData%\Foundry\Workspaces`, and `%ProgramData%\Foundry\Cache` when the media uses Wi-Fi, `arm64`, or **Dell** or **HP** drivers
- `%LocalAppData%\Foundry\BuildSnapshots`
- `%TEMP%` and `%SystemRoot%\Temp`
- the folder of the ISO file, for an ISO

**Fix:**

1. Free space on the volume named in the message.
2. For an ISO, choose an output folder on a drive with 20 GB free, or check that the network share answers.

**Collect:** `Foundry.log`, line "Boot media creation was blocked by local storage validation".

## "WinPE servicing is blocked because an earlier image cleanup has no confirmed completion." <a href="#servicing-blocked" id="servicing-blocked"></a>

**Where:** **Operation complete** dialog, at the start of a creation. The message goes on with "Retained operation: '\<folder\>'. Cleanup marker: '\<file\>'. Preserve this workspace until cleanup can be verified." and a numbered procedure.

**Cause:** During an earlier creation, Windows did not confirm that the boot image was unmounted: the DISM command exceeded 15 minutes, or Foundry OSD was closed while it ran. Foundry OSD keeps that working folder under `%ProgramData%\Foundry\Workspaces` and refuses to service another image until the old one is released.

At the start of each creation, Foundry OSD first tries to recover by itself:

- If Windows no longer reports the image as mounted, the block is lifted and the old folder is deleted.
- If the image is still mounted, Foundry OSD discards it once, which can take up to 15 minutes.

You see this message only when both fail. Each new attempt runs this recovery again, so try once more before the manual procedure.

**Fix:** The dialog shows the procedure, with the real path. If another Foundry media operation is still running, let it finish and start the operation again. Otherwise, recover manually:

1. Restart Windows.
2. From an elevated command prompt, run: `dism /Unmount-Image /MountDir:"<mount path>" /Discard`
3. Run: `dism /Cleanup-Mountpoints`
4. Start the operation again.

At that start, Foundry OSD finds the image released and removes the block.

When the dialog starts with "The cleanup marker does not name a mount directory inside this operation, so the cleanup cannot be verified automatically.", it shows five steps instead:

1. Restart Windows.
2. From an elevated command prompt, run: `dism /Get-MountedImageInfo`
3. For each mount directory listed inside the retained operation, run: `dism /Unmount-Image /MountDir:<mount directory> /Discard`
4. Run: `dism /Cleanup-Mountpoints`
5. Delete the cleanup marker file, then start the operation again.

Do not delete the retained folder yourself while an image may still be mounted in it.

A rarer form, "WinPE servicing is blocked because retained operation cleanup could not be safely inspected.", means Foundry OSD could not examine `%ProgramData%\Foundry\Workspaces`. The dialog adds the reason.

**Collect:** The full text of the dialog, `Foundry.log` and `dism.log`.

## "Windows blocked access to the WinPE image files. Security software or another tool may be locking them. Add a security software exclusion for \<folder\>, close tools that mount images, restart the computer, and try again. Details are in \<log\> and \<log\>." <a href="#access-blocked" id="access-blocked"></a>

**Where:** **Operation complete** dialog, while the working folder is created or the boot image is mounted.

**Cause:**

- Antivirus or endpoint protection software scans or locks files under `%ProgramData%\Foundry`.
- Another imaging tool has an image mounted or a handle open on the same files.

**Fix:**

1. Add an exclusion for `%ProgramData%\Foundry` in your security software.
2. Close other tools that mount Windows images, and File Explorer windows opened in that folder.
3. Restart the workstation and create the media again.

**Collect:** The two logs named in the message: `Foundry.log` and `dism.log`.

## "Failed to create WinPE workspace using copype.cmd." <a href="#adk-component" id="adk-component"></a>

**Where:** **Operation complete** dialog. Other forms: "WinPE workspace was created but boot.wim was not found.", "The selected WinPE language pack was not found.", "The WinPE optional components folder was not found.", "The required '\<name\>' WinPE optional component was not found.", and, with zero-touch Windows Autopilot, "OA3Tool executable was not found for the selected WinPE architecture."

**Cause:** A file of the Windows ADK or of the Windows PE add-on is missing or damaged for the architecture selected in **General**.

**Fix:**

1. Open the [ADK](../foundry-osd/adk.md) page and repair or reinstall the components it reports.
2. If the page reports **ADK is ready**, check that the architecture selected in **General** is the one you intend, then create the media again.

**Collect:** `Foundry.log`, and the text shown after the message.

## "PCA2023 requires /bootex support in the WinPE workspace." <a href="#bootex" id="bootex"></a>

**Where:** **Operation complete** dialog. For a USB drive: "PCA2023 USB creation requires BootEx EFI binaries in the WinPE workspace."

**Cause:** **Secure Boot** is on in **General**, which signs the boot files with **PCA 2023**, and the installed ADK cannot produce such media.

**Fix:**

1. Install the ADK version given in [Windows ADK](../reference/supported-versions.md#windows-adk) from the [ADK](../foundry-osd/adk.md) page.
2. Or turn **Secure Boot** off in **General** to use **PCA 2011**.

**Collect:** `Foundry.log`.

## "Failed to mount boot.wim." <a href="#mount-failed" id="mount-failed"></a>

**Where:** **Operation complete** dialog, followed by the output of DISM. Other forms: "Failed to commit mounted boot.wim changes." and "Failed to discard mounted boot.wim changes."

**Cause:** DISM could not mount, save or release the boot image, most often because another program uses the working folder.

**Fix:**

1. Close File Explorer windows, terminals and imaging tools that use `%ProgramData%\Foundry\Workspaces`.
2. Create the media again.
3. If the next attempt reports ["WinPE servicing is blocked..."](#servicing-blocked), follow that section.

**Collect:** `Foundry.log` and `dism.log`.

## "Failed to retrieve the WinPE driver catalog." <a href="#download-failed" id="download-failed"></a>

**Where:** **Operation complete** dialog. Other forms: "Failed to parse the WinPE driver catalog.", "Driver package download failed.", "Failed to download driver package.", "Failed to download the operating system catalog.", "Failed to acquire a verified Windows source package.", "Failed to prepare Foundry runtime payloads." and "Failed to provision Foundry runtime payloads."

**Cause:** The workstation cannot reach a host that this build needs:

- The catalogs on `raw.githubusercontent.com`, needed only when the media uses **Dell** or **HP** drivers, Wi-Fi or `arm64`. A build can therefore fail the day after you turn on Wi-Fi.
- The download servers of Dell, HP or Microsoft.
- GitHub, for the Foundry applications.

**Fix:**

1. Open **Settings** > **Proxy**, set the proxy of your network and select **Test connection**, then **Apply**.
2. Allow the hosts of the workstation listed in [Network endpoints](../reference/network-endpoints.md).
3. Create the media again. Files already downloaded are reused.

**Collect:** `Foundry.log`.

## "The transfer timed out. Check your connection and try again." <a href="#timed-out" id="timed-out"></a>

**Where:** **Operation complete** dialog.

**Cause:** A download received no data for two minutes. There is no limit on the total duration while data keeps arriving.

**Fix:**

1. Check the connection and the proxy of the workstation, and its free disk space.
2. Create the media again.

**Collect:** `Foundry.log`.

## "No Windows 11 24H2 Windows source matched the requested architecture and language." <a href="#windows-source" id="windows-source"></a>

**Where:** **Operation complete** dialog, only for media that uses Wi-Fi or `arm64`. Another form is "Failed to prepare boot image dependencies from every matching operating system source.", followed by lines such as "The cached Windows source package failed hash validation." or "The selected operating system image does not contain winre.wim."

**Cause:** These builds take files from a Windows 11 package of several GB, chosen in the [catalog](../reference/catalog.md) for the **Architecture** and the **WinPE boot language** of **General**.

- First message: no package exists for that language.
- Second form: the downloaded package could not be used, for example because it is damaged.

**Fix:**

1. For the first message, choose in **General** a **WinPE boot language** that Windows 11 is published in.
2. For the second form, close Foundry OSD, delete `%ProgramData%\Foundry\Cache\WindowsSources`, and create the media again.

**Collect:** `Foundry.log`.

## "Failed to extract driver package with bundled 7-Zip." <a href="#driver-failed" id="driver-failed"></a>

**Where:** **Operation complete** dialog. Other forms: "Executable driver package was extracted with 7-Zip but no INF files were found.", "Unsupported driver package format." and "Failed to inject driver package into the mounted image."

**Cause:**

- A downloaded **Dell** or **HP** driver package is damaged.
- A driver of your **Custom driver folder** is refused by Windows PE.

**Fix:**

1. Close Foundry OSD, delete `%ProgramData%\Foundry\Cache\WinPeDrivers`, and create the media again.
2. If the failure concerns the custom folder, remove the driver named in `dism.log` from it.

**Collect:** `Foundry.log` and `dism.log`.

## "Custom drivers total at least \<size\>; the maximum is \<size\>. Select only the network and storage drivers needed by Windows PE." <a href="#drivers-too-large" id="drivers-too-large"></a>

**Where:** **Operation complete** dialog. A second form is "Custom driver snapshots exceed the entry limit or contain a reparse point."

**Cause:** The **Custom driver folder** of **General**, subfolders included, exceeds 2 GiB or 10,000 files and folders, or contains a junction or a symbolic link. The size in the message is what was counted when the check stopped; the folder can be larger.

**Fix:** Point **Custom driver folder** to a plain folder that holds only the network and storage drivers Windows PE needs.

**Collect:** Nothing.

## "Foundry could not replace the ISO file at \<path\>. The file may be mounted, attached to a virtual machine, open in another program, or read-only. Unmount or close it, or choose another output path, then try again." <a href="#iso-locked" id="iso-locked"></a>

**Where:** **Operation complete** dialog, at the end of an ISO creation.

**Cause:** The new ISO was built, but the previous file of the same name could not be replaced after three attempts. The previous file is intact.

**Fix:**

1. Eject the ISO in File Explorer, or detach it from the virtual machine that uses it.
2. Or type another path in **ISO output**.
3. Select **Create ISO** again.

**Collect:** Nothing.

## "Unexpected failure while creating WinPE ISO media." <a href="#iso-unexpected" id="iso-unexpected"></a>

**Where:** **Operation complete** dialog, followed by a technical error text. Read its first line.

**Cause:**

- "Insufficient free space for custom-image ISO staging and atomic output publication.": the working drive or the output drive lacks room for the ISO. The wording mentions custom images even when you use none.
- "The custom-image ISO exceeds the FAT32 output file-size limit.": the ISO is larger than 4 GiB and the output drive is FAT32.
- "The ADK Oscdimg tool is required to create media containing custom images.": a tool of the ADK is missing.

**Fix:**

1. Free space in `%ProgramData%\Foundry\Workspaces` and in the output folder, or choose another output folder.
2. Save the ISO on an NTFS drive or a network share.
3. For the third text, repair or reinstall the ADK from the [ADK](../foundry-osd/adk.md) page.

**Collect:** The full text of the dialog and `Foundry.log`.

## The USB drive is not listed <a href="#usb-not-listed" id="usb-not-listed"></a>

**Where:** **Start**, **USB target** card. Under the title, the card reads "No USB drives were found.", "USB targets can be refreshed after ADK readiness is complete.", "USB target refresh failed." or "A required PowerShell USB query command failed."

**Cause:**

- The list was not refreshed after the drive was connected.
- The drive is not connected through USB, is the system disk, or is reported by Windows as not removable, as some USB hard disks and SSD enclosures are.
- The ADK is not ready, or the Windows storage commands failed.

**Fix:**

1. Select **Refresh**.
2. Use a removable USB flash drive, on another port if needed.
3. If the card names the ADK, open the [ADK](../foundry-osd/adk.md) page.

**Collect:** `Foundry.log` when the card reports a failure.

## "The USB drive identity is missing, ambiguous or has changed. Refresh the USB drive list and select the drive again." <a href="#usb-identity" id="usb-identity"></a>

**Where:** Dialog **USB creation is blocked**, or **Operation complete** dialog. A related form is "The selected USB drive is no longer safe to modify. Refresh the USB drive list and select another USB drive."

**Cause:** Foundry OSD checks the drive again before it writes, and stops when it is not sure to hold the drive you selected:

- The drive was unplugged, swapped or reconnected, so its disk number, name, serial number or size changed.
- Two identical drives without a serial number are connected.
- For the second message: the disk is no longer a removable USB disk of 16 GB or more, or became a system disk.

Nothing has been erased.

**Fix:**

1. Disconnect other USB drives of the same model.
2. Select **Refresh**, select the drive again, and start again.
3. Keep the drive connected until the end.

**Collect:** `Foundry.log`.

## "The USB BOOT partition needs approximately \<size\>, but its capacity is \<size\>. Nothing has been erased or formatted. Reduce customizations or drivers, or create an ISO." <a href="#boot-capacity" id="boot-capacity"></a>

**Where:** **Operation complete** dialog. Other forms: "The USB BOOT partition capacity could not be verified. Nothing has been erased or formatted. Check and reconnect the USB drive, then retry. Also check that the source files are accessible." and "A boot media file is \<size\>; FAT32 supports at most \<size\> per file. Reduce the boot image size or create an ISO."

**Cause:** The boot files must fit the **BOOT** partition, which is 2 GiB whatever the size of the drive, and no file can exceed 4 GiB. Drivers added to Windows PE are the usual reason.

**Fix:**

1. Turn off the driver sets you do not need in **General** > **Driver options**, and reduce the **Custom driver folder**.
2. Or [create an ISO](../foundry-osd/media/create-iso.md), which has no such limit.
3. For the "could not be verified" form, reconnect the drive, select **Refresh** and start again.

**Collect:** Nothing.

## "Failed to partition and format the USB disk." <a href="#usb-write" id="usb-write"></a>

**Where:** **Operation complete** dialog, followed by the output of the Windows storage commands, for example "Timed out waiting for BOOT volume X: to become available." or "BOOT partition was created without a drive letter." Other forms: "Failed to format the USB BOOT partition.", "Failed to copy WinPE media files to USB BOOT partition." "USB verification failed: boot.wim not found.", "USB verification failed: BCD not found." and "USB verification failed: EFI boot file not found."

**Cause:**

- The drive is write-protected by a switch, or is failing.
- A program uses the drive: a File Explorer window, an antivirus scan.
- Windows has no free drive letter to assign.

**Fix:**

1. Close the windows and programs that use the drive, and check its write-protection switch.
2. Free a drive letter if all are in use.
3. Start again. A drive that was being created is created again; a drive that was being updated is [updated](../foundry-osd/media/update-usb.md) again.
4. If it fails again, use another USB port, then another drive.

**Collect:** The full text of the dialog and `Foundry.log`.

## "Selected USB media is not a Foundry USB media." <a href="#usb-update" id="usb-update"></a>

**Where:** **Operation complete** dialog, during **Update USB**. A related form is "USB provisioning did not return assigned drive letters."

**Cause:**

- First message: the **BOOT** volume was renamed or reformatted, so the drive no longer has the layout Foundry OSD expects.
- Second message: the **Foundry Cache** volume has no drive letter in Windows.

**Fix:**

1. For the second message, assign a drive letter to **Foundry Cache** in Windows Disk Management, select **Refresh**, and update again.
2. For the first, delete the partitions of the drive in Windows Disk Management and [create the drive](../foundry-osd/media/create-usb.md) again. Its downloads are lost.

**Collect:** The full text of the dialog.

## "Custom Windows image media preparation failed." <a href="#usb-content" id="usb-content"></a>

**Where:** **Operation complete** dialog, followed by a second line. The message is used for custom images and for post-installation content alike.

**Cause:**

- "The USB disk has insufficient capacity for BOOT, custom images and runtime payloads.": the drive is too small for the custom images and the post-installation content you included.
- "A media input is on the target disk or its physical disk could not be verified.": a custom image or a post-installation file is stored on the USB drive being prepared.

**Fix:**

1. Use a larger drive, or include fewer [custom images](../foundry-osd/customization/custom-windows-images.md).
2. Keep the source files on the workstation, not on the USB drive.

**Collect:** The full text of the dialog and `Foundry.log`.

## The device does not start from the media <a href="#does-not-boot" id="does-not-boot"></a>

**Where:** Target device. The creation succeeded, but the device ignores the media, shows a Secure Boot error, or returns to its boot menu. Foundry shows nothing at this stage.

**Cause:**

- The **Architecture** of the media does not match the device: `x64` media on an ARM64 device, or the reverse.
- The device has Secure Boot turned on and its firmware does not yet trust the **PCA 2023** certificate that signs the boot files.
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

**Cause:** A PXE server delivers only `sources\boot.wim`. The deployment has a [Post-installation](../foundry-osd/customization/post-installation.md) action that uses imported files, and those files stayed on the ISO.

**Fix:**

1. Attach the complete ISO the boot image was copied from, and start the deployment again.
2. Or, in Foundry OSD, disable the actions that use imported files, create the ISO again and import its new `sources\boot.wim` in the PXE server.

See [Deploy with PXE](../foundry-osd/media/pxe-deployment.md) for what a PXE boot image delivers.

**Collect:** The Foundry Deploy log. See [Logs and support information](logs-and-support.md).

## Folders Foundry OSD uses on the workstation

<details>

<summary>Working folders and downloads</summary>

| Folder | Content | Can you delete it |
| --- | --- | --- |
| `%ProgramData%\Foundry\Workspaces` | One working folder per creation, removed at the end | No, Foundry OSD removes them |
| `%ProgramData%\Foundry\Cache\WinPeDrivers` | Downloaded Dell and HP driver sets | Yes, while Foundry OSD is closed |
| `%ProgramData%\Foundry\Cache\WindowsSources` | Windows 11 package for Wi-Fi and `arm64` builds | Yes, while Foundry OSD is closed |
| `%ProgramData%\Foundry\Cache\Installers` | Setup files of the ADK and the Windows PE add-on | Yes, while Foundry OSD is closed |
| `%ProgramData%\Foundry\Artifacts\Iso` | Default folder of the ISO file | Yes |

Deleted downloads are fetched again by the next creation that needs them.

</details>
