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
