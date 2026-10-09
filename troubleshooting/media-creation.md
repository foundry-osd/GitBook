# Media creation troubleshooting

This page covers what **Start** shows in Foundry OSD: a button that cannot be selected, a dialog, or a creation that ends with "Final media creation failed." Two kinds of problems are described elsewhere:

| Where the problem shows | Go to |
| --- | --- |
| The USB drive: not listed, cannot be written or updated, or "Custom Windows image media preparation failed." A device that does not start from the media. A start from a PXE server | [USB drive and device start](media-creation/usb-drive-and-device-start.md) |
| The **ADK** page, the proxy, updates, or an option of **General** | [Foundry OSD application troubleshooting](foundry-osd.md) |

A failure on **Start** shows in a dialog titled **ISO creation is blocked** or **USB creation is blocked**, before anything is written, or in the **Operation complete** dialog, as "Final media creation failed." followed by the reason. Most reasons are in English whatever the language of Foundry OSD, and some are followed by the output of a Windows tool.

The log is `%ProgramData%\Foundry\Logs\Foundry.log`; its line "Final boot media operation failed" names the failed step. Windows image errors are also in `%SystemRoot%\Logs\DISM\dism.log`. See [Log locations](logs-and-support.md#log-locations).

Otherwise, find the message below.

## On this page <a href="#on-this-page" id="on-this-page"></a>

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

## The button stays unavailable <a href="#button-unavailable" id="button-unavailable"></a>

**Where:** **Start**, **Create media** card. No message is shown.

**Cause:**

- A row is marked **Needs attention**; the bar at the top reads **Readiness items needing action:** with a number.
- For **Create ISO**: the **ISO output** path is empty or does not end with `.iso`.
- For **Create USB**: no drive is selected, or the drive is smaller than 16 GB.

**Fix:**

1. In each group, select the **Review** button of every row marked **Needs attention** and complete that page.
2. Type a path that ends with `.iso`, or select **Browse**.
3. Select a USB drive of 16 GB or more. If the drive is not in the list, see [The USB drive is not listed](media-creation/usb-drive-and-device-start.md#usb-not-listed).

If something changes between your click and the start, a dialog titled **ISO creation is blocked** or **USB creation is blocked** lists the same reasons, for example "ISO output path must end with .iso." or "Deploy configuration generation is not ready.". For the latter with Windows Autopilot and local accounts, see [Media creation is blocked by additional local accounts](autopilot.md#local-accounts).

**Collect:** Nothing.

## "Update Foundry OSD before creating boot media" <a href="#update-advisory" id="update-advisory"></a>

**Where:** Dialog shown when you select **Create ISO**, **Create USB** or **Update USB**.

**Cause:** A newer Foundry OSD is available. This is advice, not a failure.

**Fix:** Select **Apply update**, or **View update** while the update is not ready, then create the media with the new version. **Create anyway** uses the current version.

**Collect:** Nothing.

## "Not enough disk space to create the boot image." <a href="#not-enough-disk-space" id="not-enough-disk-space"></a>

**Where:** Dialog **ISO creation is blocked** or **USB creation is blocked**. The full text is "Not enough disk space to create the boot image. At least 20 GB of free space is required on \<volume\>. Available: \<size\>. Free up space and try again." A second form is "The available disk space for boot image creation could not be verified on \<volume\>. Check that the location is accessible and try again."

**Cause:** Before every creation, Foundry OSD requires 20 GB free on each drive that holds `%ProgramData%\Foundry`, `%LocalAppData%\Foundry`, `%TEMP%` or `%SystemRoot%\Temp`, and on the drive of the ISO file.

**Fix:** Free space on the volume named in the message. For an ISO, you can also choose another output folder, or check that the network share answers.

**Collect:** `Foundry.log`, line "Boot media creation was blocked by local storage validation".

## "WinPE servicing is blocked because an earlier image cleanup has no confirmed completion." <a href="#servicing-blocked" id="servicing-blocked"></a>

**Where:** **Operation complete** dialog, at the start of a creation, followed by "Retained operation: '\<folder\>'. Cleanup marker: '\<file\>'. Preserve this workspace until cleanup can be verified." and a numbered procedure. A second form is "WinPE servicing is blocked because retained operation cleanup could not be safely inspected."

**Cause:** During an earlier creation, Windows did not confirm that the boot image was unmounted: the DISM command exceeded 15 minutes, or Foundry OSD was closed while it ran. Foundry OSD keeps that working folder and services no other image until the old one is released.

Each creation first tries to recover: the block is lifted if Windows no longer reports the image as mounted; otherwise Foundry OSD discards the image once, which can take 15 minutes. You see the message only when both fail. The same recovery runs at each new creation, so one more attempt can succeed; allow up to 15 minutes for it.

**Fix:** The dialog gives the procedure with the real path. If another Foundry media operation is still running, let it finish and start the operation again. Otherwise, recover manually:

1. Restart Windows.
2. From an elevated command prompt, run: `dism /Unmount-Image /MountDir:"<mount path>" /Discard`
3. Run: `dism /Cleanup-Mountpoints`
4. Start the operation again.

When the dialog says that the cleanup marker does not name a mount directory, it shows other steps: after the restart, run `dism /Get-MountedImageInfo`, discard each mount directory listed inside the retained operation, run `dism /Cleanup-Mountpoints`, delete the cleanup marker file, then start again.

Do not delete the retained folder yourself.

For the second form, Foundry OSD could not read the working folders: check that your account can read `%ProgramData%\Foundry\Workspaces`, then try again.

**Collect:** The full text of the dialog, `Foundry.log` and `dism.log`.

## "Windows blocked access to the WinPE image files." <a href="#access-blocked" id="access-blocked"></a>

**Where:** **Operation complete** dialog. The full text is "Windows blocked access to the WinPE image files. Security software or another tool may be locking them. Add a security software exclusion for \<folder\>, close tools that mount images, restart the computer, and try again. Details are in \<log\> and \<log\>." Related forms, followed by the output of DISM: "Failed to mount boot.wim.", "Failed to commit mounted boot.wim changes." and "Failed to discard mounted boot.wim changes."

**Cause:**

- Antivirus or endpoint protection software scans or locks files under `%ProgramData%\Foundry`.
- Another program has the working folder open or an image mounted.

**Fix:**

1. Add an exclusion for `%ProgramData%\Foundry` in your security software.
2. Close imaging tools, terminals and File Explorer windows opened in that folder.
3. Restart the workstation and create the media again. If it then reports "WinPE servicing is blocked...", see [the previous entry](#servicing-blocked).

**Collect:** `Foundry.log` and `dism.log`.

## "Failed to create WinPE workspace using copype.cmd." <a href="#adk-component" id="adk-component"></a>

**Where:** **Operation complete** dialog. This entry covers these messages:

| Message | What it means |
| --- | --- |
| "Failed to create WinPE workspace using copype.cmd." | The ADK tool that builds the Windows PE working folder failed |
| "WinPE workspace was created but boot.wim was not found." | The working folder was built without a boot image |
| "The selected WinPE language pack was not found." | The language pack of the **WinPE boot language** is not in the Windows PE add-on |
| "The WinPE optional components folder was not found." | The optional components of the Windows PE add-on are missing |
| "The required '\<name\>' WinPE optional component was not found." | One component that Foundry adds to the boot image is missing |
| "OA3Tool executable was not found for the selected WinPE architecture." | Zero-touch Windows Autopilot only: see [the entry of Windows Autopilot troubleshooting](autopilot.md#oa3tool-not-found) |
| "PCA2023 requires /bootex support in the WinPE workspace." | The **Secure Boot** switch of **General** is on, which reads **PCA 2023**, and the installed ADK cannot produce media signed that way |
| "PCA2023 USB creation requires BootEx EFI binaries in the WinPE workspace." | The same, for a USB drive |

**Cause:** Except for the two PCA2023 messages, a file of the Windows ADK or of the Windows PE add-on is missing or damaged for the architecture selected in **General**.

**Fix:**

1. Open the [ADK](../foundry-osd/adk.md) page and repair or reinstall what it reports. The accepted version is in [Windows ADK](../reference/supported-versions.md#windows-adk).
2. For the PCA2023 messages, you can instead turn off the **Secure Boot** switch of [General](../foundry-osd/general.md), which then reads **PCA 2011**.

**Collect:** `Foundry.log`, and the text shown after the message.

## "Failed to retrieve the WinPE driver catalog." <a href="#download-failed" id="download-failed"></a>

**Where:** **Operation complete** dialog. This entry covers these messages:

| Message | What could not be obtained |
| --- | --- |
| "Failed to retrieve the WinPE driver catalog." | The catalog of Windows PE drivers |
| "Failed to parse the WinPE driver catalog." | The same catalog: what was received cannot be read |
| "Driver package download failed." or "Failed to download driver package." | A **Dell** or **HP** driver set, or the Intel Wi-Fi driver |
| "Failed to download the operating system catalog." | The catalog of Windows images, used for media with Wi-Fi or `arm64` |
| "Failed to acquire a verified Windows source package." | The Windows 11 package that such media is built from |
| "Failed to prepare Foundry runtime payloads." or "Failed to provision Foundry runtime payloads." | The Foundry applications that go on the media |
| "The transfer timed out. Check your connection and try again." | Any of these downloads: no data arrived for two minutes |

**Cause:** The workstation cannot reach a host this build needs.

- The [catalogs](../reference/catalog.md) on `raw.githubusercontent.com` are needed only with **Dell** or **HP** drivers, Wi-Fi or `arm64`. A build can therefore fail the day after you turn on Wi-Fi.
- The other hosts are GitHub and the download servers of Dell, HP and Microsoft.

**Fix:**

1. Open **Settings** > **Proxy**, set the proxy of your network, select **Test connection**, then **Apply**.
2. Allow the workstation hosts listed in [Network endpoints](../reference/network-endpoints.md).
3. Create the media again. Files already downloaded are reused.

**Collect:** `Foundry.log`.

## "Failed to prepare boot image dependencies from every matching operating system source." <a href="#package-refused" id="package-refused"></a>

**Where:** **Operation complete** dialog. Media with Wi-Fi or `arm64` takes files from a Windows 11 package of several GB, chosen for the **Architecture** and the **WinPE boot language** of **General**.

**Cause and fix**, by message:

| Message | Cause | Fix |
| --- | --- | --- |
| "Failed to prepare boot image dependencies from every matching operating system source." | No Windows 11 package could be prepared; the lines that follow name the reason | Follow the row of the reason |
| "No Windows 11 24H2 Windows source matched the requested architecture and language." | No package exists for that architecture and language | Choose a **WinPE boot language** in which Windows 11 is published |
| "The cached Windows source package failed hash validation." | The downloaded package is damaged | Close Foundry OSD, delete `%ProgramData%\Foundry\Cache\WindowsSources`, create the media again |
| "Failed to extract driver package with bundled 7-Zip.", "Executable driver package was extracted with 7-Zip but no INF files were found." or "Unsupported driver package format." | A downloaded **Dell** or **HP** driver package is damaged or cannot be used | Close Foundry OSD, delete `%ProgramData%\Foundry\Cache\WinPeDrivers`, create the media again |
| "Failed to inject driver package into the mounted image." | Windows PE refused a driver, downloaded or from your **Custom driver folder** | Remove the driver named in `dism.log` from the folder, or delete the driver cache as in the row above |

**Collect:** `Foundry.log` and `dism.log`.

## "Custom drivers total at least \<size\>; the maximum is \<size\>." <a href="#too-large" id="too-large"></a>

**Where:** **Operation complete** dialog.

**Cause and fix**, by message:

| Message | Cause | Fix |
| --- | --- | --- |
| "Custom drivers total at least \<size\>; the maximum is \<size\>. Select only the network and storage drivers needed by Windows PE." | The **Custom driver folder**, subfolders included, exceeds 2 GiB. The size shown is what was counted when the check stopped | Point **Custom driver folder** to a folder with only the network and storage drivers Windows PE needs |
| "Custom driver snapshots exceed the entry limit or contain a reparse point." or "Custom driver snapshots do not follow reparse points." | The folder holds more than 10,000 files and folders, or contains a junction or a symbolic link | Use a plain folder without links |
| "The USB BOOT partition needs approximately \<size\>, but its capacity is \<size\>. Nothing has been erased or formatted. Reduce customizations or drivers, or create an ISO." | The boot files do not fit the **BOOT** partition, which is 2 GiB whatever the size of the drive | Reduce the custom drivers, turn off the **Driver options** you do not need, or [create an ISO](../foundry-osd/media/create-iso.md) |
| "A boot media file is \<size\>; FAT32 supports at most \<size\> per file. Reduce the boot image size or create an ISO." | One file of the media is larger than the FAT32 **BOOT** partition accepts | The same |
| "The USB BOOT partition capacity could not be verified. Nothing has been erased or formatted. Check and reconnect the USB drive, then retry. Also check that the source files are accessible." | Foundry OSD could not measure the partition or the files to copy | Reconnect the drive, select **Refresh** and start again |

**Collect:** Nothing.

## "Foundry could not replace the ISO file at \<path\>." <a href="#iso-failed" id="iso-failed"></a>

**Where:** **Operation complete** dialog, at the end of an ISO creation. The last three messages of the table follow "Unexpected failure while creating WinPE ISO media.": read the first line of the technical text under it.

**Cause and fix**, by message:

| Message | Cause | Fix |
| --- | --- | --- |
| "Foundry could not replace the ISO file at \<path\>. The file may be mounted, attached to a virtual machine, open in another program, or read-only. Unmount or close it, or choose another output path, then try again." | The previous ISO of the same name is in use and could not be replaced. It is intact | Eject the ISO in File Explorer or detach it from the virtual machine, or type another path in **ISO output** |
| "Insufficient free space for custom-image ISO staging and atomic output publication." | The working drive or the output drive lacks room. The wording mentions custom images even when you use none | Free space in `%ProgramData%\Foundry\Workspaces` and in the output folder |
| "The custom-image ISO exceeds the FAT32 output file-size limit." | The ISO exceeds 4 GiB and the output drive is FAT32 | Save the ISO on an NTFS drive or a network share |
| "The ADK Oscdimg tool is required to create media containing custom images." | A tool of the ADK is missing | Repair the ADK from the [ADK](../foundry-osd/adk.md) page |

**Collect:** The full text of the dialog and `Foundry.log`.
