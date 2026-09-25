# Create deployment media

The Start page validates configuration and creates or updates Foundry deployment media.

Choose the intended [deployment profile](../deployment-profiles.md) before starting. Foundry uses the settings, passwords, and selected files from when you start creating media. Later edits or synchronized changes do not alter that running build.

## Review readiness

Resolve every blocking readiness item before starting. Checks cover Windows ADK and Windows PE, architecture, language, boot-image source, output paths, USB target, media options, drivers, networking, runtime configuration, secrets, customization, and Windows Autopilot.

<figure>
  <img src="../../.gitbook/assets/foundry-osd-media-01-readiness-overview.png" alt="Foundry OSD media creation readiness overview">
  <figcaption>Resolve every blocking item before creating or updating deployment media.</figcaption>
</figure>

## Choose an operation

| Consideration | ISO | USB |
| --- | --- | --- |
| Best for | Virtual machines, remote-management virtual media, or external imaging tools | Repeated deployment to physical devices |
| Boot method | Mount the ISO directly or write it with another tool | Boot directly from the prepared USB drive |
| Source cache | Windows sources are downloaded when required by the deployment workflow | A dedicated cache partition retains downloaded Windows sources for reuse |
| Time and bandwidth | Repeated deployments may download the same Windows sources again | Reuses cached sources to reduce deployment time and bandwidth consumption |
| Updating media | Create a new ISO | Update the boot partition while preserving the cache partition |
| Main trade-off | Portable file, but no persistent source cache | Requires a dedicated USB drive and its initial preparation erases the selected device |

Choose [Create an ISO](create-iso.md), [Create a USB drive](create-usb.md), or [Update an existing USB drive](update-usb.md).

PXE is not a native Foundry OSD media output. [Use existing PXE infrastructure](pxe-deployment.md) by importing the boot image from a validated Foundry OSD ISO.

During creation, Foundry reports workspace preparation, driver resolution, image customization, language and component processing, runtime payload provisioning, media creation, verification, and cleanup.

## Cancel media creation

Select **Cancel** in the progress dialog to stop creating or updating media. Downloads stop promptly; a disk or image operation already in progress may need to finish before cleanup can complete. Keep Foundry OSD open and the USB drive connected until the dialog reports cancellation.

Cancellation does not restore overwritten ISO files or USB contents. Create or update the media again before using it for deployment. For a download timeout, see [media creation troubleshooting](../../troubleshooting/media-creation.md#download-times-out).

## Custom image readiness

[Custom Windows images](../customization/custom-windows-images.md) add source availability and default-selection checks to media readiness. Restore missing included WIMs and optional sources or update the profile. Images are stored outside `boot.wim`, so include their size in media and working-space planning.
