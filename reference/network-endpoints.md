# Network endpoints

This page lists the hosts Foundry contacts, so that you can allow them on a firewall or a proxy. All connections are outbound.

Foundry OSD can use a proxy, set in [Settings](../foundry-osd/settings.md#proxy). That proxy is not passed to the deployment media: on the target device, Foundry Connect and Foundry Deploy connect without the proxy configured in Foundry OSD.

## Administrator workstation (Foundry OSD)

| Host | Port and protocol | Used for | Required |
| --- | --- | --- | --- |
| `github.com`, `api.github.com` | 443, HTTPS | Updates of Foundry OSD; download of Foundry Connect, Foundry Deploy and the post-installation components when media is created | Yes |
| `raw.githubusercontent.com` | 443, HTTPS | Catalog of Windows PE drivers and catalog of Windows releases | When the media uses Dell or HP drivers, Wi-Fi, or `arm64` |
| `go.microsoft.com` | 443, HTTPS | Download of the Windows ADK and Windows PE add-on installers | Until the ADK is installed |
| `dl.delivery.mp.microsoft.com` | 443, HTTPS | Windows package from which the boot image is built | When the media uses Wi-Fi or `arm64` |
| `downloads.dell.com` | 443, HTTPS | Dell drivers for Windows PE | When **Dell** is on in **Driver options** |
| `ftp.hp.com`, `ftp.ext.hp.com` | 443, HTTPS | HP drivers for Windows PE | When **HP** is on in **Driver options** |
| `catalog.s.download.windowsupdate.com` | 443, HTTPS | Intel Wi-Fi driver for Windows PE | When the media uses Wi-Fi |
| `login.microsoftonline.com`, `graph.microsoft.com` | 443, HTTPS | Sign-in to your tenant and Microsoft Graph requests from the Windows Autopilot pages | When you use Windows Autopilot |
| `eu.i.posthog.com` | 443, HTTPS | Telemetry and remote diagnostics | Optional. See [Telemetry and privacy](telemetry-and-privacy.md). |
| `docs.foundryosd.com` | 443, HTTPS | The **Documentation** buttons, opened in your browser | Optional |

GitHub and Microsoft redirect these downloads to other hosts of their own. GitHub serves release files from `*.githubusercontent.com`. If the ADK download is blocked although `go.microsoft.com` is allowed, allow the Microsoft download host named in your proxy log.

The installer of Foundry OSD downloads missing Microsoft runtimes during setup. See [Download and install](../start-here/download.md).

## Target device in Windows PE

| Host | Port and protocol | Used for | Required |
| --- | --- | --- | --- |
| `www.msftconnecttest.com`, `www.google.com` | 80, HTTP | Internet check by the Windows PE startup and Foundry Connect; clock correction | Yes, at least one of the two |
| `api.github.com`, `github.com`, `*.githubusercontent.com` | 443, HTTPS | Current version of Foundry Connect, Foundry Deploy and the post-installation components | Recommended. See the note below. |
| `raw.githubusercontent.com` | 443, HTTPS | Catalogs of Windows releases and driver packs, read each time Foundry Deploy starts | Yes |
| `dl.delivery.mp.microsoft.com` | 443, HTTPS | Windows image | Yes, unless a custom Windows image is deployed |
| `downloads.dell.com`, `ftp.hp.com`, `download.lenovo.com`, `download.microsoft.com` | 443, HTTPS | Driver packs for Dell, HP, Lenovo and Microsoft Surface | For the manufacturer of the driver pack selected |
| `www.catalog.update.microsoft.com`, `catalog.s.download.windowsupdate.com` | 443, HTTPS | Drivers from Microsoft Update Catalog and firmware updates | When that driver source or firmware updates are used |
| `login.microsoftonline.com`, `graph.microsoft.com` | 443, HTTPS | Zero-touch Windows Autopilot hardware hash upload | When that method is used |
| `time.now`, `ipapi.co`, `get.geojs.io` | 443, HTTPS | Time zone lookup from the public IP address | Optional. Windows PE uses UTC without them, or the time zone set in [General](../foundry-osd/general.md#windows-pe-time-zone). |
| `eu.i.posthog.com` | 443, HTTPS | Telemetry and remote diagnostics | Optional |

Note on GitHub: when it cannot be reached, the startup continues with the applications stored on the media. A deployment that needs post-installation components that are not on the media then stops before the disk is erased.

Microsoft Update Catalog returns the download address of each file. These addresses are on Windows Update download hosts such as `catalog.s.download.windowsupdate.com`.

## Target device after the restart

| Host | Port and protocol | Used for | Required |
| --- | --- | --- | --- |
| `login.microsoftonline.com`, `graph.microsoft.com` | 443, HTTPS | Interactive Windows Autopilot hardware hash upload | When that method is used |
| Your domain controllers and DNS servers | Active Directory ports of your network | Domain Join | When Domain Join is used |

For the interactive Windows Autopilot upload, the technician also opens `https://microsoft.com/devicelogin` on another device to sign in.

Foundry's post-installation step contacts no Foundry or telemetry host. Windows itself, Windows Autopilot enrollment and the software you install with post-installation actions have their own network requirements, which are documented by their publishers.

## Related

- [Requirements](../start-here/requirements.md)
- [Network](../foundry-osd/network/README.md)
- [Network and Foundry Connect troubleshooting](../troubleshooting/network.md)
