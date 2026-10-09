# Catalogs

Foundry does not carry a list of Windows images or driver packs. It reads three catalog files from the public `foundry-osd/catalog` repository on GitHub each time it needs them, then downloads the files they point to from Microsoft or from the manufacturer.

## The three catalogs

| Catalog | Read by | When | Lists |
| --- | --- | --- | --- |
| Windows images | Foundry Deploy | When it starts | Windows 11 images published by Microsoft |
| Windows images | Foundry OSD | When media uses Wi-Fi or `arm64` | The Windows 11 package used to build the boot image |
| Driver packs | Foundry Deploy | When it starts | Driver packs of Dell, HP, Lenovo and Microsoft Surface |
| Windows PE drivers | Foundry OSD | When media uses **Dell** or **HP** drivers, or Wi-Fi | Windows PE driver sets of Dell and HP, and one Intel Wi-Fi driver |

## Where the catalogs come from

| Fact | Value |
| --- | --- |
| Host | `raw.githubusercontent.com`, over HTTPS |
| Refresh | Once a day, without a Foundry update |
| Copy on the media or the workstation | None. The catalog is downloaded each time. |
| Proxy | Foundry OSD uses the proxy set in [Settings](../foundry-osd/settings.md). That proxy is not used on the target device. |

Without access to the host, Foundry Deploy cannot list Windows images or driver packs, and Foundry OSD cannot build media that needs a catalog. The full list of hosts, including those of the downloads, is in [Network endpoints](network-endpoints.md).

## Windows image catalog

| Field | Content |
| --- | --- |
| Release and build | Windows 11 24H2, 25H2 or 26H2, with the build number |
| Architecture | x64 or ARM64 |
| Language and edition | One entry for each language and edition |
| License channel | Retail (`RET`) or volume (`VOL`) |
| File name and size | The `.esd` file that Foundry Deploy downloads |
| Hash | SHA-256 for 25H2 and 26H2, SHA-1 for 24H2. Foundry Deploy checks the download against it. |
| Address | A download address on a Microsoft server |

26H2 follows the media Microsoft currently publishes. 25H2 and 24H2 stay at the last builds recorded in the catalog. Foundry Deploy offers only the releases it supports, even if the catalog lists others: see [Supported versions](supported-versions.md) and [Select Windows](../foundry-deploy/operating-system.md).

## Driver catalogs

| Field | Content |
| --- | --- |
| Manufacturer and models | The manufacturer, the model names and the system identifiers a pack applies to |
| Windows target | The Windows release and architecture the pack is made for |
| Package | Version, file name, format, size and download address on the manufacturer's server |
| Role | A complete driver pack, or a supplement such as the Intel Wi-Fi driver for Windows PE |
| Hash | SHA-256, when the manufacturer publishes one |

## What is not in the catalogs

- **Microsoft Update Catalog**, the other driver source of Foundry Deploy, is queried directly at Microsoft. See [Select a driver pack](../foundry-deploy/driver-pack.md).
- [Custom Windows images](../foundry-osd/customization/custom-windows-images.md) come from the media you created, not from a catalog.
- The custom driver folder of [General](../foundry-osd/general.md) is copied from the workstation.

## Related

- [Network endpoints](network-endpoints.md)
- [Supported versions](supported-versions.md)
- [Media creation troubleshooting](../troubleshooting/media-creation.md)
- [Windows deployment troubleshooting](../troubleshooting/deployment.md)
