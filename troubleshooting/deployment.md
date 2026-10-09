# Windows deployment troubleshooting

Use this page when Foundry Deploy refuses to continue, shows **Deployment failed**, or completes a deployment that does not start Windows. Entries follow the order of a deployment. Each one says whether the target disk was already erased.

## Start here

1. On the **Deployment failed** screen, note the step on the "Failed step: ..." line and select **View error details** to read the complete message.
2. Look at **Steps**. If the failed step is above **Prepare target disk**, the disk was not erased. If it is below, the disk was erased. If it is **Prepare target disk** itself, only "Disk partitioning failed ..." means the erase had started.
3. Find the step or the message in the index below.
4. Collect the evidence before you turn the device off: logs kept in Windows PE memory are lost at the restart.

Many messages are written by Foundry and are quoted here exactly. Others come straight from a Windows tool: they start with a short summary, such as "Disk partitioning failed for disk 0.", followed by an exit code and the tool's own output in English.

## What to collect <a href="#what-to-collect" id="what-to-collect"></a>

Unless an entry says otherwise, **Collect** means this set:

- The "Failed step: ..." line and the text of **View error details** (use **Copy**).
- The archive created by **Tools > Export diagnostics...**, or `X:\Foundry\Logs\FoundryDeploy.log`.
- The device manufacturer and model, the media type (USB, ISO or PXE) and the Windows image and driver source you selected.

Where the files are and how to export them is in [Logs and support information](logs-and-support.md).

## Deploy again after a failure <a href="#deploy-again" id="deploy-again"></a>

Foundry Deploy cannot resume or undo a deployment, and the error screen has no retry button. Correct the cause, restart the device from the deployment media and go through the wizard again. The new deployment erases the disk again. A Windows image or a driver pack already in the USB drive's cache is verified and reused, so it is not downloaded twice.

## Symptom index

### When Foundry Deploy opens and in the wizard

