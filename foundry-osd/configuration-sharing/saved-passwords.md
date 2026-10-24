# Saved passwords and files

Foundry OSD saves your configuration, its passwords and the files you select by itself, for your Windows account on this PC. There is no option to turn on: when you start Foundry OSD again, every page shows what you left.

## What is saved

| Item | Where, under `%LocalAppData%\Foundry` | Protection |
| --- | --- | --- |
| The options of every configuration page | `Configuration\foundry.config.json` | None: the file holds no password |
| The Deployment password, the Wi-Fi **Passphrase**, the **PFX password** of the Ethernet 802.1X, Wi-Fi and Windows Autopilot certificates, the passwords of the OOBE Administrator account and of the local accounts, and the passwords of the join accounts of zero-touch Domain Join | `Configuration\secrets.dat` | Encrypted with Windows DPAPI. Only your Windows account can read it; another account, or the file copied to another PC, cannot |
| A copy of each file you select: answer files, Ethernet 802.1X and Wi-Fi profile templates and certificates, the Windows Autopilot PFX | `Files\content` | None added by Foundry: the copies are stored as they are in your Windows profile |

A selected file is copied when you select it, so the original is no longer needed. A file larger than 4 MiB is refused with **File not added** and "The selected file could not be added to Foundry. Choose a readable file no larger than 4 MiB." The previous selection stays.

A certificate password belongs to its certificate file. When you select another certificate, type its password: the password of the previous file is not offered for it.

Post-installation packages, custom driver sets and custom Windows images are copied into their own libraries when you import them: see [Post-installation](../customization/post-installation.md), [Custom drivers](../customization/custom-drivers.md) and [Custom Windows images](../customization/custom-windows-images.md).

Never saved: the sign-in to your tenant on the Windows Autopilot pages. The proxy credentials of **Settings** are kept apart, in Windows Credential Manager: see [Settings](../settings.md#proxy).

## Use the configuration on another PC

The saved passwords and files open only for your Windows account on this PC. To use them on another PC or for another Windows account, [export and import](export-and-import.md) the configuration with **Include passwords**, or [publish it to a folder](publish-to-a-folder.md).

## When passwords cannot be restored

Windows can stop opening `secrets.dat`, for example after an administrator reset your Windows password. Foundry OSD then starts with empty password fields, and **Home** shows "Saved passwords could not be restored. Enter them again on the configuration pages."

1. Close the notice.
2. Open each page that shows **Needs attention** on **Start**, and type its passwords again.

Your options are saved normally meanwhile. The first password you type creates a new `secrets.dat`, and the notice no longer appears.

On a PC that publishes or follows a folder, the publication password is in the same file: see [Configuration sharing troubleshooting](../../troubleshooting/foundry-osd/configuration-sharing.md#passwords-needed).

## Related

- [Configuration sharing](../configuration-sharing.md)
- [Security and credentials](../../reference/security-and-credentials.md#saved-on-the-workstation)
