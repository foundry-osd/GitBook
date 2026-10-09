# Start: create deployment media

The **Start** page shows whether your configuration is ready, then creates an ISO file or a USB drive from it. Foundry OSD uses the options, passwords and files as they are when you select the button.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-media-01-readiness-overview.png`
- **Capture:** Show the whole **Start** page of a released build with the five readiness groups (**General**, **Network**, **Windows Autopilot**, **Domain Join**, **Customization**), one group expanded with a row marked **Needs attention** and its **Review** button, the **ISO output**, **USB target** and **Create media** cards, and the current navigation pane.
{% endhint %}

## Before you start

- The [ADK](../adk.md) page reports **ADK is ready**. Until then **Start** cannot be opened.
- 20 GB are free on the Windows drive. See [Limits](#limits).
- With several saved configurations, the one you want is selected in [Settings backup and sync](../deployment-profiles.md).

## Read the readiness rows

The bar at the top reads **Ready to create ISO and USB media**, **Ready to create ISO media**, **Ready to create USB media** or **Readiness items needing action:** with a number. It reads **Review media output warnings** when no row needs attention but neither a valid ISO path nor a usable USB drive is set.

Below it, five groups list one row per page of the app:

| Group | Rows | Fix on |
| --- | --- | --- |
| **General** | **Architecture**, **Secure Boot**, **WinPE boot language**, **Windows PE time zone**, **Automatic restart**, **Password protection**, **Driver options** | [General](../general.md) |
| **Network** | **Ethernet 802.1X**, **Wi-Fi** | [Network](../network/README.md) |
| **Windows Autopilot** | **JSON profile**, **Zero-Touch**, **Interactive** | [Windows Autopilot](../autopilot/README.md) |
| **Domain Join** | **Zero-Touch**, **Interactive** | [Domain Join](../domain-join/README.md) |
| **Customization** | **OS selection**, **Machine naming**, **OOBE**, **Custom Windows images**, **Post-installation**, **Unattend**, **Optional features**, **AppX removals**, **AI components** | [Customization](../customization/README.md) |

Each row shows its value and one state: **Configured**, **Default**, **Disabled**, **Not configured**, **Not selected** or **Needs attention**.

**Needs attention** means something on that page is missing or not valid. The row shows the reason and a button, **Review** followed by the row name, that opens the page to fix. Its group opens by itself. Clear every such row before you create media.

## Choose what to create

| I want to | Go to |
| --- | --- |
| Start virtual machines, or attach media through a remote management console | [Create an ISO](create-iso.md) |
| Deploy physical devices, and reuse downloads between deployments | [Create a USB drive](create-usb.md) |
| Apply new options to a Foundry USB drive and keep its downloads | [Update a USB drive](update-usb.md) |
| Start devices from an existing PXE server | [Deploy with PXE](pxe-deployment.md) |

## What each media type carries

| Content | ISO | USB drive | PXE boot image |
| --- | --- | --- | --- |
| Boot image `sources\boot.wim`: Windows PE, its drivers and your options | In the ISO | On the **BOOT** partition | The only file delivered |
| Foundry applications | Foundry Connect is in the boot image. Foundry Deploy is downloaded at each start. | Foundry Connect is in `Runtime\` on the cache partition. Foundry Deploy is downloaded at start and kept there. | As for the ISO |
| [Custom images](../customization/custom-windows-images.md) | In the ISO, in `Cache\OperatingSystems\Custom\` | Same folder on the cache partition | Not delivered |
| [Post-installation](../customization/post-installation.md) content you imported | In the ISO, in `Cache\PreOobe\` | Same folder on the cache partition | Not delivered |
| Cache of downloaded Windows images and driver packs | None: every deployment downloads again | On the cache partition, kept by updates | None |

A USB drive has two partitions:

| Partition | Format | Holds |
| --- | --- | --- |
| **BOOT** | FAT32, 2 GiB | Boot files and `sources\boot.wim` |
| **Foundry Cache** | NTFS, rest of the drive | `Runtime\`, `Cache\OperatingSystems\`, `Cache\DriverPacks\`, `Cache\Firmware\`, `Cache\PreOobe\`, `Logs\` |

## While media is being created

An **Operation in progress** dialog shows each stage, and the rest of the app is locked until it closes. Its title changes to **Operation complete** with the result, for example "The ISO media was created successfully.".

**Cancel** stops the operation. Downloads stop at once; a disk or image operation already started finishes first, so keep Foundry OSD open and the USB drive connected until the dialog reads "Media creation cancelled." A cancelled or failed ISO build keeps the previous file. A cancelled USB operation does not restore the drive: create or update it again.

## Limits

- 20 GB free on the drives that hold `%ProgramData%`, your user profile and the Windows temporary folders, and on the drive of the ISO file.
- A USB drive of 16 GB or more.
- A custom driver folder of at most 2 GiB and 10,000 files and folders.
- Boot files of at most 2 GiB on a USB drive. An ISO has no such limit.

## Related

- [General](../general.md)
- [Settings backup and sync](../deployment-profiles.md)
- [Media creation troubleshooting](../../troubleshooting/media-creation.md)
