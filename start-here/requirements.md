# Requirements

Foundry needs a Windows workstation to create the deployment media, the media itself, and a target device that can start from it. This page lists what each one must provide.

## Administrator workstation

| Requirement | Detail |
| --- | --- |
| Windows | Windows 10 or Windows 11, on an x64 or ARM64 processor. See [Supported versions](../reference/supported-versions.md). |
| Administrator rights | Foundry OSD always runs elevated. Windows shows a UAC prompt at every start, and an account that cannot approve it cannot open the app. |
| Free disk space | At least 20 GB free on the Windows system drive, and on the drive of the ISO file when you create an ISO. Foundry OSD checks this before it builds media. |
| Windows ADK and Windows PE add-on | A supported version of both. Foundry OSD installs them for you from its [ADK](../foundry-osd/adk.md) page; the accepted versions are in [Supported versions](../reference/supported-versions.md#windows-adk). |
| Microsoft runtimes | .NET 10 Desktop Runtime, Microsoft Edge WebView2 Runtime and Microsoft Visual C++ Redistributable 14.4. The installer is built to download and install the ones that are missing, so keep the workstation online during setup. |
| Internet access | To GitHub and Microsoft download sites, directly or through a proxy. See [Network endpoints](../reference/network-endpoints.md). |

## Deployment media

Choose one output, or both:

| Output | Requirement |
| --- | --- |
| USB drive | 16 GB or larger. Foundry OSD refuses a smaller drive. Creating the media erases the drive. |
| ISO file | A destination with at least 20 GB free. Use it for virtual machines, remote management consoles or [PXE](../foundry-osd/media/pxe-deployment.md). |

[Start: create deployment media](../foundry-osd/media/README.md) explains what each output carries.

## Target device

| Requirement | Detail |
| --- | --- |
| Architecture | x64 or ARM64, the same as the media. You choose the media architecture in [General](../foundry-osd/general.md). |
| Windows to install | Windows 11. The releases and editions offered are in [Supported versions](../reference/supported-versions.md); to install another image, see [Custom Windows images](../foundry-osd/customization/custom-windows-images.md). |
| Network | Internet access from Windows PE to the hosts in [Network endpoints](../reference/network-endpoints.md). Ethernet is the simplest choice; Wi-Fi works when you enable it in [Network](../foundry-osd/network/README.md) and Windows PE has a driver for the adapter. |
| Disk | A disk that can be erased. Foundry Deploy erases the disk the technician selects. |

## Optional features

Each feature has its own prerequisites, listed on its page:

- [Windows Autopilot](../foundry-osd/autopilot/README.md): a Microsoft Entra tenant with Microsoft Intune, and rights that depend on the method you choose.
- [Domain Join](../foundry-osd/domain-join/README.md): an Active Directory domain that the device can reach after the restart, and a join account allowed to join computers.
