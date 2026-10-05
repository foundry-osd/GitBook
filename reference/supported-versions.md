# Supported versions

## Application and boot media updates

Updating Foundry OSD does not rewrite media already distributed. [Bootstrap](bootstrap.md#cache-and-connectivity) can obtain newer release runtimes during startup, so existing media may run a newer Deploy application without being rebuilt. Check the running application version; an older or debug-provisioned runtime does not gain release support from a catalog update alone.

Recreate ISO media or [update an existing USB drive](../foundry-osd/media/update-usb.md) when you need to refresh embedded Bootstrap, configuration, or bundled assets. Test runtime and media updates before production use.

## Administrator workstation

- Windows 10 or Windows 11.
- Windows ADK `10.1.26100.2454`.
- Windows PE Add-on `10.1.26100.2454`.

{% hint style="warning" %}
Do not substitute another ADK or Windows PE Add-on version. Use `10.1.26100.2454` for both components.
{% endhint %}

## Windows deployment media

Available Windows releases, languages, editions, architectures, and license channels depend on the current [operating-system catalog](catalog.md), the running Deploy version, and any restrictions configured during media authoring. The catalog can update independently of the application.

{% hint style="info" %}
**Supported Windows releases**

Foundry supports Windows 11 24H2, 25H2, and 26H2 for catalog deployments, with 26H2 as the default. Microsoft's servicing dates differ by edition, as listed in [Windows 11 release information](https://learn.microsoft.com/en-us/windows/release-health/windows11-release-information).

Existing media needs a Deploy runtime that supports 26H2 to select it. When an older profile allows only releases that are no longer available, Deploy automatically falls back to supported catalog releases for the deployment architecture. If any allowed release remains available, that restriction stays in effect. See [Select Windows](../foundry-deploy/operating-system.md).
{% endhint %}

## Hardware

Network, storage, and platform support depends on Windows PE compatibility and available driver packages. Validate deployment media on representative hardware before production use.

## Custom WIMs

The [custom image workflow](../foundry-osd/customization/custom-windows-images.md) accepts readable WIM metadata without a Windows version, edition, or architecture allowlist. This is not a support guarantee for every image. Deployment tools, firmware, drivers, and selected customizations still have their own requirements.

## PostInstall runtime compatibility

[Post-installation](../foundry-osd/customization/post-installation.md) supports x64 and ARM64 targets. Bootstrap prepares PostInstall for the boot media architecture; you do not need to install .NET on the target. Use boot media matching the target Windows architecture. Deploy checks runtime compatibility before preparing the disk.

Keep deployment media and its package content together, and choose scripts and installers compatible with the target Windows image and architecture. Test the complete workflow on representative hardware before rollout.

## Domain Join

[Domain Join](../foundry-osd/domain-join/README.md) supports eligible Windows editions on x64 and ARM64 targets.

Use matching media/target architecture and validate the complete workflow before rollout.

Known unsupported edition IDs are `Core`, `CoreN`, `CoreSingleLanguage` and `CoreCountrySpecific` (Home family). They skip joining with a warning while Windows installation continues. Recognized eligible IDs are `Professional`, `ProfessionalN`, `Education`, `EducationN`, `Enterprise` and `EnterpriseN`; eligibility alone does not prove successful joining. Missing/unfamiliar edition IDs remain Unknown pending image/native inspection.

Installed Windows needs online access to domain DNS and a writable controller, along with administrator-delegated join/reuse/read/placement permissions. Preparing an OU list offline does not provide offline joining. Custom answer files and applied images must meet [domain composition/name requirements](../foundry-deploy/domain-join.md#review-the-target-and-windows).
