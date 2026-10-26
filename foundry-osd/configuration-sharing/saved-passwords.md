# Saved passwords and files

Foundry OSD saves your configuration, its passwords and the files you select by itself, for your Windows account on this PC. There is no option to turn on: when you start Foundry OSD again, every page shows what you left.

## What is saved

| Item | How it is kept |
| --- | --- |
| The options of every configuration page | Saved for your Windows account on this PC. |
| The Deployment password, the Wi-Fi **Passphrase**, the **PFX password** of the Ethernet 802.1X, Wi-Fi and Windows Autopilot certificates, the passwords of the OOBE Administrator account and of the local accounts, and the passwords of the join accounts of zero-touch Domain Join | Encrypted. Only your Windows account on this PC can read them; another account, or a copy of your files on another PC, cannot. |
| A copy of each file you select: answer files, Ethernet 802.1X and Wi-Fi profile templates and certificates, the Windows Autopilot PFX | Kept as they are in your Windows profile, under `%LocalAppData%\Foundry`. They are not encrypted by Foundry OSD. |

A selected file is copied when you select it, so the original is no longer needed. A file larger than 4 MiB is refused with **File not added** and "The selected file could not be added to Foundry. Choose a readable file no larger than 4 MiB." The previous selection stays.

A certificate password belongs to its certificate file. When you select another certificate, type its password: the password of the previous file is not offered for it.

Post-installation packages, custom driver sets and custom Windows images are copied into their own libraries when you import them: see [Post-installation](../customization/post-installation.md), [Custom drivers](../customization/custom-drivers.md) and [Custom Windows images](../customization/custom-windows-images.md).

Never saved: the sign-in to your tenant on the Windows Autopilot pages. The proxy credentials of **Settings** are kept apart, in Windows Credential Manager: see [Settings](../settings.md#proxy).

## Use the configuration on another PC

The saved passwords open only for your Windows account on this PC. To use them on another PC or for another Windows account, [export and import](export-and-import.md) the configuration with **Include passwords**, or [publish it to a folder](publish-to-a-folder.md).

## When passwords cannot be restored

Windows can stop opening the saved passwords, for example after an administrator reset your Windows password. Foundry OSD then starts with empty password fields, and a dialog titled **Check your configuration** reads "Saved passwords could not be restored. Enter them again on the configuration pages."

1. Select **Close**.
2. Open each page that shows **Needs attention** on **Start**, and type its passwords again.

Your options are saved normally meanwhile. The dialog opens at each start until you type a password again.

On a PC that publishes or follows a folder, the publication password is lost too: see [Configuration sharing troubleshooting](../../troubleshooting/foundry-osd/configuration-sharing.md#passwords-needed).

## Related

- [Configuration sharing](../configuration-sharing.md)
- [Security and credentials](../../reference/security-and-credentials.md#saved-on-the-workstation)
