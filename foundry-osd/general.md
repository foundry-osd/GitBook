# General

The General page sets the options every deployment media needs: processor architecture, boot signature, Windows PE language and time zone, what happens when a deployment ends, an optional password, and extra drivers for Windows PE. The defaults suit a first test; open the page at least once before you create media.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-general-01-overview.png`
- **Capture:** Show the whole **General** page with default values, the **Windows PE time zone** card set to **Automatic**, the cards collapsed, and the **Media creation** card with **Review and start** at the bottom.
{% endhint %}

## Before you start

The [ADK](adk.md) page must report **ADK is ready**. Until then **General** cannot be opened.

## Configure the media

1. In the navigation pane, under **General**, open **General**.
2. Set the options below. Each change is saved at once; there is no save button.
3. Select **Review and start** in the **Media creation** card to go to [Start](media/README.md).

| Option | What it does | Default |
| --- | --- | --- |
| **Architecture and signature** | Processor architecture of the media: `x64` or `arm64`. It must match the target devices. | `x64` |
| **Secure Boot** | Certificate that signs the boot files: **PCA 2023** when on, **PCA 2011** when off. | **PCA 2023** |
| **WinPE boot language** | Language of Windows PE, among the language packs installed with the Windows PE add-on. | Keeps your earlier choice. On first use: the language of Foundry OSD when its pack is installed, otherwise the first one available |
| **Windows PE time zone** | Time zone used in Windows PE. See below. | **Automatic** |
| **Automatic restart** | Restarts the device after a successful deployment. | On |
| **Restart delay** | Seconds before that restart, from 0 to 3600. 0 restarts at once. | 10 |
| **Password protection** | Asks the technician for a password before deployment. See below. | Off |
| **Driver options** | Adds the **Dell** or **HP** driver set for Windows PE to the boot image. | Both off |
| **Custom driver folder** | Folder of extra `.inf` drivers to add to Windows PE. | Empty |

### Secure Boot

Keep **PCA 2023** unless you have a reason to change it. Foundry does not check the firmware of your devices: whether a device accepts media signed with the 2023 certificate depends on its firmware, as described in Microsoft's [Secure Boot certificate guidance](https://aka.ms/getsecureboot). If a target device with Secure Boot turned on does not start the media, creating the media again with **PCA 2011** is a test worth making.

### Windows PE time zone

With **Automatic**, Foundry looks up the time zone on the target device, from the public IP address of the deployment network, once Foundry Connect has confirmed Internet access. If the lookup fails, Windows PE uses UTC. The services contacted are listed in [Network endpoints](../reference/network-endpoints.md).

Select a time zone to skip the lookup, for example when the public IP address of the site is located in another region. This option affects Windows PE only, not the installed Windows.

### Driver options

Windows PE only needs drivers for the network and storage hardware it must use. Turn on **Dell** or **HP** for devices of those manufacturers, and add a **Custom driver folder** for anything else.

- The folder must exist and contain `.inf` files, not packed installers.
- Its total size, subfolders included, is limited to 2 GiB.
- With `arm64`, a USB drive is always created with the GPT partition style.

## Password protection

Turn on **Password protection** and type the **Deployment password** twice. This is the password the technician types when Foundry Deploy starts; without it, Foundry Deploy does not open.

The password must have at least 8 characters; Foundry OSD recommends 12, or a passphrase. **Review and start** stays disabled until the two entries match.

| Data on the media | Protected by the Deployment password |
| --- | --- |
| Passwords of local Windows accounts set in OOBE | Yes |
| Windows Autopilot JSON profiles | Yes |
| Certificate and its password for the zero-touch Windows Autopilot upload | Yes |
| Custom answer files | Yes |
| Join accounts and passwords of [zero-touch Domain Join](domain-join/zero-touch.md) | Yes. This method requires Password protection. |
| Wi-Fi passwords, and certificates with their passwords for Wi-Fi and Ethernet 802.1X | No |

{% hint style="warning" %}
Network credentials are not protected because Foundry Connect uses them before the password is asked. Anyone who holds the media can read them. Password protection does not encrypt the whole ISO file or USB drive either.
{% endhint %}

Foundry OSD keeps the Deployment password only while it is open, unless **Remember passwords** is on in [Settings backup and sync](deployment-profiles.md). If the fields are empty when you come back, type the password again. If you lose the password of existing media, create the media again with a new one.

## Check the result

[Start](media/README.md) shows one row for each option of this page, from **Architecture** to **Driver options**, with the value you chose. None of them must be marked **Needs attention**.

## Related

- [Start: create deployment media](media/README.md)
- [Security and credentials](../reference/security-and-credentials.md)
- [Foundry OSD application troubleshooting](../troubleshooting/foundry-osd.md)
