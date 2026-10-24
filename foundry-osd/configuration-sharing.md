# Configuration sharing

Foundry OSD keeps one configuration for your Windows account on a PC: every option of its pages, with the passwords and files they need. The **Configuration sharing** card of **Settings** moves this configuration to another PC in an encrypted file, or keeps several PCs aligned on it through a shared folder.

| I want to ... | Go to |
| --- | --- |
| Know what Foundry OSD keeps between sessions | [Saved passwords and files](configuration-sharing/saved-passwords.md) |
| Copy the configuration to another PC once, or keep a backup | [Export and import](configuration-sharing/export-and-import.md) |
| Make this PC the source of the configuration that other PCs use | [Publish to a folder](configuration-sharing/publish-to-a-folder.md) |
| Use the configuration that another PC publishes | [Follow a folder](configuration-sharing/follow-a-folder.md) |
| Find my configurations and passwords from Settings backup and sync | [Move from Settings backup and sync](configuration-sharing/migration.md) |
| Understand a status or a message of the card | [Configuration sharing troubleshooting](../troubleshooting/foundry-osd/configuration-sharing.md) |

## The card

Open **Settings**, at the bottom of the navigation pane, and expand **Configuration sharing**. It is the third card.

- **Export configuration** and **Import configuration** are always offered.
- On a PC that neither publishes nor follows, the card also offers **Publish to a folder** and **Follow a folder**.
- On a PC that publishes or follows, the card shows its role, "This PC publishes its configuration" or "This PC follows \<PC name\>", the status, the **Folder**, the **Version** and the **Last success**, with the buttons of that role. A PC cannot publish and follow at the same time.

## What travels

| Content | Exported file | Publication in a folder |
| --- | --- | --- |
| Options of every configuration page, including Windows Autopilot JSON profiles | Yes | Yes |
| Answer files, Ethernet 802.1X and Wi-Fi profile templates and certificates, the Windows Autopilot PFX | Yes, embedded in the file, 16 MiB in total at most | Yes |
| Passwords | Only with **Include passwords** | Only with **Include passwords** |
| Post-installation packages, custom driver sets and custom Windows images | No. The import lists them under **Missing on this PC** | Yes. Followers download them in the background |
| ISO output path, Additional WinPE driver folder and telemetry settings | No | No |

The last row holds the per-PC values: each PC keeps its own through an import or a publication. The other cards of **Settings**, such as **Appearance** and **Proxy**, are not part of the configuration.

## Related

- [Settings](settings.md)
- [Security and credentials](../reference/security-and-credentials.md#configuration-sharing)
- [Configuration sharing troubleshooting](../troubleshooting/foundry-osd/configuration-sharing.md)
