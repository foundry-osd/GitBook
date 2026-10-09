# Checks and image download

Failures of the first deployment steps: **Validate answer file**, **Check deployment setup**, **Download Windows image** (**Resolve custom image** for a custom image) and **Check Windows image**, and the failures of **Prepare target disk** that happen before the erase.

{% hint style="warning" %}
On this page the disk may or may not have been erased. With an ISO, or a USB drive whose cache cannot hold the image, **Download Windows image** and **Check Windows image** run after **Prepare target disk**. Look at **Steps**: a failed step listed below **Prepare target disk** means the disk is erased.
{% endhint %}

**Collect** refers to the standard set of [What to collect](../deployment.md#what-to-collect). To start again, see [Deploy again after a failure](../deployment.md#deploy-again).

| What you see | Go to |
| --- | --- |
| "Failed step: Validate answer file" | [Answer file validation](#validate-answer-file) |
| "Windows optional feature configuration is invalid." | [Optional feature configuration](#optional-feature-configuration) |
| "Post-installation preparation failed. ...", "The post-installation components could not be prepared. ..." | [Post-installation preparation](#post-installation-preparation-fails) |
| A custom image message, or "Custom image is not ready." | [Custom image rejected](#custom-image-rejected) |
| "The target disk does not have enough space ..." | [Not enough space](#not-enough-space) |
| "The selected Windows edition is unavailable or ambiguous. ..." | [Edition unavailable](#edition-unavailable) |
| "The image cache cannot be prepared. ..." | [Cache unavailable](#cache-unavailable) |
| "The image size or hash metadata ...", "The image's expanded size ...", "The image URL must use HTTP or HTTPS. ...", "Operating system URL is missing." | [Catalog entry](#catalog-metadata) |
| "The image source is unavailable. ..." or "The transfer timed out. ..." | [Download fails](#image-source-unavailable) |
| "A secure connection could not be established. ..." | [Secure connection](#secure-connection) |
| "The downloaded Windows image failed hash verification. ..." | [Image verification failed](#image-verification-failed) |
| "Deployment readiness could not be confirmed. ..." | [Readiness not confirmed](#readiness-not-confirmed) |
| "No drive letter is available for deployment partitions." | [No drive letter](#no-drive-letter) |
| "Checking cache..." for a long time | [Cache check is slow](#cache-check-is-slow) |
| "The disk identity is missing, ambiguous or has changed. ..." | [Before the deployment starts](before-deployment-starts.md#disk-identity) |

## Failed step: Validate answer file <a href="#validate-answer-file" id="validate-answer-file"></a>

**Where:** **Validate answer file**, the first step of a deployment that uses a custom answer file. Disk not erased. The message is in English.

**Cause:** this step runs a check the wizard does not run. When the deployment has work to do after the restart, Foundry adds its own command to the answer file, and the file must follow [the rules for that](../../foundry-osd/customization/unattend.md#when-foundry-adds-its-command). Selecting the same file again fails again.

<details>
<summary>What each message means, for the administrator</summary>

| Message | What is wrong in the file |
| --- | --- |
| "The answer file contains duplicate specialize passes." | More than one `specialize` pass |
| "The specialize pass is already marked as processed." | The pass carries `wasPassProcessed` |
| "The Windows Deployment component conflicts with the selected image architecture." | `Microsoft-Windows-Deployment` appears twice in that pass, or targets another architecture than the selected Windows image |
| "Duplicate RunSynchronous lists are not supported." | More than one `RunSynchronous` list |
| "RunSynchronous orders must be unique integers from 1 through 500." | An `Order` value is missing, repeated or out of range |
| "No RunSynchronous order remains after the existing commands." | A command already uses `Order` 500 |
| "The answer file contains duplicate Foundry post-installation commands.", "The existing Foundry command conflicts with automatic post-installation integration.", or another message naming the Foundry command | The file already contains Foundry's command. Remove it |

</details>

On Domain Join media, "The custom answer-file computer name differs from the computer name confirmed for the domain join." means the name in the file is not the one shown in the wizard: see [Domain Join troubleshooting](../domain-join.md).

If the step shows "The selected answer file is unavailable, invalid, or incompatible with the selected Windows architecture." instead, the file could not be read again from the media: check that the deployment media is still connected.

**Fix:**

1. To deploy now, restart from the deployment media and select another answer file, or **Use Foundry settings** if your organization allows it.
2. The administrator corrects the file, selects **Refresh source** in [Unattend](../../foundry-osd/customization/unattend.md) and recreates the media.

**Collect:** the standard set. Do not attach the answer file.

## "Windows optional feature configuration is invalid." <a href="#optional-feature-configuration" id="optional-feature-configuration"></a>

**Where:** **Check deployment setup**. Disk not erased.

**Cause:** the optional features stored on the media are inconsistent: a feature is listed twice, is not known to this version of Foundry Deploy, or is enabled while a feature it depends on is disabled.

**Fix:** the administrator reviews [Optional features](../../foundry-osd/customization/optional-features.md) and recreates or updates the media.

**Collect:** the standard set.

## "Post-installation preparation failed. Check the deployment log for details." <a href="#post-installation-preparation-fails" id="post-installation-preparation-fails"></a>

**Where:** **Check deployment setup**. Disk not erased. The same step can show "The post-installation components could not be prepared. Check your connection and retry, or recreate the boot media. See the deployment log for details."

**Cause:**

- First message: the deployment needs scripts or packages that Foundry cannot find or verify. The USB drive or ISO was removed, is not the one created with this boot image, or its files were changed. A PXE boot image does not carry imported scripts and packages.
- Second message: the program that runs the work planned after the restart is missing from the media and could not be downloaded either.

**Fix:**

1. Connect the complete USB drive or ISO created together with the boot image, check the network connection, then restart from the media.
2. Over PXE, connect the matching media, or have the administrator disable the actions that use imported content. See [Deploy with PXE](../../foundry-osd/media/pxe-deployment.md).
3. If the message returns, the administrator recreates the media.

**Collect:** the standard set. The log names the missing or changed item.

## A custom image is rejected when the deployment starts <a href="#custom-image-rejected" id="custom-image-rejected"></a>

**Where:** **Check deployment setup**, **Resolve custom image** (named "Download Windows image" on the error screen) or **Check Windows image**. For a custom image these steps always run before **Prepare target disk**: disk not erased.

**Cause:** it depends on the message.

| Message | Step | Cause |
| --- | --- | --- |
| "The source disk cannot be confirmed as separate from the destination disk. Select another source." | **Check deployment setup** | The image file is stored on the disk you are about to erase, or Foundry cannot tell which disk holds it |
| "The image cannot be read or its selected index or metadata has changed. Select the image again and try again." | **Check deployment setup** | The file changed or became unreadable after you selected it, or the selected index no longer matches |
| "Custom image is not ready." | **Resolve custom image**, **Check Windows image** | The drive that holds the image was removed or stopped responding after the first check |

**Fix:**

1. Keep the image on the USB drive or ISO, never on the target disk, and keep that drive connected.
2. Restart from the deployment media and select the image and its index again.
3. If the image is refused again, the administrator checks the file. See [Custom Windows images](../../foundry-osd/customization/custom-windows-images.md).

**Collect:** the standard set, and the image name and index.

## "The target disk does not have enough space for the known deployment requirements. Select a larger disk." <a href="#not-enough-space" id="not-enough-space"></a>

**Where:** **Check deployment setup** or **Prepare target disk** (disk not erased); **Check Windows image** (erased only when it sits below **Prepare target disk** in **Steps**); **Apply Windows image** (disk erased).

**Cause:** the disk is smaller than what Foundry can add up: the partitions (about 5.3 GB), the expanded Windows image plus 1 GB of working space, the post-installation content, the installation files of .NET Framework 3.5 when that feature is enabled and, when they cannot stay on a USB drive's cache, the downloaded image file and the driver pack.

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
2. Free space in the cache, or have the administrator [update the USB drive](../../foundry-osd/media/update-usb.md).
3. Check that the target is not the disk that holds the cache, then restart from the deployment media.

**Collect:** the standard set, and the free space of the **Foundry Cache** volume.

## "The image size or hash metadata in the catalog is invalid. Select another image, or restart the device from the deployment media to reload the catalog." <a href="#catalog-metadata" id="catalog-metadata"></a>

**Where:** **Check deployment setup**, **Download Windows image** or **Check Windows image**. Three other messages have the same cause and the same fix:

- "The image's expanded size is unavailable or invalid. Select another image."
- "The image URL must use HTTP or HTTPS. Select another image, or restart the device from the deployment media to reload the catalog." (**Check deployment setup**, disk not erased)
- "Operating system URL is missing." (**Check deployment setup**, disk not erased)

**Cause:** the catalog entry of the selected image has an unusable size, hash or download address, or the image file cannot be read as a Windows image.

**Fix:**

1. Restart from the deployment media, which reloads the catalog, and select another **Windows update** for the same version.
2. If only one image fails, report it: the catalog is published by the Foundry project. See [Catalogs](../../reference/catalog.md).

**Collect:** the standard set, and your selections on **Operating system**.

## "The image source is unavailable. Check the network connection and try again." <a href="#image-source-unavailable" id="image-source-unavailable"></a>

**Where:** **Check deployment setup**, **Prepare target disk** (before the erase) or **Download Windows image**. A download can also stop with "The transfer timed out. Check your connection and try again."

**Cause:** the download server could not be reached or refused the request: network loss, DNS, proxy or firewall. During a download, Foundry already retried up to five times, ten seconds apart. "The transfer timed out. ..." means no data arrived for two minutes; a slow download that keeps receiving data is not stopped.

**Fix:**

1. Check the cable or the Wi-Fi signal, and that the device still has an address: the progress page shows it under **Session**.
2. Check that the proxy or firewall allows the hosts listed in [Network endpoints](../../reference/network-endpoints.md).
3. Restart from the deployment media. A download does not resume: the partial file is deleted and the download starts again from the beginning.

**Collect:** the standard set. The log gives the host that failed and the HTTP status.

## "A secure connection could not be established. Check the device date and time and the trusted certificates in your boot media, including any HTTPS proxy certificate." <a href="#secure-connection" id="secure-connection"></a>

**Where:** **Check deployment setup**, **Prepare target disk** (before the erase) or any download step. This error is not retried.

**Cause:**

- The device clock is wrong, often because of a flat clock battery, so every certificate looks expired or not yet valid.
- A proxy that inspects HTTPS presents a certificate Windows PE does not trust.

**Fix:**

1. Set the date and time in the firmware setup, then restart from the deployment media. Foundry also corrects the clock at startup when it can: see [Windows PE startup](../../foundry-connect/windows-pe-startup.md) and the [Windows PE time zone](../../foundry-osd/general.md#windows-pe-time-zone) setting.
2. If your network inspects HTTPS, ask the network administrator for a path that does not replace server certificates, or for the proxy's root certificate to be trusted in the boot image.

**Collect:** the standard set, and the date and time shown by the firmware.

## "The downloaded Windows image failed hash verification. Check the network connection and the storage device, then try again." <a href="#image-verification-failed" id="image-verification-failed"></a>

**Where:** **Download Windows image**.

**Cause:** the freshly downloaded file is not identical to the one published in the catalog. The transfer was altered or truncated, for example by a proxy, or the USB drive, the target disk or the device memory is faulty. A cached image that fails verification is downloaded again automatically and does not show this message.

**Fix:**

1. Restart from the deployment media and try once more, then on another network path or with another USB drive.
2. If it fails on one device only, test its memory and its disk.

**Collect:** the standard set. The log contains the expected and the actual hash.

## "Deployment readiness could not be confirmed. Restart deployment before preparing the disk." <a href="#readiness-not-confirmed" id="readiness-not-confirmed"></a>

**Where:** **Download Windows image**, **Check Windows image**, **Prepare target disk** (before the erase) or **Apply Windows image** (disk erased).

**Cause:** something Foundry verified at the start is no longer there: the USB drive or the ISO was removed or stopped responding, the cached image disappeared, or the post-installation content changed.

**Fix:** reconnect the deployment media, check its connector or port, then restart from it and deploy again.

**Collect:** the standard set.

## "No drive letter is available for deployment partitions." <a href="#no-drive-letter" id="no-drive-letter"></a>

**Where:** **Prepare target disk**, before anything is written. Disk not erased.

**Cause:** Foundry needs free drive letters for the partitions it is about to create, and every letter from D to Z is in use.

**Fix:** disconnect storage and card readers you do not need, then restart from the deployment media and deploy again.

**Collect:** the standard set, and the list of drives connected to the device.

## "Checking cache..." stays on screen for a long time <a href="#cache-check-is-slow" id="cache-check-is-slow"></a>

This is not an error. Foundry reads the whole cached file to compare it with the catalog before reusing it, which takes minutes on a slow USB drive. Let it finish: if the file does not match, Foundry downloads it again by itself.