| What you see | Go to |
| --- | --- |
| "The password is incorrect. Try again." | [Password refused](#password-incorrect) |
| Foundry Deploy closes as soon as it opens | [Closes at start](#closes-at-start) |
| The **Target disk** list is empty | [No disk](#no-disk) |
| "Blocked: system disk", "Blocked: boot disk", "Blocked: read-only", "Blocked: offline" | [Disk blocked](#disk-blocked) |
| "The disk identity is missing, ambiguous or has changed. ..." | [Disk identity](#disk-identity) |
| **Device inventory** shows **Unavailable** everywhere | [Hardware not detected](#hardware-not-detected) |
| A message under **Computer name** | [Computer name](#computer-name) |
| A message under **Answer file** | [Answer file rejected](#answer-file-rejected) |
| **Next** stays unavailable on **Target device** | [Next unavailable](#next-unavailable) |
| A message under the custom image selectors | [Custom image cannot be selected](#custom-image-selection) |
| "\<manufacturer>: no matching model or version" | [No matching driver pack](#no-matching-driver-pack) |
| **Deploy** stays unavailable, or returns to the summary | [Deploy does not start](#deploy-does-not-start) |

### Failed step, disk not erased

| Failed step | Go to |
| --- | --- |
| **Validate answer file** | [Answer file validation](#validate-answer-file) |
| **Check deployment setup**: "Windows optional feature configuration is invalid." | [Optional feature configuration](#optional-feature-configuration) |
| **Check deployment setup**: "Post-installation preparation failed. ..." | [Post-installation preparation](#post-installation-preparation-fails) |
| **Check deployment setup**: "The post-installation components could not be prepared. ..." | [Post-installation components](#post-installation-components) |
| **Check deployment setup**: a custom image message | [Custom image rejected](#custom-image-rejected) |
| **Check deployment setup**: "The image URL must use HTTP or HTTPS. ..." | [Image address](#image-address) |

### Failed step, disk erased or not depending on the media

| Message | Go to |
| --- | --- |
| "The target disk does not have enough space ..." | [Not enough space](#not-enough-space) |
| "The selected Windows edition is unavailable or ambiguous. ..." | [Edition unavailable](#edition-unavailable) |
| "The image cache cannot be prepared. ..." | [Cache unavailable](#cache-unavailable) |
| "The image size or hash metadata in the catalog is invalid. ..." | [Catalog metadata](#catalog-metadata) |
| "The image source is unavailable. ..." | [Image source unavailable](#image-source-unavailable) |
| "A secure connection could not be established. ..." | [Secure connection](#secure-connection) |
| "The transfer timed out. ..." | [Transfer timed out](#transfer-timed-out) |
| "The downloaded Windows image failed hash verification. ..." | [Image verification failed](#image-verification-failed) |
| "Deployment readiness could not be confirmed. ..." | [Readiness not confirmed](#readiness-not-confirmed) |
| "Checking cache..." for a long time | [Cache check is slow](#cache-check-is-slow) |

### Failed step, disk erased

| Failed step | Go to |
| --- | --- |
| **Prepare target disk** | [Disk partitioning failed](#disk-partitioning-failed) |
| **Apply Windows image** | [Image apply failed](#image-apply-failed) |
| **Configure Windows boot** | [Boot configuration failed](#boot-configuration-failed) |
| **Copy answer file** | [Answer file not copied](#answer-file-not-copied) |
| **Download driver pack** fails | [Driver pack download](#driver-pack-download) |
| **Download driver pack** is skipped | [No driver from Microsoft Update Catalog](#no-driver-from-microsoft-update-catalog) |
| **Extract driver pack**, **Install Windows drivers** | [Driver extraction or installation](#driver-extraction-or-installation) |
| **Stage driver installer** | [Driver installer not staged](#driver-installer-not-staged) |
| A firmware update step | [Firmware update](#firmware-update) |
| **Set computer name**, **Configure Windows setup** | [Name or Windows setup](#name-or-windows-setup) |
| **Configure Windows features** | [Optional features](#optional-features) |
| **Prepare setup tasks**: "Post-installation staging failed. ..." | [Post-installation staging](#post-installation-staging) |
| **Configure Windows recovery**, **Install recovery drivers**, **Hide recovery partition** | [Windows recovery](#windows-recovery) |
| An Autopilot step | [Autopilot step](#autopilot-step) |
| "System reboot" | [Restart does not start](#restart-does-not-start) |

### After the deployment

| What you see | Go to |
| --- | --- |
| **Deployment cancelled** | [Cancelled](#cancelled) |
| The device lost power during the deployment | [Power loss](#power-loss) |
| The device returns to the deployment media | [Returns to the media](#returns-to-the-media) |
| The device does not start Windows | [Does not start Windows](#does-not-start-windows) |
| The post-installation console fails, or Windows is not activated | [After the restart troubleshooting](after-the-restart.md) |
| "Diagnostics could not be exported. Check the log for details." | [Logs and support information](logs-and-support.md#export-failed) |

---

**From here to the next notice, the disk has not been erased.** These problems appear when Foundry Deploy opens, in the wizard, or in the checks that run before **Prepare target disk**.

## "The password is incorrect. Try again." <a href="#password-incorrect" id="password-incorrect"></a>

**Where:** the **Protected deployment** window, when Foundry Deploy opens.

**Cause:** the Deployment password typed is not the one set when the media was created. Each failed attempt adds one second of waiting, up to five seconds.

**Fix:**

1. Show the password with the show-or-hide button and check each character: the keyboard layout of Windows PE can differ from the one printed on your keyboard.
2. Ask the administrator for the password of this media. A password cannot be recovered from the media.
3. If the password is lost, the administrator recreates the media. See [Password protection](../foundry-osd/general.md#password-protection).

**Collect:** nothing. Never write the password in a support request.

## Foundry Deploy closes as soon as it opens <a href="#closes-at-start" id="closes-at-start"></a>

**Where:** right after Foundry Connect, with or without a password prompt. There is no message.

**Cause:** **Cancel** was selected in the **Protected deployment** window, or the deployment configuration on the media cannot be read, or the media carries protected content without the information needed to unlock it. Foundry Deploy refuses to run rather than continue without that content.

**Fix:**

1. Restart the device from the deployment media and enter the password.
2. If Foundry Deploy closes without asking for anything, the media is damaged or incomplete: the administrator recreates it.

**Collect:** `X:\Foundry\Logs\FoundryDeploy.log` if you can still reach it, or the `Logs` folder of the USB drive's **Foundry Cache** volume. See [Log locations](logs-and-support.md#log-locations).

## The Target disk list is empty <a href="#no-disk" id="no-disk"></a>

**Where:** **Target device**, **Target disk**. No message is shown.

**Cause:**

- Windows PE has no driver for the storage controller (Intel VMD or RST, RAID controllers).
- The only disk is connected over USB. Foundry never offers a USB disk as a target.
- Foundry could not read the disk list. The log then contains "Failed to inspect target disks."

**Fix:**

1. Check in the firmware setup that the internal disk is detected.
2. Ask the administrator to add the storage driver to the media, in the driver settings of the [General](../foundry-osd/general.md) page, then recreate or update the media.
3. As an alternative, change the storage controller mode in the firmware setup (for example from RAID or VMD to AHCI) if your organization allows it.
4. Restart from the deployment media: the list is read once, when Foundry Deploy opens.

**Collect:** the standard set, and the storage controller model.

## "Blocked: system disk", "Blocked: boot disk", "Blocked: read-only" or "Blocked: offline" <a href="#disk-blocked" id="disk-blocked"></a>

**Where:** **Target device**, at the end of a line of **Target disk**, and under the list once the disk is selected.

**Cause:** Windows PE reports that it started from this disk, or that the disk is read-only or offline. Foundry does not let you select such a disk.

**Fix:**

1. **Blocked: system disk** or **Blocked: boot disk**: start the device from the USB drive, the ISO or PXE instead of this disk.
2. **Blocked: read-only**: remove the hardware write protection, or clear the attribute with DiskPart (`attributes disk clear readonly`) from another Windows or Windows PE session.
3. **Blocked: offline**: check the controller or RAID configuration, or bring the disk online with DiskPart (`online disk`) from another session.
4. Restart from the deployment media.

**Collect:** the standard set, and the complete **Target disk** line.

## "The disk identity is missing, ambiguous or has changed. Restart Foundry Deploy and select the disk again." <a href="#disk-identity" id="disk-identity"></a>

**Where:** under **Target disk**; as a **Status** row in the **Target device** category of the summary; or as the error of **Check deployment setup** or **Prepare target disk**. In every case the disk has not been erased.

**Cause:** the disk does not report an identity that separates it from the other disks, or a disk was added, removed or renumbered after you selected the target. Foundry rechecks the disk just before erasing and stops if it is not exactly the one you confirmed.

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
2. If it happens again on this model only, deploy with a typed computer name and a driver source chosen by hand, and report the model.
3. If it happens on every device, the administrator recreates the media.

**Collect:** the standard set. The log records the detection error.

## A message under Computer name <a href="#computer-name" id="computer-name"></a>

**Where:** **Target device**, under **Computer name**: "Computer name must contain 1 to 15 valid characters (letters, numbers, or hyphen)." or a line such as "Serial number: Unavailable".

**Cause:** the name is empty, longer than 15 characters, contains another character, or is made of digits only. "\<value>: Unavailable" means the administrator's naming rule uses a device value (serial number, manufacturer, model, asset tag or system UUID) that this device does not report, or reports as a placeholder.

**Fix:**

1. If the field accepts typing, enter a complete valid name. A numeric serial number needs a prefix, for example `PC-`.
2. If the field is read-only, the administrator locked the generated name: they change the rule in [Machine naming](../foundry-osd/customization/machine-naming.md) and recreate or update the media.

**Collect:** the message, and the device values shown under **Device inventory**.

## A message under Answer file <a href="#answer-file-rejected" id="answer-file-rejected"></a>

**Where:** **Target device**, under **Answer file**, and as a **Status** row in the summary. **Next** stays unavailable.

| Message | Cause |
| --- | --- |
| "The selected answer file is unavailable, invalid, or incompatible with the selected Windows architecture. Choose another file or rebuild the media." | The file cannot be read from the media, is not a valid answer file, or was written for another architecture than the selected Windows image |
| "The configured default answer file is missing. Rebuild the boot media." | The file the administrator chose as default is not on the media |
| "This answer file conflicts with the configured Autopilot enrollment mode. Choose another file or change the media configuration." | The file contains settings that cannot work with the Autopilot method of this media |

**Fix:**

1. Select another file in **Answer file**, or **Use Foundry settings** if your organization allows it.
2. Otherwise the administrator corrects the file in [Unattend](../foundry-osd/customization/unattend.md) and recreates or updates the media.

With Domain Join, the answer file must also set a fixed computer name: see [Domain Join troubleshooting](domain-join.md).

**Collect:** the message and the name of the selected file. Do not attach the answer file itself: it can contain passwords.

## Next stays unavailable on Target device <a href="#next-unavailable" id="next-unavailable"></a>

**Where:** **Target device**. No message explains it.

**Cause:**

- The Windows catalog could not be downloaded when Foundry Deploy opened, so there is no Windows image to offer. The catalog is loaded once, during **Initializing components...**. Media that provides custom images still lets you continue.
- The answer file selection is not valid: see the entry above.

**Fix:**

1. Check the network: cable or Wi-Fi, proxy, firewall, date and time. See [Network and Foundry Connect troubleshooting](network.md) and [Network endpoints](../reference/network-endpoints.md).
2. Restart the device from the deployment media. Foundry Deploy has no button to reload the catalog.

**Collect:** the standard set. The log contains "Operating system catalog load failed" with the reason.

## A message under the custom image selectors <a href="#custom-image-selection" id="custom-image-selection"></a>

**Where:** **Operating system**, with **Image source** set to **Custom image**. **Next** stays unavailable until an image and an index are selected.

| Message | What to do |
| --- | --- |
| "No custom images were found. Attach the correct media or add a WIM to the documented USB folder." | Connect the complete USB drive or ISO created with this boot image, then select **Windows catalog** and **Custom image** again |
| "The custom image manifest is missing, damaged, or does not match this boot media. Recreate the media or choose another source." | The image files and the boot image come from different media builds. The administrator recreates the media |
| "The configured default image or index is unavailable. Choose an image and index explicitly." | The administrator's preferred image or index is not on this media. Select an image and an index yourself |
| "The default image exists on more than one attached volume. Choose the source volume explicitly." | Two connected drives carry the same image. Select the one to use, or disconnect the other drive |
| "The image cannot be read or its selected index or metadata has changed. Select the image again and try again." | The file is damaged, was replaced, or the drive was removed. Select the image again |

**Collect:** the message, and the list of drives connected to the device. See [Custom Windows images](../foundry-osd/customization/custom-windows-images.md).

## "\<manufacturer>: no matching model or version" <a href="#no-matching-driver-pack" id="no-matching-driver-pack"></a>

**Where:** **Summary**, as a **Status** row in the **Drivers** category. **Deploy** stays unavailable.

**Cause:** **Driver source** is set to a manufacturer whose catalog has no pack for the selected model, Windows version and architecture.

**Fix:**

1. Select **Edit** on **Drivers**.
2. Set **Driver source** to **Microsoft Update Catalog**, which provides storage and network drivers only, or to **None**.
3. Or select another **Model** on purpose, if you know its pack suits the device.

**Collect:** the device manufacturer, model and product shown under **Device inventory**, and the Windows version selected.

## Deploy stays unavailable, or returns to the summary <a href="#deploy-does-not-start" id="deploy-does-not-start"></a>

**Where:** **Summary**.

**Cause:** a selection is incomplete or was refused when you selected **Deploy**. Foundry adds a **Status** row to the category concerned.

**Fix:** read the **Status** rows, then:

1. **Target device**: correct the computer name, the answer file or the disk. For "The disk identity is missing, ambiguous or has changed. ..." see [the disk identity entry](#disk-identity).
2. **Drivers**: see [the driver pack entry](#no-matching-driver-pack).
3. **Autopilot**: select a profile in the [Windows Autopilot step](../foundry-deploy/autopilot.md).
4. "The domain join information is missing or not valid. Deployment has not started.": see [Domain Join troubleshooting](domain-join.md).
5. If you answered **No** to **Confirm disk erase**, nothing changed: select **Deploy** again when ready.

**Collect:** the text of each **Status** row.

## Failed step: Validate answer file <a href="#validate-answer-file" id="validate-answer-file"></a>

**Where:** first step of the deployment. Disk not erased.

**Cause:** the custom answer file could not be read again from the media, or no longer passes the checks made in the wizard. With Domain Join, "The custom answer-file computer name differs from the computer name confirmed for the domain join." means the name in the file is not the one shown in the wizard.

**Fix:**

1. Check that the deployment media is still connected.
2. Restart from the deployment media and select the answer file again, or another one.
3. For the Domain Join message, see [Domain Join troubleshooting](domain-join.md).

**Collect:** the standard set, without the answer file.

## "Windows optional feature configuration is invalid." <a href="#optional-feature-configuration" id="optional-feature-configuration"></a>

**Where:** **Check deployment setup**. Disk not erased.

**Cause:** the optional features stored on the media are inconsistent: a feature is listed twice, is not known to this version of Foundry Deploy, or is enabled while a feature it depends on is disabled.

**Fix:** the administrator reviews [Optional features](../foundry-osd/customization/optional-features.md) and recreates or updates the media.

**Collect:** the standard set.

## "Post-installation preparation failed. Check the deployment log for details." <a href="#post-installation-preparation-fails" id="post-installation-preparation-fails"></a>

**Where:** **Check deployment setup**. Disk not erased.

**Cause:** the deployment needs scripts or packages that Foundry cannot find or verify on the media.

- The USB drive or ISO was removed, or is not the one created with this boot image.
- The device started over PXE: a PXE boot image does not carry imported scripts and packages.
- The files on the media were changed after the media was created.

**Fix:**

1. Connect the complete USB drive or ISO created together with the boot image, then restart from it.
2. Over PXE, connect the matching media, or have the administrator disable the actions that use imported content. See [Deploy with PXE](../foundry-osd/media/pxe-deployment.md).
3. Do not mix a boot image with content from another media build.

**Collect:** the standard set. The log names the missing or changed item.

## "The post-installation components could not be prepared. Check your connection and retry, or recreate the boot media. See the deployment log for details." <a href="#post-installation-components" id="post-installation-components"></a>

**Where:** **Check deployment setup**. Disk not erased.

**Cause:** the program that runs the work planned after the restart is missing from the media, and could not be downloaded either.

**Fix:**

1. Check the network connection and restart from the deployment media.
2. If it fails again, the administrator recreates the media.

**Collect:** the standard set.

## A custom image is rejected when the deployment starts <a href="#custom-image-rejected" id="custom-image-rejected"></a>

**Where:** **Check deployment setup** or **Check Windows image**. Disk not erased.

| Message | Cause |
| --- | --- |
| "The source disk cannot be confirmed as separate from the destination disk. Select another source." | The image file is stored on the disk you are about to erase, or Foundry cannot tell which disk holds it |
| "The image cannot be read or its selected index or metadata has changed. Select the image again and try again." | The file changed or became unreadable after you selected it, or the selected index no longer matches |
| "Custom image is not ready." | The drive that holds the image was removed after the first check |

**Fix:**

1. Keep the image on the USB drive or ISO, never on the target disk.
2. Keep that drive connected, restart from the deployment media and select the image and its index again.
3. If the image is refused again, the administrator checks the file. See [Custom Windows images](../foundry-osd/customization/custom-windows-images.md).

**Collect:** the standard set, and the image name and index.

## "The image URL must use HTTP or HTTPS. Select another image, or restart the device from the deployment media to reload the catalog." <a href="#image-address" id="image-address"></a>

**Where:** **Check deployment setup**. Disk not erased. The related message "Operating system URL is missing." has the same cause.

**Cause:** the catalog entry of the selected image has no usable download address.

**Fix:**

1. Restart from the deployment media, which reloads the catalog.
2. Select another **Windows update** or another image.

**Collect:** the standard set, and the **Version**, **Windows update**, **Language** and **Edition (Target)** you selected.

{% hint style="warning" %}
**From here to the next notice, the disk may or may not have been erased.** These messages come from the download and the check of the Windows image. With a USB drive that has enough cache space they appear before **Prepare target disk**. With an ISO, or a USB drive whose cache is too small, they appear after it. Look at **Steps** to know.
{% endhint %}

## "The target disk does not have enough space for the known deployment requirements. Select a larger disk." <a href="#not-enough-space" id="not-enough-space"></a>

**Where:** **Check deployment setup**, **Check Windows image** or **Prepare target disk** (disk not erased), or **Apply Windows image** (disk erased).

**Cause:** the disk is smaller than what Foundry can add up:

- about 5.3 GB for the EFI, MSR and recovery partitions;
- the expanded size of the Windows image, plus 1 GB of working space;
- the downloaded image file, when it has to be stored on the target disk (ISO, or USB cache too small);
- the driver pack, when it has to be stored on the target disk;
- the post-installation content.

**Fix:**

1. Select a larger disk.
2. Or deploy from a USB drive with enough free space in its cache, so that the image file and the driver pack stay off the target disk.

**Collect:** the standard set, and the disk size shown in **Target disk**.

## "The selected Windows edition is unavailable or ambiguous. Select another image." <a href="#edition-unavailable" id="edition-unavailable"></a>

**Where:** **Check deployment setup** or **Check Windows image**.

**Cause:** the image file does not contain the edition selected in **Edition (Target)**, or contains it more than once.

**Fix:** restart from the deployment media and select another **Edition (Target)** or another **Windows update**.

**Collect:** the standard set, and your selections on **Operating system**.

## "The image cache cannot be prepared. Check its free space and write access." <a href="#cache-unavailable" id="cache-unavailable"></a>

**Where:** **Check deployment setup**, **Download Windows image** or **Check Windows image**.

**Cause:**

- The USB drive's cache is full, write-protected or was removed.
- The disk filled up during the download.
- The cache sits on the disk selected as target. Foundry refuses to erase the disk it downloads to.

**Fix:**

1. Reconnect the USB drive and check that it is not write-protected.
2. Free space in the cache, or have the administrator [update the USB drive](../foundry-osd/media/update-usb.md).
3. Check that you did not select the USB drive's own disk, or a disk that holds the cache, as target.
4. Restart from the deployment media.

**Collect:** the standard set, and the free space of the **Foundry Cache** volume.

## "The image size or hash metadata in the catalog is invalid. Select another image, or restart the device from the deployment media to reload the catalog." <a href="#catalog-metadata" id="catalog-metadata"></a>

**Where:** **Check deployment setup**, **Download Windows image** or **Check Windows image**. The message "The image's expanded size is unavailable or invalid. Select another image." belongs to the same family: Foundry could not read valid size information from the catalog or from the image file.

**Cause:** the catalog entry is wrong, or the image file cannot be read as a Windows image.

**Fix:**

1. Restart from the deployment media, which reloads the catalog.
2. Select another **Windows update** for the same version.
3. If only one image fails, report it: the catalog is published by the Foundry project. See [Catalogs](../reference/catalog.md).

**Collect:** the standard set, and your selections on **Operating system**.

## "The image source is unavailable. Check the network connection and try again." <a href="#image-source-unavailable" id="image-source-unavailable"></a>

**Where:** **Check deployment setup**, **Prepare target disk** (before the erase) or **Download Windows image**.

**Cause:** the download server could not be reached or refused the request: network loss, DNS, proxy or firewall. During **Download Windows image**, Foundry already retried up to five times, ten seconds apart.

**Fix:**

1. Check the cable or the Wi-Fi signal, and that the device still has an address: the progress page shows it under **Session**.
2. Check that the proxy or firewall allows the hosts listed in [Network endpoints](../reference/network-endpoints.md).
3. Restart from the deployment media. A download does not resume: it starts again from the beginning.

**Collect:** the standard set. The log gives the host that failed and the HTTP status.

## "A secure connection could not be established. Check the device date and time and the trusted certificates in your boot media, including any HTTPS proxy certificate." <a href="#secure-connection" id="secure-connection"></a>

**Where:** **Check deployment setup** or any download step. This error is not retried.

**Cause:**

- The device clock is wrong, often because of a flat clock battery, so every certificate looks expired or not yet valid.
- A proxy that inspects HTTPS presents a certificate Windows PE does not trust.

**Fix:**

1. Set the date and time in the firmware setup, then restart from the deployment media. Foundry also corrects the clock at startup when it can: see [Windows PE startup](../foundry-connect/windows-pe-startup.md) and the [Windows PE time zone](../foundry-osd/general.md#windows-pe-time-zone) setting.
2. If your network inspects HTTPS, ask the network administrator for a path that does not replace server certificates, or for the proxy's root certificate to be trusted in the boot image.

**Collect:** the standard set, and the date and time shown by the firmware.

## "The transfer timed out. Check your connection and try again." <a href="#transfer-timed-out" id="transfer-timed-out"></a>

**Where:** a download step.

**Cause:** no data arrived for two minutes. There is no limit on the total duration: a slow download continues as long as data keeps arriving.

**Fix:**

1. Check the network path and its stability, wired if possible.
2. Restart from the deployment media. The partial file is deleted and the download starts again.

**Collect:** the standard set.

## "The downloaded Windows image failed hash verification. Check the network connection and the storage device, then try again." <a href="#image-verification-failed" id="image-verification-failed"></a>

**Where:** **Download Windows image**.

**Cause:** the downloaded file is not identical to the one published in the catalog. A cached image that fails verification is downloaded again automatically; this message means the fresh download failed verification too.

- The transfer was altered or truncated, for example by a proxy.
- The USB drive, the target disk or the device memory is faulty.

**Fix:**

1. Restart from the deployment media and try once more.
2. Try another network path, then another USB drive.
3. If it fails on one device only, test its memory and its disk.

**Collect:** the standard set. The log contains the expected and the actual hash.

## "Deployment readiness could not be confirmed. Restart deployment before preparing the disk." <a href="#readiness-not-confirmed" id="readiness-not-confirmed"></a>

**Where:** **Download Windows image**, **Check Windows image**, **Prepare target disk** (before the erase) or **Apply Windows image**.

**Cause:** something Foundry verified at the start is no longer there when it is needed: the USB drive or the ISO was removed or stopped responding, the cached image disappeared, or the post-installation content changed.

**Fix:**

1. Reconnect the deployment media and check its connector or port.
2. Restart from the deployment media and deploy again.

**Collect:** the standard set.

## "Checking cache..." stays on screen for a long time <a href="#cache-check-is-slow" id="cache-check-is-slow"></a>

**Where:** **Download Windows image** or **Download driver pack**, with a percentage.

**Cause:** this is not an error. Foundry reads the whole cached file to compare it with the catalog before reusing it. A Windows image is several gigabytes, and a slow USB drive takes minutes.

**Fix:** let it finish. If the file does not match, Foundry downloads it again by itself.

**Collect:** nothing.

{% hint style="danger" %}
**From here on, the disk has been erased.** The previous content cannot be recovered and the device may not start. Collect the evidence before turning the device off, then [deploy again](#deploy-again).
{% endhint %}

## "Disk partitioning failed for disk \<n>." <a href="#disk-partitioning-failed" id="disk-partitioning-failed"></a>

**Where:** **Prepare target disk**. The message is followed by an exit code and the output of DiskPart. The disk may be partly erased.

**Cause:** DiskPart could not clean, convert or format the disk: a failing disk, a hardware write protection, a locked self-encrypting drive, or a RAID volume the controller does not let Windows PE repartition. The rare message "No drive letter is available for deployment partitions." means every drive letter is in use.

**Fix:**

1. Read the DiskPart output in **View error details**: it usually names the operation that failed.
2. Check the disk in the firmware setup or the controller utility, and remove any lock or write protection.
3. Disconnect storage and card readers you do not need, then deploy again.
4. If it fails again, replace the disk.

**Collect:** the standard set, with the complete DiskPart output.

## Failed step: Apply Windows image <a href="#image-apply-failed" id="image-apply-failed"></a>

**Where:** **Apply Windows image**. Disk erased.

| Message | Cause |
| --- | --- |
| "OS image apply failed for index \<n>." followed by the output of DISM, or an error text from Windows imaging | The image file is damaged, the media was removed, or the disk or the memory is faulty |
| "Target volume free space could not be checked. Check the target disk before retrying deployment." | The new Windows partition is not accessible after formatting |
| "The target disk does not have enough space ..." | See [Not enough space](#not-enough-space) |
| "Post-installation staging failed. ..." | See [Post-installation staging](#post-installation-staging) |

**Fix:**

1. Deploy again. If the image came from the cache of a USB drive, it is verified again before reuse.
2. For a custom image, check on another device that the file can be applied.
3. If the same device fails twice with different images, test its disk and its memory.

**Collect:** the standard set, with the complete output.

## "BCDBoot configuration failed." <a href="#boot-configuration-failed" id="boot-configuration-failed"></a>

**Where:** **Configure Windows boot**. The message is followed by the output of BCDBoot. Disk erased.

**Cause:** the boot files could not be written to the EFI system partition. "The applied Windows image does not contain bcdboot.exe." means the applied image is not a complete Windows installation.

**Fix:**

1. Deploy again.
2. For a custom image, check that it was captured from a complete Windows installation.

**Collect:** the standard set, with the complete output.

## Failed step: Copy answer file <a href="#answer-file-not-copied" id="answer-file-not-copied"></a>

**Where:** **Copy answer file**: "The custom answer file could not be staged to the target Windows installation." Disk erased.

**Cause:** the file could not be written to the new Windows installation.

**Fix:** deploy again. If it fails again, treat it as a disk problem.

**Collect:** the standard set, without the answer file.

## Failed step: Download driver pack <a href="#driver-pack-download" id="driver-pack-download"></a>

**Where:** **Download driver pack**. Disk erased.

| Message | Cause |
| --- | --- |
| "Hash verification failed for '\<path>' (SHA256). Expected '\<hash>', actual '\<hash>'." | The downloaded pack is not the file the manufacturer catalog describes |
| An HTTP error text, such as a line containing "404 (Not Found)" | The manufacturer moved or removed the file |
| "The selected driver pack content is unavailable." | The download produced no usable file |
| "A secure connection could not be established. ..." or "The transfer timed out. ..." | See [Secure connection](#secure-connection) and [Transfer timed out](#transfer-timed-out) |

**Fix:**

1. Deploy again and select another **Version** of the pack on **Drivers**.
2. Or set **Driver source** to **Microsoft Update Catalog** to get storage and network drivers, and install the others once Windows runs.

**Collect:** the standard set, and the manufacturer, **Model** and **Version** selected.

## Download driver pack is skipped <a href="#no-driver-from-microsoft-update-catalog" id="no-driver-from-microsoft-update-catalog"></a>

**Where:** **Steps**, with **Driver source** set to **Microsoft Update Catalog**. The deployment continues and completes. Point at the step to read one of these reasons:

- "Microsoft Update Catalog is not reachable; skipping driver lookup."
- "Microsoft Update Catalog did not return any applicable driver payloads for the detected critical devices (DiskDrive, Net, SCSIAdapter)."
- "No eligible critical Plug and Play devices (DiskDrive, Net, SCSIAdapter) were found for Microsoft Update Catalog driver lookup."

**Cause:** the catalog site is blocked, or it has no driver for the storage and network devices of this model. Foundry searches these device classes only: disks, storage controllers and network adapters. No driver was added to Windows.

**Fix:**

1. If Windows starts and has network access, install the remaining drivers the way your organization usually does.
2. If Windows does not start, or has no network, deploy again with the manufacturer as **Driver source**.
3. For "not reachable", allow the Microsoft Update Catalog hosts listed in [Network endpoints](../reference/network-endpoints.md).

**Collect:** the reason shown on the step, and the device model.

## Failed step: Extract driver pack or Install Windows drivers <a href="#driver-extraction-or-installation" id="driver-extraction-or-installation"></a>

**Where:** **Extract driver pack** or **Install Windows drivers**. Disk erased.

**Cause:**

- "Driver pack was not downloaded.": the downloaded pack is no longer where Foundry stored it.
- An error from the extraction tool: the archive is damaged, or the disk is full.
- "No extracted INF driver content is available.": the pack contains no driver Windows can add offline.

A driver that Windows refuses, for example because it is unsigned or built for another architecture, does not fail the step. It is recorded as a warning in the log and the other drivers are installed.

**Fix:**

1. Deploy again and select another **Version** of the pack.
2. If a device does not work in Windows although the step completed, search the log for the rejected driver files.

**Collect:** the standard set.

## Failed step: Stage driver installer <a href="#driver-installer-not-staged" id="driver-installer-not-staged"></a>

**Where:** **Stage driver installer**, for Lenovo and Surface packs that install after the restart. Disk erased.

**Cause:** "Driver pack source content is unavailable for first-boot staging." means the downloaded installer is missing. "First-boot driver pack staging was requested without a supported command." means the pack is in a format Foundry cannot run.

**Fix:** deploy again with another **Version** of the pack, or with **Microsoft Update Catalog**.

**Collect:** the standard set, and the **Model** and **Version** selected.

## A firmware update step is skipped or fails <a href="#firmware-update" id="firmware-update"></a>

**Where:** **Download firmware update**, **Extract firmware update** or **Stage firmware update**, when **Apply firmware updates** is checked.

A skipped step does not stop the deployment. Point at it to read the reason:

| Reason | Meaning |
| --- | --- |
| "Firmware updates are skipped while the device is running on battery power." | Connect AC power before you deploy |
| "System firmware hardware identifier is unavailable." | The device does not report the identifier needed to search for an update |
| "Microsoft Update Catalog is not reachable; skipping firmware update." | The catalog site is blocked |
| "No firmware update was found in Microsoft Update Catalog for firmware id '\<id>'." | The manufacturer published no update there for this device |

A failed step stops the deployment with "The selected firmware content is unavailable." or "The extracted firmware content does not contain any INF files.": the downloaded update is not usable.

**Fix:**

1. For a skipped step, nothing is required. Update the firmware with the manufacturer's tool if you need to.
2. For a failed step, deploy again with **Apply firmware updates** cleared.

**Collect:** the reason or the message, and the device model.

## Failed step: Set computer name or Configure Windows setup <a href="#name-or-windows-setup" id="name-or-windows-setup"></a>

**Where:** **Set computer name** or **Configure Windows setup**. Disk erased.

**Cause:** Foundry could not write the Windows setup settings. "An encrypted OOBE account password is missing." means the media configuration declares a local account whose password is not on the media.

**Fix:**

1. Deploy again.
2. If the message returns, the administrator reviews [OOBE](../foundry-osd/customization/oobe.md) and recreates the media.

**Collect:** the standard set.

## Failed step: Configure Windows features <a href="#optional-features" id="optional-features"></a>

**Where:** **Configure Windows features**. Disk erased.

| Message | Cause |
| --- | --- |
| "Matching NetFx3 source is unavailable. \<detail>" | .NET Framework 3.5 needs installation files that match the image exactly, and they are not available |
| "Windows optional feature '\<name>' has a removed payload and no supported local source mapping." | The image does not contain the files of this feature |
| "Windows optional feature verification failed for '\<name>'." | The feature was changed but Windows does not report the requested state |

**Fix:**

1. The administrator removes the feature from [Optional features](../foundry-osd/customization/optional-features.md), or uses an image that contains it, then recreates or updates the media.
2. Deploy again.

**Collect:** the standard set, and the feature named in the message.

## "Post-installation staging failed. Verify the runtime, payloads and Windows answer file before retrying." <a href="#post-installation-staging" id="post-installation-staging"></a>

**Where:** **Apply Windows image** or **Prepare setup tasks**. Disk erased.

**Cause:**

- The applied image, or the custom answer file, already contains a first-start command that conflicts with the one Foundry must add.
- The deployment media was removed after the disk was erased, so the scripts and packages could not be copied.

**Fix:**

1. Keep the deployment media connected until **Deployment complete**.
2. For a custom image or a custom answer file, the administrator checks it against [what a custom answer file overrides](../foundry-osd/customization/unattend.md#what-a-custom-answer-file-overrides).
3. Deploy again.

**Collect:** the standard set. Do not attach the answer file.

## A Windows recovery step is skipped or fails <a href="#windows-recovery" id="windows-recovery"></a>

**Where:** **Configure Windows recovery**, **Install recovery drivers** or **Hide recovery partition**. Disk erased.

Skipped, with "The applied Windows image does not contain winre.wim.": the deployment continues and completes, but the device has no Windows Recovery Environment. This is typical of a custom image captured without `Windows\System32\Recovery\winre.wim`. The administrator captures the image again with that file in place: see [Custom Windows images](../foundry-osd/customization/custom-windows-images.md).

Failed, with one of these messages:

| Message | Cause |
| --- | --- |
| "Required WinPE executable 'winrecfg.exe' was not found. Add the WinPE-WinReCfg optional component to the WinPE image." | The boot image is incomplete |
| "Failed to set the Windows RE image location." | The recovery image was copied but could not be registered |
| "The recovery partition does not contain winre.wim." or "The Windows RE mount directory already has a registered image." | A previous servicing operation was interrupted |
| "Failed to hide the recovery partition." or "Recovery partition letter '\<letter>' is still accessible after sealing." | DiskPart could not remove the drive letter of the recovery partition |

**Fix:**

1. Restart from the deployment media and deploy again: this clears an interrupted operation.
2. For the `winrecfg.exe` message, the administrator recreates the media with a supported Windows ADK. See [ADK](../foundry-osd/adk.md).

**Collect:** the standard set.

## An Autopilot step is skipped or fails <a href="#autopilot-step" id="autopilot-step"></a>

**Where:** **Copy Autopilot profile**, **Register Autopilot device** or **Prepare Autopilot assistant**. Disk erased, Windows installed.

See [Windows Autopilot troubleshooting](autopilot.md).

## Finalize deployment reports that diagnostics were retained <a href="#diagnostics-retained" id="diagnostics-retained"></a>

**Where:** **Finalize deployment** completes with a note ending in "Diagnostic handoff incomplete; evidence retained at '\<path>'." or "Diagnostic persistence incomplete; evidence retained at '\<path>'.".

**Cause:** the logs could not all be copied to their final folder in the installed Windows. The deployment itself succeeded.

**Fix:** nothing is required for the device.

**Collect:** the folder named in the note, in addition to the usual log folder.

## "Failed step: System reboot" <a href="#restart-does-not-start" id="restart-does-not-start"></a>

**Where:** the **Deployment complete** screen turns into **Deployment failed** when the restart is requested, with "Required reboot executable 'wpeutil.exe' was not found." or "wpeutil.exe failed with exit code \<n>. \<detail>".

**Cause:** Windows PE could not restart the device. Windows is installed.

**Fix:**

1. Export the logs.
2. Remove the deployment media, then turn the device off and on.
3. If it happens on every device, the administrator recreates the media.

**Collect:** the standard set.

## "Deployment stopped. The target disk may contain an incomplete installation." <a href="#cancelled" id="cancelled"></a>

**Where:** the **Deployment cancelled** screen, after **Cancel** was selected or the window was closed during a deployment.

**Cause:** the deployment was cancelled. Nothing is undone, and the device does not restart by itself.

**Fix:** if **Prepare target disk** had run, the disk is erased: [deploy again](#deploy-again). See [Cancel a deployment](../foundry-deploy/review-and-deploy.md#cancel-a-deployment).

**Collect:** nothing, unless the cancellation was not intended.

## The device lost power or restarted during the deployment <a href="#power-loss" id="power-loss"></a>

**Where:** any step.

**Cause:** battery, power cable, or a forced restart. If **Prepare target disk** had started, the disk holds an incomplete installation.

**Fix:** connect AC power, start from the deployment media and deploy again.

**Collect:** the logs in Windows PE memory are lost. What can remain is the session folder under `Logs` on the USB drive's **Foundry Cache** volume and, on the target disk, `Foundry\Logs` or `Windows\Temp\Foundry\Logs`. See [Log locations](logs-and-support.md#log-locations).

## After the restart, the device returns to the deployment media <a href="#returns-to-the-media" id="returns-to-the-media"></a>

**Where:** after **Deployment complete**. The device starts Foundry again, starts PXE, or opens the firmware boot menu.

**Cause:**

- The deployment media is still connected and comes first in the firmware boot order.
- The firmware refused to put **Windows Boot Manager** first. Foundry requests it at the end of **Configure Windows boot**, and a refusal does not fail the deployment. The log then contains "The firmware boot order could not be fully updated. The device may start from another boot device after the restart."

**Fix:**

1. Do not select **Start deployment**: that would lead to a second erase.
2. Remove the USB drive or the ISO and restart.
3. If the device still does not start Windows, open the firmware boot menu and select **Windows Boot Manager**.
4. Put **Windows Boot Manager** first in the firmware boot order.

**Collect:** the deployment log from the installed Windows, `C:\Windows\Temp\Foundry\Logs\Deployment`. It lists the firmware boot entries found at the end of the deployment.

## After the restart, the device does not start Windows <a href="#does-not-start-windows" id="does-not-start-windows"></a>

**Where:** after **Deployment complete**: no boot device, a restart loop, or a stop error such as INACCESSIBLE_BOOT_DEVICE.

**Cause:**

- The firmware starts in legacy BIOS (CSM) mode. Foundry always creates a GPT disk that starts in UEFI mode only.
- Windows has no driver for the storage controller. This happens when **Driver source** was **None**, when Microsoft Update Catalog returned nothing, or when the pack is an installer that runs only once Windows has started (Lenovo `.exe`, Surface `.msi`).
- The firmware boot order was not updated: see [the previous entry](#returns-to-the-media).

Foundry erases the whole disk, including encrypted volumes. It does not clear the TPM, remove boot entries left by the previous installation or change the Secure Boot settings.

**Fix:**

1. In the firmware setup, set the boot mode to UEFI and disable legacy or CSM boot.
2. Select **Windows Boot Manager** in the firmware boot menu.
3. Deploy again with a manufacturer **Driver source**, or change the storage controller mode to one Windows supports without an extra driver.
4. Remove old boot entries in the firmware setup if several entries named **Windows Boot Manager** exist.

**Collect:** the firmware boot mode, the exact stop code, and the deployment logs left on the target disk: see [Collect logs from a device that does not start](logs-and-support.md#collect-logs-from-a-device-that-does-not-start).
