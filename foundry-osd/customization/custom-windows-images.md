# Custom Windows images

**Custom Windows images** adds your own Windows images, from a WIM file or an ISO, to the deployment media, so the technician can deploy them instead of an image downloaded from the Foundry catalog.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-custom-images-01-library.png`
- **Capture:** Show the Custom Windows images page switched to Enabled with the PXE warning under the title, the command bar, one image with Included Yes and Status Available, its indexes under Selected image, and Default image source in Foundry Deploy. Use a released build.
{% endhint %}

## Before you start

- **Source.** A `.wim` file, or an ISO that contains `sources\install.wim` or `sources\install.esd`. Split `.swm` images are not supported.
- **Disk space.** Foundry OSD copies the image into `%LOCALAPPDATA%\Foundry\Images\Custom`. That drive needs free space of at least the size of the WIM or, for an `install.esd`, the expanded size of all its indexes plus 1 GB.
- **Media type.** The page states: "Not available with PXE boot. Custom images require the full ISO or USB media."
- **Captured images.** Keep the recovery image in the capture, as described [below](#capture-an-image-that-keeps-windows-recovery).

## Import an image

1. Open **Customization > Custom Windows images** and turn the switch at the top right to **Enabled**.
2. Select **Import ISO/WIM** in the command bar.
3. Select **Browse** and choose the file. Foundry OSD lists the indexes of the image. If the ISO contains both `install.wim` and `install.esd`, choose one under "Choose the installation image in this ISO."
4. Check **Name**. It defaults to the file name, accepts up to 200 characters and must be unique in the current configuration.
5. Select **Import ISO/WIM** and keep the source available until the dialog shows "Import complete". **Cancel** stops the import.

Foundry OSD keeps every index of the image and converts `install.esd` to WIM. A new image is included in the media straight away: its **Included** column shows **Yes**.

## Choose what goes on the media

Select an image row to list its indexes under **Selected image**, then use the command bar.

| Action | Effect |
| --- | --- |
| **Refresh** | Reads the library again and updates the **Status** of each image. |
| **Edit** | Changes the name shown for the image. The file is not renamed. |
| **Include** / **Exclude** | Adds the image to the media you create next, or leaves it out. An excluded image stays in the library. |
| **Remove** | After confirmation, removes the image from the current configuration and deletes its copy from the library. |
| **Set default image** | Includes the image and marks it as **Preferred image**. The technician chooses the index. |
| **Set default index** | Includes the image and marks the selected index row as **Preferred index**. |
| **Clear default** | Clears the preferred image and index. |

Below the tables, **Default image source in Foundry Deploy** decides which source Foundry Deploy shows first: **Foundry catalog**, the default, or **Custom Windows images**. Setting a preferred image does not change it.

- While the page is **Enabled**, Foundry OSD creates media only if at least one image is included and every included image shows the **Status** **Available**, even when the default source is **Foundry catalog**. To create media without custom images, switch the page off.
- Excluding or removing the preferred image clears the preference.
- **Remove** deletes the library copy for every configuration: the others show the image as **Missing**. The original file and existing media are not changed.

## Restore or move an image

[Settings backup and sync](../deployment-profiles.md) carries the list of images and their defaults, not the image files. On another PC, or after the library copy is lost, the image shows **Missing**.

Import the same `.wim` file again, under a name that is not yet used. Foundry OSD recognizes the image by the SHA-256 hash of the WIM, and the existing row keeps its name, inclusion and defaults. This is not guaranteed for an image converted from `install.esd`, because the conversion writes a new WIM file.

## Capture an image that keeps Windows recovery

A captured image should contain `Windows\System32\Recovery\winre.wim`. Before you capture the reference device, run `reagentc /disable` on it, so that Windows moves the recovery image back to that folder.

Without the file, Foundry Deploy skips the **Configure Windows recovery** and **Install recovery drivers** steps. The deployment completes, and the deployed Windows has no recovery environment.

## What the technician sees

In Foundry Deploy, the technician chooses the image source, then the image and the index to apply. Your preferred image and index are preselected. See [Select Windows](../../foundry-deploy/operating-system.md).

## Limits

- 256 images in the library, 1,024 indexes per image, 200 characters per name.
- Foundry OSD checks that it can read the image, not that the image deploys or suits your other customizations. Versions, editions and architectures are not limited to those of the Foundry catalog, and [OS selection](operating-system.md) does not filter custom images.
- .NET Framework 3.5 cannot be turned on in a custom image during deployment. See the limits of [Optional features](optional-features.md).
- For where the images sit on an ISO or a USB drive, see [What each media type carries](../media/README.md#what-each-media-type-carries).

## If something goes wrong

### "The image operation failed. Check the source, available space, and Foundry logs."

- **Where:** the import dialog, or the top of the page after an action.
- **Cause:** one message covers every failure: an ISO without `sources\install.wim` or `sources\install.esd`; too little free space; an ISO that cannot be mounted; a library that already holds 256 images; a source or a `%LOCALAPPDATA%` folder reached through a junction or a symbolic link; an image in use by a media build when you select **Remove**.
- **Fix:** check the source and the free space, then try again. The last "Custom image import failed.", "Custom image preview failed." or "Custom image library operation failed." entry in the log gives the exact cause.
- **Collect:** `%ProgramData%\Foundry\Logs\Foundry.log`. See [Logs and support information](../../troubleshooting/logs-and-support.md).

### The Import ISO/WIM button stays unavailable

- **Where:** the import dialog.
- **Cause:** the dialog shows "An image with this name already exists in this profile.", still shows "Reading image indexes…", or waits for your choice under "Choose the installation image in this ISO."
- **Fix:** change **Name**, wait for the indexes to appear, or choose the installation image.

### "Include at least one available Windows image in this profile. Restore missing included images and fix or clear the preferred image or index before creating or updating media."

- **Where:** the top of the page. **Start** shows **Custom Windows images** as **Needs attention** and media creation is blocked.
- **Cause:** no image is included, an included image shows **Missing**, the preferred image is excluded, or the preferred index no longer exists in the image.
- **Fix:** include an image whose **Status** is **Available**, import a **Missing** image again, or select **Clear default**. To build media without custom images, switch the page off.

## Related

- [Select Windows](../../foundry-deploy/operating-system.md)
- [Start: create deployment media](../media/README.md)
- [Troubleshooting: Media creation](../../troubleshooting/media-creation.md)
- [Troubleshooting: Windows deployment](../../troubleshooting/deployment.md)
