# Update a USB drive

Update existing Foundry USB media when configuration or runtime content changes.

## Before updating

- Confirm that the selected drive is Foundry deployment media.
- Confirm that required offline cache content is stored outside the boot partition that Foundry updates.
- Close applications that may be using files on the USB drive.
- Review the current configuration because the update uses the active Foundry OSD settings.

If Foundry cannot confirm that the selected drive is still the same device, the update stops. Refresh the removable-device list, select the drive again, and retry. See [disk identity guidance](create-usb.md#if-the-disk-identity-cannot-be-confirmed) if the warning persists.

Foundry checks the prepared boot files against the existing BOOT partition's capacity before formatting it. If they do not fit or the capacity cannot be verified, the update stops before formatting. Reduce customizations or drivers, or [create an ISO](create-iso.md) when more space is needed. If capacity cannot be verified, check access to the source files, reconnect the USB drive, and retry.

## Update the drive

1. Connect the existing Foundry USB drive.
2. Open **Start** and select the correct target.
3. Confirm Foundry recognizes the drive as existing Foundry media.
4. Resolve readiness items.
5. Select **Update USB**. Foundry refreshes the boot partition while preserving the cache partition.
6. Keep the drive connected until the update completes.
7. Test boot the updated media before production use.

<figure>
  <img src="../../.gitbook/assets/foundry-osd-media-update-usb-01-action.png" alt="Foundry OSD showing recognized deployment media and the Update USB action">
  <figcaption>Update recognized Foundry USB media without rebuilding its cache partition or re-downloading cached Windows sources.</figcaption>
</figure>

## Custom image content

When [custom images](../customization/custom-windows-images.md) are enabled, an update stages the current profile's new managed image content on the data partition and verifies capacity before refreshing BOOT. Keep enough free space for the new content as well as retained content. Manual WIMs and unrelated caches are preserved. Old managed images may remain physically present but are not selected by a new manifest. If an update is interrupted, repeat it and validate the resulting media before use.
