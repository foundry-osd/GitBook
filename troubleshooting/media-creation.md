# Media creation troubleshooting

This page covers what **Start** shows in Foundry OSD: a button that cannot be selected, a dialog, or a creation that ends in a dialog titled **ISO creation failed**, **USB media creation failed** or **USB media update failed**. Two kinds of problems are described elsewhere:

| Where the problem shows | Go to |
| --- | --- |
| The USB drive: not listed, cannot be written or updated, or "Custom Windows image media preparation failed." A device that does not start from the media. A start from a PXE server | [USB drive and device start](media-creation/usb-drive-and-device-start.md) |
| The **ADK** page, the proxy, updates, or an option of **General** | [Foundry OSD application troubleshooting](foundry-osd.md) |

A failure on **Start** shows in a dialog titled **ISO creation is blocked** or **USB creation is blocked**, before anything is written, or in the failure dialog that ends the creation, titled **ISO creation failed**, **USB media creation failed** or **USB media update failed**. The failure dialog gives the reason, then the step that failed, such as "It failed during step 3 of 5: Customizing the boot image.", then "Details are in the log.", with an **Open log folder** button. Reasons that Foundry OSD recognizes are in the language of Foundry OSD. Any other reason follows "An unexpected error stopped media creation." and appears in English after "Technical details:", sometimes followed by the output of a Windows tool.

