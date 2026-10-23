# Deploy with PXE

Create the files a PXE server needs to start devices from the network with Foundry. Foundry OSD writes them to a folder; it does not configure the PXE server, DHCP or TFTP, which stay the responsibility of their administrator.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-media-pxe-01-destination.png`
- **Capture:** Show the **Create PXE files** dialog over Start, with a destination folder typed and the **Not delivered with a PXE start** warning listing one custom Windows image.
{% endhint %}

## Before you start

- No row of [Start](README.md) is marked **Needs attention**.
- 20 GB are free on the drives listed in [Start](README.md#before-you-start). The destination folder only needs room for the four files, about the size of `boot.wim`.
- The architecture, the language and the drivers of Windows PE are set in [General](../general.md). The network drivers the devices need in Windows PE must be there.
- A PXE server that can start a UEFI device from a WIM boot image, and the right to add a boot image to it.
- Internet access from Windows PE to the hosts in [Network endpoints](../../reference/network-endpoints.md).

## Create the PXE files

1. Open **Start** and select **Create PXE files**.
2. In **Destination folder**, type the folder or select **Browse**. A network share written as `\\server\share\folder` works. The folder you used last time is filled in.
3. Read the warnings of the dialog, then select **Create**:
   - "This folder already contains a boot image." The four files below are replaced. Other files of the folder are kept.
   - **Not delivered with a PXE start** lists the [imported content](#what-a-pxe-start-delivers) of your configuration that devices started from the network will not have.
4. If a dialog titled **Update Foundry OSD before creating boot media** opens, apply the update first, or select **Create anyway**.
5. Wait until the dialog is titled **PXE files created** and reads "The PXE boot files were saved to:", followed by the folder and the total size. Select **Open folder** to open it, or **Close**.

| File | Use |
| --- | --- |
| `boot.wim` | The boot image: Windows PE, its drivers, Foundry Connect and your options, built like the boot image of an ISO. Import this file in Windows Deployment Services or Configuration Manager. |
| `boot.sdi` | The RAM disk image that Windows PE starts from. |
| `BCD` | The UEFI boot configuration that loads `boot.wim`. |
| `bootmgr.efi` | The UEFI boot manager, signed for the **Secure Boot** choice of [General](../general.md). |

PXE servers that start Windows PE with wimboot, such as iPXE or FOG, use all four files. The files start UEFI devices only.

Foundry OSD replaces the files only once all four are written: a failed or cancelled creation keeps the previous ones.

## What a PXE start delivers

| Delivered | Not delivered |
| --- | --- |
| Windows PE with its drivers, and Foundry Connect. As with an ISO, Foundry Deploy is downloaded when the device starts. | [Custom Windows images](../customization/custom-windows-images.md) and [custom driver sets](../customization/custom-drivers.md) |
| Every option of your configuration: network, Windows Autopilot, Domain Join, computer name, OOBE, answer file, optional features, AppX removals, AI components | [Post-installation](../customization/post-installation.md) actions that use imported files: every **PowerShell script (.ps1)** and **Software (.exe/.msi)** action, and a **Command line** action with imported content |
| Post-installation actions that need no imported file: **Command line** without content and **Restart Windows** | A cache: each deployment downloads Windows and the driver pack again |

A deployment that needs a post-installation action with imported files stops before the disk is erased. Start those deployments from an [ISO](create-iso.md) or a [USB drive](create-usb.md), or disable the actions in Foundry OSD and create the PXE files again. See [PXE start](../../troubleshooting/media-creation/usb-drive-and-device-start.md#pxe).

## Check the result

1. Import the files in your PXE server, following the documentation of that server.
2. Start a test device from the network.
3. Check that Foundry Connect opens and reports the network as ready, then that Foundry Deploy opens.
4. Run one complete deployment and the [post-deployment checks](../../foundry-deploy/verify-deployment.md).

## Keep the boot image up to date

Each time you change the configuration, create the PXE files again and import them again. Keep the previous boot image on the PXE server until the new one has started a test device.

## Related

- [Start: create deployment media](README.md)
- [Create an ISO](create-iso.md)
- [Windows PE startup](../../foundry-connect/windows-pe-startup.md)
- [USB drive and device start troubleshooting](../../troubleshooting/media-creation/usb-drive-and-device-start.md)
