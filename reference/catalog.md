# Catalogs

Foundry catalogs provide Windows media and driver-package metadata used during deployment.

## Operating-system catalog

Operating-system entries can include:

- Windows release and build.
- Architecture.
- Language and edition.
- License channel.
- Filename and size.
- SHA-256 hash.
- Direct ESD source URL.

{% hint style="info" %}
**Unreleased: Windows 11 catalog transition**

The updated catalog targets Windows 11 24H2, 25H2, and 26H2. Windows 11 26H2 uses Microsoft's current dynamic media source. Windows 11 25H2 remains available from archived catalog sources; the dynamic endpoint now supplies 26H2 and no longer refreshes 25H2 media.

Catalog availability is separate from application support. Deploy must recognize a release before it can offer that release, even if its media is already published in the catalog. Older media can use a newer runtime when [Bootstrap updates it](bootstrap.md#cache-and-connectivity); verify the application actually running before relying on 26H2 support.

See [Select Windows](../foundry-deploy/operating-system.md) for automatic fallback when none of the configured releases remains available.
{% endhint %}

## Driver catalogs

Unified driver entries can include:

- Stable item and package identifiers.
- Manufacturer, model, and system identifiers.
- Windows release, build, and architecture targeting.
- Package version, filename, format, size, and download URL.
- Package role, including base driver packs and supplements.
- Available SHA-256 values for driver packs, and SHA-1 or SHA-256 values for operating-system media.
- Legacy status.

Foundry currently aggregates supported vendor data into unified DriverPack and WinPE catalogs. Catalog content is updated independently from the application, so available items can change without an application update.

## Selection guidance

Prefer a non-legacy package that matches the detected manufacturer, model, Windows target, and architecture. Verify the displayed package information when more than one version is available.

## Custom image selection

[Custom Windows images](../foundry-osd/customization/custom-windows-images.md) use imported WIM metadata and exact numeric indexes independently of catalog entries. Import does not add an image to the public Foundry catalog or certify it as supported. Internet access remains a prerequisite.
