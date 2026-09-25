# Select a driver pack

The Driver pack step selects hardware drivers compatible with the target device and Windows selection.

## Make a selection

1. Choose **None** when Windows inbox drivers or separately managed drivers are sufficient.
2. Choose **Microsoft Update Catalog** when the deployment should obtain applicable device drivers from that source.
3. Choose an available manufacturer catalog when the device requires a supported OEM driver pack.
4. For a manufacturer catalog, confirm the detected or selected model and choose the appropriate package version.
5. Review package details before continuing.

<figure>
  <img src="../.gitbook/assets/foundry-deploy-driver-pack-01-selection.png" alt="Foundry Deploy driver pack source, model, and version selection">
  <figcaption>Select the driver pack that matches the target hardware and Windows selection.</figcaption>
</figure>

Foundry uses catalog metadata to identify supported models, operating-system targets, architecture, package format, and available hashes. If no suitable pack appears, see [Catalogs](../reference/catalog.md) and [Windows deployment troubleshooting](../troubleshooting/deployment.md).

For [custom Windows images](operating-system.md#custom-images), recognized Windows client builds use the same driver release preference and fallback rules as catalog images. For example, a Windows 11 24H2 image (build 26100) prefers a matching 24H2 OEM pack, and Microsoft Update Catalog searches start with 24H2. Hardware and architecture matching still apply. Server images and unidentified builds are not assigned a Windows client release.

## Driver downloads and storage

When you select **Microsoft Update Catalog**, Foundry uses `Cache/MicrosoftUpdateCatalog/Drivers` on USB media when the cache is writable and has enough space for the download size reported by the catalog. Otherwise, it uses the target disk. ISO deployments use the target disk. Drivers are extracted on the target disk, and only drivers selected for the current deployment are installed.

Foundry checks downloaded and cached driver files against the catalog hash when one is supplied. Drivers without a catalog hash are downloaded again for each deployment to temporary storage on the prepared target disk. Keep the device connected to the network and leave enough space for driver downloads and extraction.

## Driver installation paths

- **None:** driver download, extraction, and installation steps are omitted.
- **Offline driver packages:** **Download driver pack**, **Extract driver pack**, and **Install Windows drivers** prepare and inject INF drivers. **Install recovery drivers** also services Windows Recovery Environment when applicable.
- **Deferred installers:** supported packages such as Lenovo executable installers and Surface MSI packages use **Stage driver installer** to copy the package, followed by **Prepare setup tasks** to schedule installation. Installation occurs during Windows setup after reboot. Extraction and offline INF installation steps are omitted for this path.

A download can be **Skipped** because all selected files were reused from cache while extraction and installation still succeed normally. Microsoft Update Catalog lookup still requires network access; cached package files do not provide an offline copy of the catalog.
