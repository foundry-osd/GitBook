# Supported versions

## Application and boot media updates

Updating Foundry OSD does not update media already distributed. Recreate ISO media or [update an existing USB drive](../foundry-osd/media/update-usb.md) to include new configuration and runtime assets. Test the refreshed media before production use.

## Administrator workstation

- Windows 10 or Windows 11.
- Windows ADK `10.1.26100.2454`.
- Windows PE Add-on `10.1.26100.2454`.

{% hint style="warning" %}
Do not substitute another ADK or Windows PE Add-on version. Use `10.1.26100.2454` for both components.
{% endhint %}

## Windows deployment media

Available Windows releases, languages, editions, architectures, and license channels come from the current [operating-system catalog](catalog.md) and any restrictions configured during media authoring. The catalog can update independently of the application.

## Hardware

Network, storage, and platform support depends on Windows PE compatibility and available driver packages. Validate deployment media on representative hardware before production use.

## Custom WIMs

The [custom image workflow](../foundry-osd/customization/custom-windows-images.md) accepts readable WIM metadata without a Windows version, edition, or architecture allowlist. This is not a support guarantee for every image. Deployment tools, firmware, drivers, and selected customizations still have their own requirements.

## PostInstall runtime pairing (unreleased)

[Post-installation](../foundry-osd/customization/post-installation.md) uses x64 and ARM64 self-contained .NET runtime ZIPs paired with the Deploy archive. Deploy verifies the exact companion hash and contract. An incompatible cached runner is not accepted, and the target runner does not update itself during execution. Test supported Windows images and hardware before rollout.
