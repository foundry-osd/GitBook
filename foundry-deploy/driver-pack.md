# Select a driver pack

On **Drivers**, the third step of the wizard, you choose where Foundry Deploy gets the drivers it adds to Windows for this device.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-deploy-driver-pack-01-selection.png`
- **Capture:** Show the **Drivers** step of a release build with **Driver source** set to a manufacturer and the **Model** and **Version** fields filled in.
{% endhint %}

## Choose a driver source

1. Check the pre-selected **Driver source** against the table below and change it if needed.
2. For a manufacturer source, check **Model**. It must name the device in front of you.
3. In **Version**, keep the pre-selected pack version unless you were told to use another one.
4. Select **Next**.

| Driver source | What you get | Pre-selected when |
| --- | --- | --- |
| **None** | No drivers are added. Windows uses the drivers it already contains. | The device is a virtual machine |
| **Microsoft Update Catalog** | Drivers for the storage controllers, disks and network adapters detected on this device. Nothing else: no graphics, audio or chipset drivers. | The device model has no pack in the manufacturer catalogs |
| **Dell**, **Lenovo**, **HP**, **Microsoft** | The manufacturer's driver pack for the model and Windows version you select. **Microsoft** covers Surface devices. | The device model has a pack in that catalog |

**Model** and **Version** appear only for a manufacturer source.

{% hint style="warning" %}
When Foundry pre-selects a manufacturer, the model matches the device. When you switch to a manufacturer source yourself and the device is not in its catalog, **Model** shows the first entry of the list, which is another machine. Select the right model, or go back to **Microsoft Update Catalog**.
{% endhint %}

## What the deployment does with your choice

| Driver source | During deployment |
| --- | --- |
| **None** | No driver step runs. |
| **Microsoft Update Catalog** | **Download driver pack**, then **Extract driver pack**, **Install Windows drivers** and **Install recovery drivers**. The drivers are in Windows before the restart. |
| A pack Foundry can unpack: Dell, HP, and any `.cab` or `.zip` pack | The same four steps. The drivers are in Windows before the restart. |
| A pack that is an installer: Lenovo `.exe`, Surface `.msi` | **Download driver pack**, then **Stage driver installer**. The installer runs in Windows after the restart, before your organization's post-installation actions. |

The search and the download need the network. Manufacturer packs are large, so allow time for the download. A pack already present in the [cache of the USB drive](../foundry-osd/media/README.md#what-each-media-type-carries), where Foundry keeps downloaded images and packs between deployments, is verified and reused instead of downloaded.

If Microsoft Update Catalog cannot be reached, or returns no driver for the device, **Download driver pack** is skipped and the deployment continues without those drivers. The step shows the reason, for example "Microsoft Update Catalog is not reachable; skipping driver lookup." Check the step before you hand over the device: without a storage or network driver, Windows may not start or may have no network.

For a custom image, Foundry matches packs to the Windows release of the image build, as it does for a catalog image.

## If something stops you

- The summary shows "\<manufacturer>: no matching model or version" and **Deploy** stays unavailable.
- **Download driver pack**, **Extract driver pack** or **Stage driver installer** fails.

The first is covered in [Before the deployment starts](../troubleshooting/deployment/before-deployment-starts.md), the second in [After the disk is erased](../troubleshooting/deployment/after-disk-erase.md). The catalogs themselves are described in [Catalogs](../reference/catalog.md).

Next: the [Windows Autopilot step](autopilot.md) or the [Domain Join step](domain-join.md) when the wizard shows one, otherwise [Review and deploy](review-and-deploy.md).