The log is `%ProgramData%\Foundry\Logs\Foundry.log`; its line "Final boot media operation failed" names the failed step. Windows image errors are also in `%SystemRoot%\Logs\DISM\dism.log`. See [Log locations](logs-and-support.md#log-locations).

Otherwise, find the message below.

## On this page <a href="#on-this-page" id="on-this-page"></a>

| What you see | Go to section |
| --- | --- |
| **Create ISO** or **Create USB** cannot be selected, or a dialog lists reasons | [The button stays unavailable](#button-unavailable) |
| A dialog offering **Create anyway** | [Update Foundry OSD](#update-advisory) |
| "Not enough disk space to create the boot image..." | [Not enough disk space](#not-enough-disk-space) |
| "The selected WinPE keyboard layout is not installed on this computer.", "...is not available in the boot image." | [The keyboard layout is missing](#keyboard-layout-missing) |
| "WinPE servicing is blocked..." | [Image cleanup not confirmed](#servicing-blocked) |
| "Windows blocked access to the WinPE image files...", "Failed to mount boot.wim." | [Image files are locked](#access-blocked) |
| "Failed to create WinPE workspace using copype.cmd.", a component "was not found", "PCA2023 requires..." | [The ADK lacks a component](#adk-component) |
| "Failed to retrieve...", "Failed to download...", "The transfer timed out..." | [A download fails](#download-failed) |
| A driver package that is refused | [A downloaded package is refused](#package-refused) |
| "ARM64 boot media requires the Windows Recovery Environment...", or a result message saying the media uses the standard Windows PE image | [Windows RE is unavailable](#winre-unavailable) |
| "Custom drivers total at least...", "The USB BOOT partition needs approximately..." | [Drivers or boot files are too large](#too-large) |
| "Foundry could not replace the ISO file...", "Unexpected failure while creating WinPE ISO media." | [The ISO cannot be written](#iso-failed) |

## The button stays unavailable <a href="#button-unavailable" id="button-unavailable"></a>

**Where:** **Start**, **Create media** card. No message is shown.

**Cause:**

- A row is marked **Needs attention**; the bar at the top reads **Readiness items needing action:** with a number. For **Custom images**, the row can read "One or more included custom images are not built for the selected media architecture. Exclude them or change the media architecture.": see [Custom Windows images](../foundry-osd/customization/custom-windows-images.md#choose-what-goes-on-the-media).
- On a PC that [follows a folder](../foundry-osd/configuration-sharing/follow-a-folder.md#new-versions), shared content is not installed yet. The bar reads "Shared content is still being synchronized. Media creation is available once it is complete." and every media action is unavailable until the synchronization completes. When it failed or stalled, see [Shared content missing](foundry-osd/configuration-sharing.md#content-failed).
- For **Create ISO**: the **ISO output** path is empty or does not end with `.iso`.
- For **Create USB**: no drive is selected, or the drive is smaller than 16 GB. A drive sold as 16 GB is accepted, although the list shows it at about 14.5 GB.

**Fix:**

1. In each group, select the **Review** button of every row marked **Needs attention** and complete that page.
2. Type a path that ends with `.iso`, or select **Browse**.
3. Select a USB drive sold as 16 GB or more. If the drive is not in the list, see [The USB drive is not listed](media-creation/usb-drive-and-device-start.md#usb-not-listed).

If something changes between your click and the start, a dialog titled **ISO creation is blocked** or **USB creation is blocked** gives the reason, or "Resolve the following before creating media:" followed by a list of reasons, for example "ISO output path must end with .iso." or "Deploy configuration generation is not ready.". For the latter with Windows Autopilot and local accounts, see [Media creation is blocked by additional local accounts](autopilot.md#local-accounts).

**Collect:** Nothing.

## "Update Foundry OSD before creating boot media" <a href="#update-advisory" id="update-advisory"></a>

**Where:** Dialog shown when you select **Create ISO**, **Create USB** or **Update USB**.

**Cause:** A newer Foundry OSD is available. This is advice, not a failure.

**Fix:** Select **Apply update**, or **View update** while the update is not ready, then create the media with the new version. **Create anyway** uses the current version.

**Collect:** Nothing.

## "The selected WinPE keyboard layout is not installed on this computer." <a href="#keyboard-layout-missing" id="keyboard-layout-missing"></a>

**Where:** **Start**, on the **WinPE boot language** row marked **Needs attention**, in the **ISO creation is blocked** or **USB creation is blocked** dialog, and under the **WinPE keyboard layout** card of **General**. A second form, "The selected WinPE keyboard layout is not available in the boot image.", appears in the **Operation complete** dialog with the layout and its file.

**Cause:** The first form: the layout was chosen on another computer, then the configuration was imported or followed here, and this computer does not have it. The second form: the Windows PE image for this architecture does not contain the file of that layout.

**Fix:** On **General**, in the **WinPE keyboard layout** card, choose a layout available here, or **Default**. For the second form, choose another layout of the same language, or **Default**.

**Collect:** `Foundry.log`, line "Media preflight summary refreshed", which names the layout.

## "Not enough disk space to create the boot image." <a href="#not-enough-disk-space" id="not-enough-disk-space"></a>

**Where:** Dialog **ISO creation is blocked** or **USB creation is blocked**. The full text is "Not enough disk space to create the boot image. At least 20 GB of free space is required on \<volume\>. Available: \<size\>. Free up space and try again." A second form is "The available disk space for boot image creation could not be verified on \<volume\>. Check that the location is accessible and try again."

**Cause:** Before every creation, Foundry OSD requires 20 GB free on each drive that holds `%ProgramData%\Foundry`, `%LocalAppData%\Foundry`, `%TEMP%` or `%SystemRoot%\Temp`, and on the drive of the ISO file.

**Fix:** Free space on the volume named in the message. For an ISO, you can also choose another output folder, or check that the network share answers.

**Collect:** `Foundry.log`, line "Boot media creation was blocked by local storage validation".

## "WinPE servicing is blocked because an earlier image cleanup has no confirmed completion." <a href="#servicing-blocked" id="servicing-blocked"></a>

**Where:** Failure dialog, at the start of a creation, after "Technical details:", followed by "Retained operation: '\<folder\>'. Cleanup marker: '\<file\>'. Preserve this workspace until cleanup can be verified." and a numbered procedure. A second form is "WinPE servicing is blocked because retained operation cleanup could not be safely inspected."

**Cause:** During an earlier creation, Windows did not confirm that the boot image was unmounted: the DISM command exceeded 15 minutes, or Foundry OSD was closed while it ran. Foundry OSD keeps that working folder and services no other image until the old one is released.

Each creation first tries to recover: the block is lifted if Windows no longer reports the image as mounted; otherwise Foundry OSD discards the image once, which can take 15 minutes. During that time the dialog stays on step 1, "Preparing the workspace", and shows "Cleaning up files from a previous run…" or "Discarding an image left mounted by a previous run…" with a percentage. You see the message only when both fail. The same recovery runs at each new creation, so one more attempt can succeed; allow up to 15 minutes for it.

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

**Where:** Failure dialog. The full text is "Windows blocked access to the WinPE image files. Security software or another tool may be locking them. Add a security software exclusion for \<folder\>, close tools that mount images, restart the computer, and try again. Details are in \<log\> and \<log\>." Related forms appear after "Technical details:", followed by the output of DISM: "Failed to mount boot.wim.", "Failed to commit mounted boot.wim changes." and "Failed to discard mounted boot.wim changes."

**Cause:**

- Antivirus or endpoint protection software scans or locks files under `%ProgramData%\Foundry`.
- Another program has the working folder open or an image mounted.

**Fix:**

1. Add an exclusion for `%ProgramData%\Foundry` in your security software.
2. Close imaging tools, terminals and File Explorer windows opened in that folder.
3. Restart the workstation and create the media again. If it then reports "WinPE servicing is blocked...", see [the previous entry](#servicing-blocked).

**Collect:** `Foundry.log` and `dism.log`.

## "Failed to create WinPE workspace using copype.cmd." <a href="#adk-component" id="adk-component"></a>

**Where:** Failure dialog, after "Technical details:". This entry covers these messages:

| Message | What it means |
| --- | --- |
| "Failed to create WinPE workspace using copype.cmd." | The ADK tool that builds the Windows PE working folder failed |
| "Failed to create WinPE workspace using copype.cmd.", then "DISM mount replay:" | The ADK tool could not mount the boot image. Foundry OSD ran the mount again with DISM and shows the DISM result after that line |
| "WinPE workspace was created but boot.wim was not found." | The working folder was built without a boot image |
| "The selected WinPE language pack was not found." | The language pack of the **WinPE boot language** is not in the Windows PE add-on |
| "The WinPE optional components folder was not found." | The optional components of the Windows PE add-on are missing |
| "The required '\<name\>' WinPE optional component was not found." | One component that Foundry adds to the boot image is missing |
| "OA3Tool executable was not found for the selected WinPE architecture." | Zero-touch Windows Autopilot only: see [the entry of Windows Autopilot troubleshooting](autopilot.md#oa3tool-not-found) |
| "PCA2023 requires /bootex support in the WinPE workspace." | The **Secure Boot** switch of **General** is on, which reads **PCA 2023**, and the installed ADK cannot produce media signed that way |
| "PCA2023 USB creation requires BootEx EFI binaries in the WinPE workspace." | The same, for a USB drive |

**Cause:** Except for the two PCA2023 messages and the mount failure, a file of the Windows ADK or of the Windows PE add-on is missing or damaged for the architecture selected in **General**.

**Fix:**

1. Open the [ADK](../foundry-osd/adk.md) page and repair or reinstall what it reports. The accepted version is in [Windows ADK](../reference/supported-versions.md#windows-adk).
2. For the PCA2023 messages, you can instead turn off the **Secure Boot** switch of [General](../foundry-osd/general.md), which then reads **PCA 2011**.
3. When the text contains "DISM mount replay:", the image mount is blocked on this workstation, usually by security software or the Windows image mount driver. Follow [Image files are locked](#access-blocked). If the text says the failure "did not reproduce", create the media again.

**Collect:** `Foundry.log`, and the text shown after the message. For a mount failure, also `dism.log` and `%SystemRoot%\Logs\DISM\WimMountAdkSetup.log`.

## "Failed to retrieve the WinPE driver catalog." <a href="#download-failed" id="download-failed"></a>

**Where:** Failure dialog. Except for "The transfer timed out...", the messages appear after "Technical details:". This entry covers these messages:

| Message | What could not be obtained |
| --- | --- |
| "Failed to retrieve the WinPE driver catalog." | The catalog of Windows PE drivers |
| "Failed to parse the WinPE driver catalog." | The same catalog: what was received cannot be read |
| "Driver package download failed." or "Failed to download driver package." | A **Dell** or **HP** driver set, or the Intel Wi-Fi driver |
| "Failed to prepare Foundry runtime payloads." or "Failed to provision Foundry runtime payloads." | The Foundry applications that go on the media |
| "The transfer timed out. Check your connection and try again." | Any of these downloads: no data arrived for two minutes |

**Cause:** The workstation cannot reach a host this build needs.

- The [catalog](../reference/catalog.md) of Windows PE drivers on `raw.githubusercontent.com` is needed only with **Dell** or **HP** drivers, or Wi-Fi. The catalog of Windows images and the Windows 11 package are covered in [Windows RE is unavailable](#winre-unavailable).
- The other hosts are GitHub and the download servers of Dell, HP and Microsoft.

**Fix:**

1. Open **Settings** > **Proxy**, set the proxy of your network, select **Test connection**, then **Apply**.
2. Allow the workstation hosts listed in [Network endpoints](../reference/network-endpoints.md).
3. Create the media again. Files already downloaded are reused.

**Collect:** `Foundry.log`.

## "ARM64 boot media requires the Windows Recovery Environment..." <a href="#winre-unavailable" id="winre-unavailable"></a>

**Where:** With `arm64`, failure dialog, after "Technical details:". With `x64`, the media is created and its result message ends with "Foundry OSD could not get the Windows Recovery Environment, so this media uses the standard Windows PE image. Wi-Fi is not available on it..."

**Cause:** Foundry OSD builds every boot image from the Windows RE of the newest Windows 11 package: see [What decides the boot image](../foundry-osd/media/create-iso.md#what-decides-the-boot-image). It could not get that package and had no Windows RE kept from an earlier build for the **Architecture** and the **WinPE boot language** of **General**. An `x64` media then uses the standard Windows PE of the ADK, without Wi-Fi; an `arm64` media cannot. The lines that follow the first message name the reason:

| Message | Cause | Fix |
| --- | --- | --- |
| "Failed to download the operating system catalog." | The catalog of Windows images on `raw.githubusercontent.com` cannot be reached | Follow [A download fails](#download-failed) |
| "Failed to acquire a verified Windows source package." | The Windows 11 package could not be downloaded from `dl.delivery.mp.microsoft.com`, or arrived damaged | Follow [A download fails](#download-failed). A damaged package is downloaded again on the next build |
| "No Windows 11 source of the 26100 servicing branch (24H2, 25H2 or 26H2) matched the requested architecture and language." | No package exists for that architecture and language | Choose a **WinPE boot language** in which Windows 11 is published |
| "Failed to extract WinRE from every matching operating system source." | The package was downloaded but its Windows RE could not be extracted; the lines that follow name the DISM or file error | Check the free space and `dism.log`, then create the media again |

**Fix:** remove the cause, then create the media again. An `x64` media created in the meantime works, but without Wi-Fi.

Foundry OSD checks the kept Windows RE before each use and extracts it again when it is damaged. To start over anyway, close Foundry OSD and delete `%ProgramData%\Foundry\Cache\WinRe`.

**Collect:** `Foundry.log` and `dism.log`.

## "Failed to extract driver package with bundled 7-Zip." <a href="#package-refused" id="package-refused"></a>

**Where:** Failure dialog, after "Technical details:".

**Cause and fix**, by message:

| Message | Cause | Fix |
| --- | --- | --- |
| "Failed to extract driver package with bundled 7-Zip.", "Executable driver package was extracted with 7-Zip but no INF files were found." or "Unsupported driver package format." | A downloaded **Dell** or **HP** driver package is damaged or cannot be used | Close Foundry OSD, delete `%ProgramData%\Foundry\Cache\WinPeDrivers`, create the media again |
| "Failed to inject driver package into the mounted image." | Windows PE refused a driver, downloaded or from your **Additional WinPE driver folder** | Remove the driver named in `dism.log` from the folder, or delete the driver cache as in the row above |

**Collect:** `Foundry.log` and `dism.log`.

## "Custom drivers total at least \<size\>; the maximum is \<size\>." <a href="#too-large" id="too-large"></a>

**Where:** Failure dialog. The two "Custom driver snapshots" messages appear after "Technical details:".

**Cause and fix**, by message:

| Message | Cause | Fix |
| --- | --- | --- |
| "Custom drivers total at least \<size\>; the maximum is \<size\>. Select only the network and storage drivers needed by Windows PE." | The **Additional WinPE driver folder**, subfolders included, exceeds 2 GiB. The size shown is what was counted when the check stopped | Point **Additional WinPE driver folder** to a folder with only the network and storage drivers Windows PE needs |
| "Custom driver snapshots exceed the entry limit or contain a reparse point." or "Custom driver snapshots do not follow reparse points." | The folder holds more than 10,000 files and folders, or contains a junction or a symbolic link | Use a plain folder without links |
| "The USB BOOT partition needs approximately \<size\>, but its capacity is \<size\>. Nothing has been erased or formatted. Reduce customizations or drivers, or create an ISO." | The boot files do not fit the **BOOT** partition, which is 2 GiB whatever the size of the drive | Reduce the custom drivers, turn off the **WinPE drivers** options you do not need, or [create an ISO](../foundry-osd/media/create-iso.md) |
| "A boot media file is \<size\>; FAT32 supports at most \<size\> per file. Reduce the boot image size or create an ISO." | One file of the media is larger than the FAT32 **BOOT** partition accepts | The same |
| "The USB BOOT partition capacity could not be verified. Nothing has been erased or formatted. Check and reconnect the USB drive, then retry. Also check that the source files are accessible." | Foundry OSD could not measure the partition or the files to copy | Reconnect the drive, select **Refresh** and start again |

**Collect:** Nothing.

## "Foundry could not replace the ISO file at \<path\>." <a href="#iso-failed" id="iso-failed"></a>

**Where:** Failure dialog, at the end of an ISO creation. The last three messages of the table follow "Technical details: Unexpected failure while creating WinPE ISO media.": read the first line of the technical text under it.

**Cause and fix**, by message:

| Message | Cause | Fix |
| --- | --- | --- |
| "Foundry could not replace the ISO file at \<path\>. The file may be mounted, attached to a virtual machine, open in another program, or read-only. Unmount or close it, or choose another output path, then try again." | The previous ISO of the same name is in use and could not be replaced. It is intact | Eject the ISO in File Explorer or detach it from the virtual machine, or type another path in **ISO output** |
| "Insufficient free space for custom-image ISO staging and atomic output publication." | The working drive or the output drive lacks room. The wording mentions custom images even when you use none | Free space in `%ProgramData%\Foundry\Workspaces` and in the output folder |
| "The custom-image ISO exceeds the FAT32 output file-size limit." | The ISO exceeds 4 GiB and the output drive is FAT32 | Save the ISO on an NTFS drive or a network share |
| "The ADK Oscdimg tool is required to create media containing custom images." | A tool of the ADK is missing | Repair the ADK from the [ADK](../foundry-osd/adk.md) page |

**Collect:** The full text of the dialog and `Foundry.log`.
