# After the disk is erased

Failures and skipped steps from **Prepare target disk** to the end of the deployment.

{% hint style="danger" %}
On this page the disk has been erased. Its previous content cannot be recovered and the device may not start. Collect the evidence before you turn the device off, then [deploy again](../deployment.md#deploy-again).
{% endhint %}

**Collect** refers to the standard set of [What to collect](../deployment.md#what-to-collect).

| Failed step or symptom | Go to |
| --- | --- |
| **Prepare target disk** | [Disk partitioning failed](#disk-partitioning-failed) |
| **Download Windows image** or **Check Windows image**, listed below **Prepare target disk** | [Checks and image download](checks-and-image-download.md) |
| **Apply Windows image** | [Image apply failed](#image-apply-failed) |
| **Configure Windows boot** | [Boot configuration failed](#boot-configuration-failed) |
| **Copy answer file**, **Set computer name**, **Configure Windows setup** | [Name or Windows setup](#name-or-windows-setup) |
| **Download driver pack**, **Extract driver pack**, **Install Windows drivers**, **Stage driver installer** | [A driver pack step fails](#driver-pack-download) |
| **Download driver pack** is skipped | [No driver from Microsoft Update Catalog](#no-driver-from-microsoft-update-catalog) |
| A firmware update step is skipped or fails | [Firmware update](#firmware-update) |
| **Configure Windows features** | [Optional features](#optional-features) |
| "Post-installation staging failed. ..." | [Post-installation staging](#post-installation-staging) |
| **Configure Windows recovery**, **Install recovery drivers**, **Hide recovery partition** | [Windows recovery](#windows-recovery) |
| "Failed step: Provision Autopilot", or a skipped Autopilot step | [Windows Autopilot troubleshooting](../autopilot.md) |
| **Finalize deployment** completes with "... evidence retained at ..." | Collect the folder named in the note too: see [Log locations](../logs-and-support.md#log-locations) |
| "Failed step: System reboot" | [Restart does not start](#restart-does-not-start) |

The error screen names the Autopilot step "Provision Autopilot". **Steps** shows it as **Copy Autopilot profile**, **Register Autopilot device** or **Prepare Autopilot assistant**.

## "Disk partitioning failed for disk \<n>." <a href="#disk-partitioning-failed" id="disk-partitioning-failed"></a>

**Where:** **Prepare target disk**. The message is followed by an exit code and the output of DiskPart. The disk may be partly erased.

**Cause:** DiskPart could not clean, convert or format the disk: a failing disk, a hardware write protection, a locked self-encrypting drive, or a RAID volume the controller does not let Windows PE repartition.

The same step can show "No drive letter is available for deployment partitions." when every drive letter is in use. With this message the disk has not been touched: disconnect storage and card readers you do not need, then deploy again.

**Fix:**

1. Read the DiskPart output in **View error details** to see which operation failed.
2. Check the disk in the firmware setup or the controller utility, and remove any lock or write protection.
3. Deploy again. If it fails again, replace the disk.

**Collect:** the standard set, with the complete DiskPart output.

## Failed step: Apply Windows image <a href="#image-apply-failed" id="image-apply-failed"></a>

**Where:** **Apply Windows image**.

**Cause:** it depends on the message.

| Message | Cause |
| --- | --- |
| "OS image apply failed for index \<n>." followed by the output of DISM, or an error text from Windows imaging | The image file is damaged, the media was removed, or the disk or the memory is faulty |
| "Target volume free space could not be checked. Check the target disk before retrying deployment." | The new Windows partition is not accessible after formatting |
| "The target disk does not have enough space ..." | See [Not enough space](checks-and-image-download.md#not-enough-space) |
| "Deployment readiness could not be confirmed. ..." | See [Readiness not confirmed](checks-and-image-download.md#readiness-not-confirmed) |
| "Post-installation staging failed. ..." | See [Post-installation staging](#post-installation-staging) |

**Fix:**

1. Deploy again. A cached image is verified again before reuse.
2. For a custom image, check on another device that the file can be applied.
3. If the same device fails with different images, test its disk and its memory.

**Collect:** the standard set, with the complete output.

## "BCDBoot configuration failed." <a href="#boot-configuration-failed" id="boot-configuration-failed"></a>

**Where:** **Configure Windows boot**. The message is followed by the output of BCDBoot.

**Cause:** the boot files could not be written to the EFI system partition. "The applied Windows image does not contain bcdboot.exe." means the applied image is not a complete Windows installation.

**Fix:**

1. Deploy again.
2. For a custom image, check that it was captured from a complete Windows installation.

**Collect:** the standard set, with the complete output.

## Failed step: Copy answer file, Set computer name or Configure Windows setup <a href="#name-or-windows-setup" id="name-or-windows-setup"></a>

**Where:** one of the three steps that write the Windows setup settings to the new installation.

**Cause:**

- "The custom answer file could not be staged to the target Windows installation.", at **Copy answer file**: the file could not be written to the disk.
- "An encrypted OOBE account password is missing.", at **Configure Windows setup**: the media declares a local account whose password is not on the media.

**Fix:**

1. Deploy again. If **Copy answer file** fails again, treat it as a disk problem.
2. If the password message returns, the administrator reviews [OOBE](../../foundry-osd/customization/oobe.md) and recreates the media.

**Collect:** the standard set, without the answer file.

## A driver pack step fails <a href="#driver-pack-download" id="driver-pack-download"></a>

**Where:** **Download driver pack**, **Extract driver pack**, **Install Windows drivers** or **Stage driver installer**.

**Cause:** it depends on the step and the message.

| Step | Message | Cause |
| --- | --- | --- |
| **Download driver pack** | "Hash verification failed for '\<path>' (SHA256). Expected '\<hash>', actual '\<hash>'." | The downloaded pack is not the file the manufacturer catalog describes |
| **Download driver pack** | An HTTP error text, such as a line containing "404 (Not Found)" | The manufacturer moved or removed the file |
| **Download driver pack** | "The selected driver pack content is unavailable." | The download produced no usable file |
| **Download driver pack** | "A secure connection could not be established. ..." or "The transfer timed out. ..." | See [Secure connection](checks-and-image-download.md#secure-connection) and [Download fails](checks-and-image-download.md#image-source-unavailable) |
| **Extract driver pack** | "Driver pack was not downloaded." or an error from the extraction tool | The pack is missing, the archive is damaged, or the disk is full |
| **Install Windows drivers** | "No extracted INF driver content is available." | The pack contains no driver that can be added to Windows before the restart |
| **Stage driver installer** | "Driver pack source content is unavailable for first-boot staging." or "First-boot driver pack staging was requested without a supported command." | The downloaded installer is missing, or the pack is in a format Foundry cannot run |

A driver that Windows refuses does not fail **Install Windows drivers**: it is recorded as a warning in the log and the other drivers are installed.

**Fix:**

1. Deploy again and select another **Version** of the pack on **Drivers**.
2. Or set **Driver source** to **Microsoft Update Catalog** to get storage and network drivers, and install the others once Windows runs.
3. If a device does not work in Windows although the steps completed, search the log for the rejected driver files.

**Collect:** the standard set, and the manufacturer, **Model** and **Version** selected.

## Download driver pack is skipped <a href="#no-driver-from-microsoft-update-catalog" id="no-driver-from-microsoft-update-catalog"></a>

**Where:** **Steps**, with **Driver source** set to **Microsoft Update Catalog**. The deployment continues and completes. Point at the step to read one of these reasons:

- "Microsoft Update Catalog is not reachable; skipping driver lookup."
- "Microsoft Update Catalog did not return any applicable driver payloads for the detected critical devices (DiskDrive, Net, SCSIAdapter)."
- "No eligible critical Plug and Play devices (DiskDrive, Net, SCSIAdapter) were found for Microsoft Update Catalog driver lookup."

**Cause:** the catalog site is blocked, or it has no driver for the disks, storage controllers and network adapters of this model, the only device classes Foundry searches. No driver was added to Windows.

**Fix:**

1. If Windows does not start, or has no network, deploy again with the manufacturer as **Driver source**.
2. For "not reachable", allow the Microsoft Update Catalog hosts listed in [Network endpoints](../../reference/network-endpoints.md).

**Collect:** the reason shown on the step, and the device model.

## A firmware update step is skipped or fails <a href="#firmware-update" id="firmware-update"></a>

**Where:** **Download firmware update**, **Extract firmware update** or **Stage firmware update**, when **Apply firmware updates** is checked.

**Cause:** a skipped step does not stop the deployment. Point at it to read the reason in its tooltip.

| Reason of a skipped step | Meaning |
| --- | --- |
| "Firmware updates are skipped while the device is running on battery power." | Connect AC power before you deploy |
| "System firmware hardware identifier is unavailable." | The device does not report the identifier needed to search for an update |
| "Microsoft Update Catalog is not reachable; skipping firmware update." | The catalog site is blocked |
| "No firmware update was found in Microsoft Update Catalog for firmware id '\<id>'." | The manufacturer published no update there for this device |

A failed step stops the deployment with "The selected firmware content is unavailable." or "The extracted firmware content does not contain any INF files.": the downloaded update is not usable.

**Fix:** after a skipped step, nothing is required; use the manufacturer's tool if the firmware must be updated. After a failed step, deploy again with **Apply firmware updates** cleared.

**Collect:** the reason or the message, and the device model.

## Failed step: Configure Windows features <a href="#optional-features" id="optional-features"></a>

**Where:** **Configure Windows features**.

**Cause:** it depends on the message.

| Message | Cause |
| --- | --- |
| "Matching NetFx3 source is unavailable. \<detail>" | .NET Framework 3.5 needs installation files that match the image exactly, and they are not available |
| "Windows optional feature '\<name>' has a removed payload and no supported local source mapping." | The image does not contain the files of this feature |
| "Windows optional feature verification failed for '\<name>'." | The feature was changed but Windows does not report the requested state |

**Fix:** the administrator removes the feature from [Optional features](../../foundry-osd/customization/optional-features.md), or uses an image that contains it, then recreates or updates the media.

**Collect:** the standard set, and the feature named in the message.

## "Post-installation staging failed. Verify the runtime, payloads and Windows answer file before retrying." <a href="#post-installation-staging" id="post-installation-staging"></a>

**Where:** **Apply Windows image** or **Prepare setup tasks**. The step tells you which cause applies.

**Cause:**

- At **Apply Windows image**: a custom image carries its own answer file. Foundry refuses `Windows\Panther\Unattend\unattend.xml`, `Windows\Panther\Unattend\autounattend.xml` and an `UnattendFile` value under `HKLM\SYSTEM\Setup`, which Windows would use instead of Foundry's file, and a `Windows\Panther\unattend.xml` that cannot take Foundry's command.
- At **Prepare setup tasks**: the deployment media was removed or changed during the deployment, a file could not be copied to the disk, or the answer file on the disk cannot take Foundry's command.

**Fix:**

1. Keep the deployment media connected until **Deployment complete**.
2. For a custom image, the administrator removes the answer file from the reference installation and captures the image again. See [Custom Windows images](../../foundry-osd/customization/custom-windows-images.md).
3. For a custom answer file, the administrator checks it against [the rules Foundry needs to add its command](../../foundry-osd/customization/unattend.md#when-foundry-adds-its-command).
4. Deploy again.

**Collect:** the standard set. Do not attach the answer file.

## A Windows recovery step is skipped or fails <a href="#windows-recovery" id="windows-recovery"></a>

**Where:** **Configure Windows recovery**, **Install recovery drivers** or **Hide recovery partition**.

**Cause:** a skipped step shows "The applied Windows image does not contain winre.wim.". The deployment completes and the device has no Windows Recovery Environment: see [Deployment steps](../../foundry-deploy/review-and-deploy.md#deployment-steps). A failed step shows one of these messages.

| Message | Cause |
| --- | --- |
| "Required WinPE executable 'winrecfg.exe' was not found. Add the WinPE-WinReCfg optional component to the WinPE image." | The boot image is incomplete |
| "Failed to set the Windows RE image location." | The recovery image was copied but could not be registered |
| "The recovery partition does not contain winre.wim." or "The Windows RE mount directory already has a registered image." | A previous servicing operation was interrupted |
| "Failed to hide the recovery partition." or "Recovery partition letter '\<letter>' is still accessible after sealing." | DiskPart could not remove the drive letter of the recovery partition |

**Fix:**

1. Restart from the deployment media and deploy again: this clears an interrupted operation.
2. For the `winrecfg.exe` message, the administrator recreates the media with a supported Windows ADK. See [ADK](../../foundry-osd/adk.md).

**Collect:** the standard set.

## "Failed step: System reboot" <a href="#restart-does-not-start" id="restart-does-not-start"></a>

**Where:** the **Deployment complete** screen turns into **Deployment failed** when the restart is requested, with "Required reboot executable 'wpeutil.exe' was not found." or "wpeutil.exe failed with exit code \<n>. \<detail>".

**Cause:** Windows PE could not restart the device. Windows is installed.

**Fix:**

1. Export the logs, remove the deployment media, then turn the device off and on.
2. If it happens on every device, the administrator recreates the media.

**Collect:** the standard set.
