# Custom Windows images

Use **Windows customization > Custom Windows images** to import a Windows image, include it in deployment media, and choose the image source Foundry Deploy initially displays. Internet access remains required; Foundry Connect works as usual.

## Import an image

1. Select **Import image**.
2. Browse to a `.wim` or `.iso` file, review the source summary, and enter a name unique within the active profile (up to 200 characters). If the ISO contains both installation WIM and ESD files, choose which one to import.
3. For an ISO, choose whether to import available optional feature sources.
4. Select **Import image** and keep the source available until import completes. Use **Cancel** to stop an import.
5. Select the imported row to inspect its image indexes and metadata.

The first successful import enables custom images for the profile. The default source remains **Foundry catalog** until you change it.

Foundry copies WIM content into its local library. An ISO provides `sources\install.wim`, or `sources\install.esd`, which Foundry exports to WIM. Every image index is retained. Split `.swm` sets are not supported by this import flow.

Import checks that image metadata can be read. It does not certify that the image will deploy successfully or that your selected customizations suit it. Windows versions, editions, and architectures are not restricted to the Foundry catalog. Metadata reported by the image is shown as supplied; an unknown field is not replaced with a catalog default.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-custom-images-01-library.png`
- **Capture:** Show sanitized imported images with inclusion and preferred-image columns, and the source and numeric-index controls.
{% endhint %}

## Include images and choose defaults

The table distinguishes these actions:

| Control | Effect |
| --- | --- |
| Select a row | Shows its details and actions. |
| Toggle inclusion in profile | Adds or excludes the image from newly generated media. |
| Rename in profile | Changes this profile's label without renaming the original file. |
| Default image source in Deploy | Chooses Foundry catalog or custom Windows images as the initial workflow. |
| Preferred index and Set preferred image and index | Includes the selected image and records its preferred image/index. An index is its numeric WIM index, even when several indexes share an edition name. |
| Let the operator choose | Clears the preferred image/index. |
| Enable custom Windows images | Enables the custom workflow and packaging of included images for this profile. |

The source defaults to **Foundry catalog**. Enabling custom images does not force that source to change. You can also enable the custom workflow without including a managed image, then supply a WIM manually on USB.

An explicit preference that becomes unavailable requires attention. Foundry does not silently replace it with another image or the first index. Restore the source, change the preference, or clear it.

Reimporting the same WIM content restores a missing local copy. Its available optional feature sources can also be refreshed from the import. Reimport does not replace another profile's source choice.

## Remove a reference or delete local content

**Remove from profile** changes the active profile only. **Delete local copy** removes Foundry's local image copy; other profiles referencing that content then need the source restored. Original files and existing ISO/USB media are unchanged.

Content in use by a media build cannot be deleted. Deletion also reclaims companion source bundles when no remaining local image references them; shared bundles are retained. Removing or excluding a preferred image leaves a preference that requires attention until you explicitly change or clear it.

The local library is under `%LOCALAPPDATA%\Foundry\Images\Custom`. Profile export and synchronization transfer image references and defaults, not WIM bytes or optional source bundles. On another PC, import the same source content to satisfy those references. See [Deployment profiles](../deployment-profiles.md).

## Find images on generated media

Included WIMs are stored outside `boot.wim`:

| Output | Location |
| --- | --- |
| ISO | `Foundry\Images\Custom\managed\<content hash>\image.wim` on the ISO filesystem |
| USB | The same path on the NTFS data/cache partition |

Optional feature sources and the manifest binding the media to its images are kept beside this managed content. WIMs are not stored on the USB FAT32 BOOT partition. Allow space for all included images and their source files.

For a manual USB image, enable the custom workflow when authoring, then place a regular `.wim` file directly in `Foundry\Images\Custom\` on the data partition. Deploy does not recursively scan subfolders. Manual files are identified in the current session and are not used as substitutes for a missing managed default.

[USB updates](../media/update-usb.md) preserve manual files and unrelated cache data. New managed content is staged before the boot partition is refreshed. Old managed content may remain on the data partition and is not offered unless the current media manifest references it.

Copying only `sources\boot.wim` for [PXE](../media/pxe-deployment.md) does not carry these external images. This feature does not add a PXE or network-share image delivery mechanism.

## Deploy and validate

In [Foundry Deploy](../../foundry-deploy/operating-system.md), choose the custom source, image, and exact index. Enabled customizations still apply. Optional feature sources imported from an ISO are made available during servicing; Foundry does not preflight their compatibility with each feature or image.

Before destructive deployment starts, Foundry verifies source identity and metadata while holding read locks, and protects the physical source disk from target selection. Keep USB or ISO media available throughout deployment.

Hash and metadata checks detect changed or missing sources. They do not prove that every compressed WIM resource is intact. DISM can still fail while applying or servicing an image, after target preparation has begun. Validate your images and customizations on representative hardware before production deployment.
