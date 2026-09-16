# Create a USB drive

Create bootable USB media for physical deployment.

{% hint style="danger" %}
The selected USB drive can be erased. Verify its identity, capacity, and contents before confirming creation.
{% endhint %}

## Create the drive

1. Connect the USB drive.
2. Open **Start** and refresh removable devices if necessary.
3. Select the correct USB target.
4. Choose the required layout and format options.
5. Resolve all blocking readiness items.
6. Select **Create USB** and confirm the destructive operation.
7. Keep the drive connected until verification and cleanup complete.

<figure>
  <img src="../../.gitbook/assets/foundry-osd-media-create-usb-01-confirmation.png" alt="Foundry OSD confirmation before formatting and creating a USB drive">
  <figcaption>Verify the selected USB drive before confirming the destructive operation.</figcaption>
</figure>

## If the disk identity cannot be confirmed

Foundry checks that the connected drive still matches the one you confirmed before changing it. A missing, changed or ambiguous device identity, or a changed disk number, stops the operation.

Refresh the removable-device list, select the intended drive again, and confirm creation. If the warning remains, use a drive or connection that reports a distinct device identity. Keep the drive connected throughout creation; do not swap drives after confirmation.

## Validate the drive

Safely eject the drive, boot representative hardware, and confirm that Foundry Connect and Foundry Deploy start correctly.
