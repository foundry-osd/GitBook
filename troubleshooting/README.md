# Troubleshooting

Start from the stage where the work stopped, or from what you see on screen. Each page of this section opens with an index of symptoms and quotes the messages exactly as Foundry shows them.

## By stage

| Stage | Where it happens | Go to |
| --- | --- | --- |
| Foundry OSD starts, installs the ADK, updates, or saves and syncs settings | Workstation, Foundry OSD | [Foundry OSD application](foundry-osd.md) and its page [Settings backup and sync](foundry-osd/settings-backup-and-sync.md) |
| Creating an ISO or a USB drive, and starting a device from it or from a PXE server | Workstation, Foundry OSD **Start**, then the target device | [Media creation](media-creation.md) and its page [USB drive and device start](media-creation/usb-drive-and-device-start.md) |
| The device starts from the media, up to the first Foundry window | Target device, Windows PE | [Windows PE startup](windows-pe-startup.md) |
| Getting an Ethernet or Wi-Fi connection | Target device, Foundry Connect | [Network and Foundry Connect](network.md) |
| Choosing the disk, Windows and drivers, then installing Windows | Target device, Foundry Deploy | [Windows deployment](deployment.md), in four pages: [before the deployment starts](deployment/before-deployment-starts.md), [checks and image download](deployment/checks-and-image-download.md), [after the disk is erased](deployment/after-disk-erase.md), [Windows does not start after deployment](deployment/device-does-not-start.md) |
| First start of Windows: post-installation console, activation | Target device, installed Windows | [After the restart](after-the-restart.md) |
| Autopilot profile or hardware hash upload | Foundry OSD, Foundry Deploy or installed Windows | [Windows Autopilot](autopilot.md) for Foundry OSD, then [during deployment](autopilot/during-deployment.md) and [after the restart](autopilot/after-the-restart.md) |
| Joining an Active Directory domain | Foundry OSD, Foundry Deploy or installed Windows | [Domain Join](domain-join.md) |
| Finding, exporting and sending logs | Any | [Logs and support information](logs-and-support.md) |

## By symptom

| I see this | Go to |
| --- | --- |
| Foundry OSD does not open, its pages are grayed out, or an update fails | [Foundry OSD application](foundry-osd.md) |
| A message on a Customization page of Foundry OSD | The "If something goes wrong" section of that page: [Custom Windows images](../foundry-osd/customization/custom-windows-images.md#if-something-goes-wrong), [Unattend](../foundry-osd/customization/unattend.md#if-something-goes-wrong), [Machine naming](../foundry-osd/customization/machine-naming.md#if-something-goes-wrong), [Post-installation](../foundry-osd/customization/post-installation.md#if-something-goes-wrong) |
| A message under the **Settings backup and sync** card, or a configuration that does not synchronize | [Settings backup and sync](foundry-osd/settings-backup-and-sync.md) |
| **Start** in Foundry OSD does not let me create media, or the creation fails | [Media creation](media-creation.md) |
| The USB drive is not listed or cannot be written, or "Custom Windows image media preparation failed." | [USB drive and device start](media-creation/usb-drive-and-device-start.md) |
| The device does not start from the USB drive, the ISO or PXE | [The device does not start from the media](media-creation/usb-drive-and-device-start.md#does-not-boot) |
| A console window stays open in Windows PE and no Foundry window appears | [Windows PE startup](windows-pe-startup.md) |
| Foundry Connect does not report the network as ready | [Network and Foundry Connect](network.md) |
| Foundry Deploy refuses the password, or closes as soon as it opens | [Before the deployment starts](deployment/before-deployment-starts.md) |
| No disk is offered, a message under **Answer file**, or **Next** or **Deploy** stays unavailable | [Before the deployment starts](deployment/before-deployment-starts.md) |
| **Deployment failed** with a "Failed step: ..." line | [Windows deployment](deployment.md), which sends you to the page for that step |
| After **Deployment complete**, the device returns to the media or does not start Windows | [Windows does not start after deployment](deployment/device-does-not-start.md) |
| The Foundry console after the restart shows a failed action or does not finish, or Windows is not activated | [After the restart](after-the-restart.md) |
| The device is not registered in Windows Autopilot | [Windows Autopilot](autopilot.md) |
| The device did not join the domain, or is in the wrong OU | [Domain Join](domain-join.md) |
| "Diagnostics could not be exported. Check the log for details." | On the target device: [Logs and support information](logs-and-support.md#export-failed). In Foundry OSD: [Diagnostics export failed](foundry-osd.md#export-failed) |

## Before you change anything

A failed deployment keeps its evidence only until the device restarts or is deployed again. Record this first:

1. The application and the screen: Foundry OSD, Foundry Connect, Foundry Deploy, or the console shown after the restart.
2. The exact message. In Foundry Deploy, note the "Failed step: ..." line, select **View error details**, then **Copy**.
3. Where the failed step sits in **Steps**. Above **Prepare target disk**, the disk was not erased; below it, the disk was erased. If **Prepare target disk** is the failed step, the disk is intact unless the message starts with "Disk partitioning failed".
4. The device manufacturer and model, and how the device was started: USB drive, ISO or PXE.
5. The Foundry OSD version that created the media.

Then export the logs before you restart the device. In Foundry Connect and Foundry Deploy, use **Tools > Export diagnostics...**. The procedure for each application is in [Logs and support information](logs-and-support.md).
