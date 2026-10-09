# Supported versions

This page lists what Foundry runs on and what it deploys. Foundry OSD checks most of these rules itself and reports the one that is not met.

## Support matrix

| Component | Supported | Notes |
| --- | --- | --- |
| Foundry OSD | The latest published release | Foundry OSD updates itself. See [Settings](../foundry-osd/settings.md#update-app). |
| Administrator workstation | Windows 10 version 1809 (build 17763) or later, and Windows 11, on x64 or ARM64 | One installer per architecture. See [Download and install](../start-here/download.md). |
| Windows ADK and Windows PE add-on | Version `10.1.26100.9457` or a later revision of `10.1.26100` | See [Windows ADK](#windows-adk) below. |
| Deployment media | x64 or ARM64 | The architecture is chosen in [General](../foundry-osd/general.md) and must match the target device. |
| USB drive | 16 GB or larger | Smaller drives are refused. |
| Windows installed from the catalog | Windows 11 24H2, 25H2 and 26H2 | 26H2 is proposed by default. Windows 10 is not offered. |
| Custom Windows images | Images you import yourself | See [Custom Windows images](../foundry-osd/customization/custom-windows-images.md). |

Microsoft ends servicing of each Windows 11 release at a different date for each edition. See [Windows 11 release information](https://learn.microsoft.com/en-us/windows/release-health/windows11-release-information).

## Windows 11 editions

| Edition | Licensing | Architectures |
| --- | --- | --- |
| Home, Home N, Home Single Language | Retail | x64, ARM64 |
| Home China | Retail | x64 |
| Pro, Pro N, Education, Education N | Retail or volume | x64, ARM64 |
| Enterprise, Enterprise N | Volume | x64, ARM64 |

What Foundry Deploy actually offers on a device also depends on the [catalog](catalog.md) content of the day and on the limits the administrator set in [OS selection](../foundry-osd/customization/operating-system.md).

## Windows ADK

Foundry OSD builds media with the Windows ADK for Windows 11, version 24H2, and its Windows PE add-on. It accepts an installation when all three conditions are true:

| Condition | Rule |
| --- | --- |
| ADK release | The version starts with `10.1.26100`. |
| ADK revision | The fourth number is `9457` or higher, for example `10.1.26100.9457`. |
| Windows PE add-on | Its version is exactly the version of the installed ADK. |

What Foundry OSD reports for other installations:

| Installed ADK | Reported as |
| --- | --- |
| `10.1.26100` with a revision below `9457`, or any earlier release | **ADK version is unsupported** |
| A release newer than `10.1.26100` | **ADK version is unsupported** |
| A supported ADK with an add-on of another version, or with missing files | **Windows PE Add-on needs repair** |

The [ADK](../foundry-osd/adk.md) page installs version `10.1.26100.9457` of both components and replaces an unsupported version for you.

## Related

- [Requirements](../start-here/requirements.md)
- [Network endpoints](network-endpoints.md)
- [Catalogs](catalog.md)
