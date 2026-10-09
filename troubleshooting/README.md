# Troubleshooting

Start from the stage where the work stopped, or from what you see on screen. Each page of this section opens with an index of symptoms and quotes the messages exactly as Foundry shows them.

## By stage

| Stage | Where it happens | Go to |
| --- | --- | --- |
| Foundry OSD starts, updates, or saves and syncs settings | Workstation, Foundry OSD | [Foundry OSD application](foundry-osd.md) |
| Installing the ADK, creating an ISO, a USB drive or a PXE boot image | Workstation, Foundry OSD **Start** | [Media creation](media-creation.md) |
| The device starts from the media, up to the first Foundry window | Target device, Windows PE | [Windows PE startup](windows-pe-startup.md) |
| Getting an Ethernet or Wi-Fi connection | Target device, Foundry Connect | [Network and Foundry Connect](network.md) |
| Choosing the disk, Windows and drivers, then installing Windows | Target device, Foundry Deploy | [Windows deployment](deployment.md) |
| First start of Windows: post-installation console, activation | Target device, installed Windows | [After the restart](after-the-restart.md) |
| Autopilot profile or hardware hash upload | Foundry OSD, Foundry Deploy or installed Windows | [Windows Autopilot](autopilot.md) |
| Joining an Active Directory domain | Foundry OSD, Foundry Deploy or installed Windows | [Domain Join](domain-join.md) |
| Finding, exporting and sending logs | Any | [Logs and support information](logs-and-support.md) |

## By symptom

| I see this | Go to |
| --- | --- |
| **Start** in Foundry OSD does not let me create media | [Media creation](media-creation.md) |
| The device does not start from the USB drive, the ISO or PXE | [Windows PE startup](windows-pe-startup.md) |
| A console window stays open in Windows PE and no Foundry window appears | [Windows PE startup](windows-pe-startup.md) |
| Foundry Connect does not report the network as ready | [Network and Foundry Connect](network.md) |
| Foundry Deploy refuses the password, or closes as soon as it opens | [Windows deployment](deployment.md) |
| No disk is offered, or **Next** or **Deploy** stays unavailable | [Windows deployment](deployment.md) |
| **Deployment failed** with a "Failed step: ..." line | [Windows deployment](deployment.md) |
| After **Deployment complete**, the device returns to the media or does not start Windows | [Windows deployment](deployment.md) |
| The Foundry console after the restart shows a failed action or does not finish | [After the restart](after-the-restart.md) |
| The device is not registered in Windows Autopilot | [Windows Autopilot](autopilot.md) |
| The device did not join the domain, or is in the wrong OU | [Domain Join](domain-join.md) |
| "Diagnostics could not be exported. Check the log for details." | [Logs and support information](logs-and-support.md) |

## Before you change anything

A failed deployment keeps its evidence only until the device restarts or is deployed again. Record this first:

1. The application and the screen: Foundry OSD, Foundry Connect, Foundry Deploy, or the console shown after the restart.
2. The exact message. In Foundry Deploy, note the "Failed step: ..." line, select **View error details**, then **Copy**.
3. Whether **Prepare target disk** had already run. If it had, the disk was erased.
4. The device manufacturer and model, and how the device was started: USB drive, ISO or PXE.
5. The Foundry OSD version that created the media.

Then export the logs before you restart the device. In Foundry Connect and Foundry Deploy, use **Tools > Export diagnostics...**. The procedure for each application is in [Logs and support information](logs-and-support.md).
