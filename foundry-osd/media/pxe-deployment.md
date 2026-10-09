# Deploy with PXE

Foundry OSD does not create PXE media and does not configure a PXE server. You can still start devices from a PXE server you already run, by importing the boot image of a Foundry ISO into it. This use is not officially supported: the PXE server, DHCP and TFTP stay the responsibility of their administrator.

## What a PXE boot image delivers

A PXE server sends one file to the device, `sources\boot.wim`. Everything Foundry OSD stores next to that file on an ISO or a USB drive stays behind.

| Delivered with the boot image | Not delivered |
| --- | --- |
| Windows PE with its drivers, and Foundry Connect | [Custom Windows images](../customization/custom-windows-images.md) |
| Every option of your configuration: network, Windows Autopilot, Domain Join, computer name, OOBE, answer file, optional features, AppX removals, AI components | [Post-installation](../customization/post-installation.md) actions that use imported files: every **PowerShell script (.ps1)** and **Software (.exe/.msi)** action, and a **Command line** action with imported content |
| Post-installation actions that need no imported file: **Command line** without content and **Restart Windows** | A cache: each deployment downloads Windows and the driver pack again |

Foundry OSD says the same on the two pages concerned: "Not available with PXE boot. Custom images require the full ISO or USB media." and "Actions that use imported files are not available with PXE boot. They require the full ISO or USB media."

As with an ISO, Foundry Deploy is downloaded when the device starts. The full comparison is in [What each media type carries](README.md#what-each-media-type-carries).

## Before you start

- A PXE server that can import and start a WIM boot image, and the right to add a boot image to it.
- Target devices of the same architecture as the ISO, `x64` or `arm64`.
- The network drivers the devices need in Windows PE, added in [General](../general.md) before the ISO is created.
- Internet access from Windows PE to the hosts in [Network endpoints](../../reference/network-endpoints.md).

## Prepare the boot image

1. [Create an ISO](create-iso.md) and start one test device or virtual machine from it.
2. Open the ISO in File Explorer and copy `sources\boot.wim`.
3. Import the copy as a boot image in your PXE server and publish it, following the documentation of that server.

Take the file from an ISO, not from a Foundry USB drive: the boot image of a USB drive does not contain Foundry Connect, which is stored on its cache partition.

## Check the result

1. Start a test device from the network.
2. Check that Foundry Connect opens and reports the network as ready, then that Foundry Deploy opens.
3. Run one complete deployment and the [post-deployment checks](../../foundry-deploy/verify-deployment.md).

## Keep the boot image up to date

Each time you create the ISO again, import its `sources\boot.wim` again. Keep the previous boot image on the PXE server until the new one has started a test device.

## Use imported content with a PXE start

If a deployment must use imported post-installation content, attach to the device the complete ISO the boot image was copied from, for example as virtual media, and leave it attached until the deployment ends. Foundry Deploy looks for the content on every drive of the device and accepts only the content of that same ISO. It does not download it from a web server or a network share.

Without that media, the deployment stops before the disk is erased, with "Post-installation preparation failed. Check the deployment log for details.". See [Media creation troubleshooting](../../troubleshooting/media-creation.md).

## Related

- [Create an ISO](create-iso.md)
- [Start: create deployment media](README.md)
- [Windows PE startup](../../foundry-connect/windows-pe-startup.md)
- [Media creation troubleshooting](../../troubleshooting/media-creation.md)
